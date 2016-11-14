# Tate 🎨 Mediachain

Publishing data to Mediachain involves three parts:
- Downloading the data
- Processing the data into a Mediachain-friendly format
- Publishing the data using a Mediachain node

## Downloading the data
The [Tate collection metadata](https://github.com/tategallery/collection) can be downloaded using the following command:

`git clone git@github.com:tategallery/collection.git`

The metadata to import is in `.json` files under the `/artists` and `/artworks` folders (i.e. two data sets).

## Processing the data
In order to publish data to Mediachain we need to create:
- a [self describing JSON schema](https://github.com/snowplow/iglu/wiki/Self-describing-JSON-Schemas) describing the data set
- a [Newline delimited JSON file](http://ndjson.org/) with all the contents of the data set

We can use schema-guru to generate a JSON schema from a set of JSON instances. You will find instructions on how to install schema-guru and the details of the parameters in [this guide](https://github.com/mediachain/aleph/blob/9c69b24ab95d173d10c9087402870ba7fbe62ce6/docs/schema-generation.md).

```
schema-guru schema --no-length --vendor uk.org.tate --name artist --schemaver 1-0-0 --output uk.org.tate-artist-jsonschema-1-0-0.json artists
```

```
schema-guru schema --no-length --vendor uk.org.tate --name artist --schemaver 1-0-0 --output uk.org.tate-artwork-jsonschema-1-0-0.json artworks
```

We also need to convert the existing data set, which is spread across multiple files, into a Newline delimited JSON. That can be done using the following commands (you may need to install [parallel](https://www.gnu.org/software/parallel/), which is available via [brew](http://brewformulas.org/Parallel)):

```
find ./artists -name "*.json" -type f -print | parallel -j 16 "cat {} | ( tr -d '\n'; echo )" > tate-artists.ndjson
```

```
find ./artworks -name "*.json" -type f -print | parallel -j 16 "cat {} | ( tr -d '\n'; echo )" > tate-artworks.ndjson
```

## Publishing the data
For this you need to have your local Mediachain node up and running. You have to [install concat](https://github.com/mediachain/concat#installation) and [aleph](https://github.com/mediachain/aleph#installation).

You can publish the previously created schemas using the following commands:

```
› mcclient publishSchema uk.org.tate-artist-jsonschema-1-0-0.json
Published schema with wki = schema:uk.org.tate/artist/jsonschema/1-0-0 to namespace mediachain.schemas
Object ID: QmYGpiJ41okjCewfLuRi8B3a6dB7sQKJMG6xd6EceNc2o4
Statement ID: 4XTTM1tagBWiPwSeTARik2ht8p1j4uKpfVEL1RkXCFUinqsaB:1478970714:70742
```

```
› mcclient publishSchema uk.org.tate-artwork-jsonschema-1-0-0.json
Published schema with wki = schema:uk.org.tate/artwork/jsonschema/1-0-0 to namespace mediachain.schemas
Object ID: QmVAFMrtvaHLuYS7FsDyMZgUHtePVE6775RAwJ9J4nkxUy
Statement ID: 4XTTM1tagBWiPwSeTARik2ht8p1j4uKpfVEL1RkXCFUinqsaB:1478970774:70743
```

The `Object ID` attribute from the `publishSchema` command is used to identify the schema (i.e. `schemaReference` flag) when publishing the data set to Mediachain:

```
mcclient publish --namespace museums.tate.artworks --idFilter .id --schemaReference QmVAFMrtvaHLuYS7FsDyMZgUHtePVE6775RAwJ9J4nkxUy tate-artworks.ndjson
```

```
mcclient publish --namespace museums.tate.artists --idFilter .id --schemaReference QmYGpiJ41okjCewfLuRi8B3a6dB7sQKJMG6xd6EceNc2o4 tate-artists.ndjson
```

[MCQL](https://github.com/mediachain/concat#basic-operations) can be used to interact with the data and confirm that the data is in the node:

```
› mcclient query "SELECT COUNT(*) FROM museums.tate.*"                  
70740
```

## Going public
See the instructions [here](https://github.com/mediachain/concat#going-public) to configure your NAT, register your node with the directory, and bring it online so anyone can interact with it.

## Conclusion
If you published a new dataset after following this tutorial, reach out to us on [Slack](http://slack.mediachain.io) so we can merge it into the Museum Node with the rest of the museum data!

Open an issue if you have any questions or problems!
