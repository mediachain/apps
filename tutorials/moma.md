# MoMA 🎨 Mediachain

## Installing Mediachain
Follow the instructions [here]() to get your local Mediachain node up and running.

This tutorial assumes that you're running a local instance of `mcnode` and you're able to control it with `mcclient`.

## Download MoMA's collection
The MoMA collection dataset contains 129,241 artwork records and  15,002 artist records and is accessible via [https://github.com/MuseumofModernArt/collection](https://github.com/MuseumofModernArt/collection).

We're going to use the JSON dumps so we can easily process the data and generate schemas for it.

Download the dumps:
```
$ wget https://github.com/MuseumofModernArt/collection/raw/master/Artists.json
$ wget https://github.com/MuseumofModernArt/collection/raw/master/Artworks.json
```

## Preparing the data
The `Artists.json` and `Artworks.json` files each consist of one big JSON array, each member of which is an object. We want to turn that into newline-delimited JSON so we can easily generate a schema and publish the data to Mediachain.

Using [jq](https://stedolan.github.io/jq/), we can easily unpack the array: `jq -c '.[]' Artworks.json > Artworks.ndjson` - the `.[]` jq filter selects each member of the array and prints it, and the `-c` flag instructs it to use "compact" printing, which results in one object per line.

Run the command on both files:
```
$ jq -c '.[]' Artworks.json > Artworks.ndjson
$ jq -c '.[]' Artists.json > Artists.ndjson
```

## Generating, validating, and publishing schemas
Follow the instructions to install [schema-guru](https://github.com/mediachain/aleph/blob/master/docs/schema-generation.md), a tool that lets you automatically derive JSON schemas from a set of JSON instances. The [schema generation guide](https://github.com/mediachain/aleph/blob/master/docs/schema-generation.md) explains how the naming conventions work and the meaning of all the parameters.

Generate the schemas:
```
$ schema_guru schema --ndjson --no-length --vendor org.moma --name artwork --schemaver 1-0-0 --output org.moma-artwork-jsonschema-1-0-0.json Artworks.ndjson
$ schema_guru schema --ndjson --no-length --vendor org.moma --name artist --schemaver 1-0-0 --output org.moma-artist-jsonschema-1-0-0.json Artists.ndjson
```

You can use `mcclient` to validate a dataset using a schema. This is useful to make sure you are publishing data that conforms to your schema and eases data interoperability for everyone using Mediachain.

```
$ mcclient validate org.moma-artwork-jsonschema-1-0-0.json Artworks.ndjson
129024 statements validated successfully
```

Once you've generated a schema, you can publish it to mediachain with the `mcclient publishSchema` command:
```
$ mcclient publishSchema org.moma-artwork-jsonschema-1-0-0.json
```

This will result in output similar to the following:
```
Published schema with wki = schema:org.moma/artwork/jsonschema/1-0-0 to namespace mediachain.schemas
Object ID: QmXF4LR4QkuRVh3WQbB56seTX2aPm3Tz7b4Y8heoLAiTkk
Statement ID: 4XTTM1xnJ712RdLnpHUrLuS8PjTNo94k1DDGpn5ssQKx6D3zt:1478226364:72001
```

The `Object ID` above is used as the --schemaReference flag when publishing records.

Publish the artist schema as well:
```
$ mcclient publishSchema org.moma-artist-jsonschema-1-0-0.json
```

## Publishing MoMA data to Mediachain
Since we have everything we need ready to go, we can publish the MoMA collection to Mediachain!

```
$ mcclient publish --namespace museums.moma.artworks --idFilter .ObjectID --schemaReference QmdfuvXHnKtewFeunFBBDHrTsG5qyHBFCruWWh4xxXm9PA Artworks.ndjson
```

The `mcclient publish` command also accepts newline-delimited JSON, so we give it the same file we prepared earlier as the final argument.  If the argument is omitted, it will read from standard input.

The `--idFilter` flag is a jq filter that selects the `ObjectID` field from the input records.  Note the `.` preceding the field name; without it jq will give you an error. This produces a mediachain "WKI" (well-known-identifier), that can be used to look up the records in queries.

The `--namespace` flag is also required to give the records a "home" in the mediachain hierarchy.

The `--schemaReference` is the object ID of the schema we published earlier.

When you provide a schema reference to the `mcclient publish` command, your input objects are validated against it, and wrapped in a small outer object that contains a link to the schema in the `schema` field, with your record as the `data` field.  Wrapping objects in this way allows other users to fetch the schema for a record without needing to know it up front.

For example, this MoMA artist record:

```json
{
    "ArtistBio": "American, 1934\u20132015",
    "BeginDate": 1934,
    "ConstituentID": 67350,
    "DisplayName": "Howard Guttenplan",
    "EndDate": 2015,
    "Gender": null,
    "Nationality": "American",
    "ULAN": null,
    "Wiki QID": null
}
```

Would stored in mediachain as:

```
{
  "schema": {
    "/": "QmXciNhn4P6veuojsR6uRGHBu27pAztp5o1rtMkkmsTtUf"
  },
  "data": {
    "ArtistBio": "American, 1934\u20132015",
    "BeginDate": 1934,
    "ConstituentID": 67350,
    "DisplayName": "Howard Guttenplan",
    "EndDate": 2015,
    "Gender": null,
    "Nationality": "American",
    "ULAN": null,
    "Wiki QID": null
  }
}
```

Now lets publish the artist data:
```
$ mcclient publish --namespace museums.moma.artists --idFilter .ObjectID --schemaReference QmdfuvXHnKtewFeunFBBDHrTsG5qyHBFCruWWh4xxXm9PA Artists.ndjson
```

## Interacting with the data
Lets confirm that the data is really in our node!

```
mcclient query "SELECT COUNT(*) FROM museums.moma.*"
143974
```

Looks like there are 143,974 records in the MoMA namespace!

Lets look at one of the artwork records:
```
$ mcclient query "SELECT * FROM museums.moma.artworks LIMIT 1"
{
  "id": "4XTTM1xnJ712RdLnpHUrLuS8PjTNo94k1DDGpn5ssQKx6D3zt:1478227578:101900",
  "publisher": "4XTTM1xnJ712RdLnpHUrLuS8PjTNo94k1DDGpn5ssQKx6D3zt",
  "namespace": "museums.moma.artworks",
  "body": {
    "Body": {
      "Simple": {
        "object": "QmWwr4ZfeLJfbWNAuCQfefwo1aHtxC5yjyU8C5WG4DYrYe",
        "refs": [
          "2"
        ],
        "deps": [
          "QmXF4LR4QkuRVh3WQbB56seTX2aPm3Tz7b4Y8heoLAiTkk"
        ]
      }
    }
  },
  "timestamp": 1478227578,
  "signature": "2iB6dzdz2cOy2HC4CrMdcs4tmaBAyebzVpDDWgrewI9c90zdlI4MHLgRPAubx40mxlVIGHoy/NggVjY3Pj+UDg=="
}
```

The query returns a *statement*, which contains a unique identifier, your publisher ID, the namespace it is in, the timestamp, and your cryptographic signature. 

It also includes an reference to the actual MoMA artwork metadata: `QmWwr4ZfeLJfbWNAuCQfefwo1aHtxC5yjyU8C5WG4DYrYe`.

Lets expand the original object:
```
mcclient getData QmWwr4ZfeLJfbWNAuCQfefwo1aHtxC5yjyU8C5WG4DYrYe
{
  "schema": {
    "/": "QmXF4LR4QkuRVh3WQbB56seTX2aPm3Tz7b4Y8heoLAiTkk"
  },
  "data": {
    "AccessionNumber": "885.1996",
    "Artist": [
      "Otto Wagner"
    ],
    "ArtistBio": [
      "Austrian, 1841\u20131918"
    ],
    "BeginDate": [
      1841
    ],
    "Cataloged": "Y",
    "Classification": "Architecture",
    "ConstituentID": [
      6210
    ],
    "CreditLine": "Fractional and promised gift of Jo Carole and Ronald S. Lauder",
    "Date": "1896",
    "DateAcquired": "1996-04-09",
    "Department": "Architecture & Design",
    "Dimensions": "19 1/8 x 66 1/2\" (48.6 x 168.9 cm)",
    "EndDate": [
      1918
    ],
    "Gender": [
      "Male"
    ],
    "Height (cm)": 48.6,
    "Medium": "Ink and cut-and-pasted painted pages on paper",
    "Nationality": [
      "Austrian"
    ],
    "ObjectID": 2,
    "ThumbnailURL": "http://www.moma.org/media/W1siZiIsIjU5NDA1Il0sWyJwIiwiY29udmVydCIsIi1yZXNpemUgMzAweDMwMFx1MDAzZSJdXQ.jpg?sha=137b8455b1ec6167",
    "Title": "Ferdinandsbr\u00fccke Project, Vienna, Austria, Elevation, preliminary version",
    "URL": "http://www.moma.org/collection/works/2",
    "Width (cm)": 168.9
  }
}
```

There's the original object! You can interact with the rest of the data in the same way with MCQL, the Mediachain query language that is very similar to SQL. See the [README](https://github.com/mediachain/concat#basic-operations) for more query examples.

## Going public
See the instructions [here](https://github.com/mediachain/concat#going-public) to configure your NAT, register your node with the directory, and bring it online so anyone can interact with it.

## Conclusion
If you published a new dataset after following this tutorial, reach out to us on [Slack](http://slack.mediachain.io) so we can merge it into the Museum Node with the rest of the museum data!

Open an issue if you have any questions or problems!
