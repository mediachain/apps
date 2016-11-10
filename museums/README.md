# Museums on Mediachain
Earlier this year, Mediachain Labs blogged about [how difficult it is](https://blog.mediachain.io/bringing-cultural-metadata-to-life-12cc118b2298#.qkd1u3fjj) to use and publish open data from cultural institutions. Today, they publish their open data through proprietary APIs or as static data dumps on Github. The datasets live in disparate silos, unaware of each other’s existence. 

![](https://cdn-images-1.medium.com/max/1800/1*B3GazCVvNpqulGjjHV3PPA.jpeg)
*Open data exists in different shapes and lives in fractured, disparate silos.*

Developers hoping to use open data must accept the burden of interacting with multiple incompatible APIs, and this friction inhibits reuse.

Mediachain enables cultural heritage data to live in one place, and to become interoperable and easy to use. 

![](https://cdn-images-1.medium.com/max/1800/1*ru1HtO5OfLm3HbJQ-LTjrA.jpeg)
*Open cultural data in Mediachain*

## Crowdsourcing cultural heritage in Mediachain
The Mediachain community has initiated a crowdsourcing effort to aggregate all of the open access datasets from cultural institutions across the world in Mediachain.

Follow [this GitHub issue](https://github.com/mediachain/apps/issues/5) for progress and come say hello on [Slack](slack.mediachain.io) to join the community! 

## Contributing data
Now that Mediachain 1.0 is live, it is trivial to start adding various datasets into Mediachain! See the [1.0 blog post](https://blog.mediachain.io/bringing-cultural-metadata-to-life-12cc118b2298) and [README](http://github.com/mediachain/concat) for a high level overview.

### Museum ingestion tutorials
The Mediachain community is contributing tutorials for datasets from specific institutions to serve as guidelines for others.

Since data dumps come in various shapes and sizes, the tutorials provide a helpful guide for getting your data into a format that is easily ready to ingest into Mediachain.

- [MoMA Tutorial](https://github.com/mediachain/apps/blob/master/tutorials/moma.md) by @denisnazarov
* [Cooper-Hewitt Tutorial](https://github.com/mediachain/apps/blob/dn-cooperhewitt/tutorials/cooper-hewitt.md) by @denisnazarov

## Dataset checklist
- [x] DPLA @vyzo 
- [x] Rijksmuseum
- [x] MoMA @denisnazarov
- [x] Cooper-Hewitt @denisnazarov
- [ ] Tate @marionzualo 
- [ ] Brooklyn Museum
- [ ] Getty Research Institute
- [ ] LACMA
- [ ] SFMOMA
- [ ] Harvard Art Museum
- [ ] British Museum
- [ ] Europeana (@aisaac, @hugomanguinhas)
- [ ] [NYPL](https://github.com/NYPL-publicdomain/data-and-utilities)
  - [ ] extend w/ geotags [OldNYC](https://www.oldnyc.org/) ([dump](https://github.com/oldnyc/oldnyc.github.io/raw/master/data.json))
- [ ] [San Francisco Historical Photograph Collection](http://sfpl.org/index.php?pg=0200000301)
  - [ ] extend w/ geotags [OldSF](http://www.oldsf.org/) ([dump](http://www.oldsf.org/records.js.zip))
- [ ] [Shared Shelf Commons](http://www.sscommons.org/openlibrary/welcome.html#1)
- [ ] [CMOA](https://github.com/cmoa/collection)
- [ ] [Nationalmuseum](http://www.nationalmuseum.se/wikimediacommonseng) (via @DianeDrubay)
- [ ] [SMK](http://www.smk.dk/en/use-of-images-and-text/free-download-of-artworks/) (via @DianeDrubay)
- [ ] [British Library](http://www.openculture.com/2013/12/british-library-puts-1000000-images-into-public-domain.html)

See [this issue](https://github.com/mediachain/apps/issues/5) for community discussion on ingesting new datasets.

Please submit a pull request if you have suggestions for more!

If you help ingest a dataset, your name will be listed as a contributor in the public node's info.

## Interacting with the data
First, find the node ID of the museum node.
```
$ mcclient listPeers -i
QmeiY2eHMwK92Zt6X4kUUC3MsjMmVb2VnGZ17DhnhRPCEQ -- Metadata for CC images from DPLA, 500px, and pexels; operated by Mediachain Labs.
QmZ6dckUhRouVr6AsBTpK6vMLVpcz1KAeJAJVQEZQ5gCek -- Metadata for CC images from flickr; operated by Mediachain Labs.
QmTVsocFCSEdPyM8dZ734GRhjvvmYyL9fShyezVkbPj17E -- Curated Museum Metadata; Operated by Mediachain Labs
```

Now lets see what kind of data it has!
```
$ mcclient query -r QmTVsocFCSEdPyM8dZ734GRhjvvmYyL9fShyezVkbPj17E "SELECT namespace FROM *"
"museums.cooperhewitt.objects"
"museums.moma.artists"
"museums.moma.artworks"
"museums.rijksmuseum.artworks"
```

Lets get a count of the total items from all the museums.
```
$ mcclient query -r QmTVsocFCSEdPyM8dZ734GRhjvvmYyL9fShyezVkbPj17E "SELECT COUNT(*) FROM museums.*"
854057
```

For a guide on using the Mediachain query language to explore the data further, see this [README](https://github.com/mediachain/concat#basic-operations).
