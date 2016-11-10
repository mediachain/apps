# Cooper Hewitt 🎨 Mediachain
## Getting the raw data
Cooper Hewitt has released their collection via a [data dump on Github](https://github.com/cooperhewitt/collection).

First, lets clone the repo. 

```
$ git clone --depth 1 git@github.com:cooperhewitt/collection.git
```

The `--depth` flag lets us create a shallow clone with only the latest commits, avoiding downloading the entire history of this large repo.

Inside the repo, the collection is broken up into folders representing different data types: departments, exhibitions, objects, people, periods, roles.

Each folder has a directory structure that may seem unusual. The unique ID of each object has been chopped into groups to serve as folder and file names:

> the full path for iRobot's Roomba vacuum cleaner (ID 18704235) is: objects/187/042/35/18704235.json

Lets process the data so it's easier to work with.

## Processing the data
We want the data in a Mediachain-friendly format: a single newline-delimited JSON file. We can traverse the folders with a single command. Let's process the `objects` collection first:

```
$ find ./collection/objects -name "*.json" -type f -print | parallel -j 16 "cat {} | ( tr -d '\n'; echo )" > cooperhewitt-objects-nojq.ndjson
```

## Generating a schema
Since the objects collection is quite large, lets take a sample of it before generating our schema:
```
$ sample --sample-size=10000 --shuffle cooperhewitt-objects.ndjson > cooperhewitt-objects-sample.ndjson
```

Follow the instructions to install [schema-guru](https://github.com/mediachain/aleph/blob/master/docs/schema-generation.md), a tool that automatically derives a JSON schema from a set of JSON instances. The [schema generation guide](https://github.com/mediachain/aleph/blob/master/docs/schema-generation.md) explains the naming conventions and the parameters we're using:
```
$ schema_guru schema --ndjson --no-length --vendor org.cooperhewitt --name object --schemaver 1-0-0 --output org.cooperhewitt-object-jsonschema-1-0-0.json cooperhewitt-objects-sample.ndjson
```

Publish the schema to Mediachain:
```
$ mcclient publishSchema org.cooperhewitt-object-jsonschema-1-0-0.json
```

You should see output similar to the following:
```
Published schema with wki = schema:org.cooperhewitt/object/jsonschema/1-0-0 to namespace mediachain.schemas
Object ID: QmZZocm4RynNrCe4poQcF7t1pD732dnCqaRFKAwYHwQ2tB
Statement ID: 4XTTM9Y6Sso29BhUFWsNwjRbtmQrTz1oYSPfVNFxMkhLyH7iF:1478808450:0
```

## Publish to Mediachain
We're ready to publish objects from the Cooper-Hewitt collection to Mediachain:

```
$ mcclient publish --namespace museums.cooperhewitt.objects --idFilter .id --schemaReference QmZZocm4RynNrCe4poQcF7t1pD732dnCqaRFKAwYHwQ2tB cooperhewitt-objects.ndjson
```

## Publishing the rest of the data
Repeat the steps above to create schemas and publish data for departments, exhibitions, people, periods, and roles.

## Interacting with the data
Lets confirm that the data is really in our node!

```
mcclient query "SELECT COUNT(*) FROM museums.cooperhewitt.*"
194316
```

You can interact with the rest of the data in the same way with MCQL, the Mediachain query language that is very similar to SQL. See the [README](https://github.com/mediachain/concat#basic-operations) for more query examples.

## Going public
See the instructions [here](https://github.com/mediachain/concat#going-public) to configure your NAT, register your node with the directory, and bring it online so anyone can interact with it.

## Conclusion
If you published a new dataset after following this tutorial, reach out to us on [Slack](http://slack.mediachain.io) so we can merge it into the Museum Node with the rest of the museum data!

Open an issue if you have any questions or problems!
