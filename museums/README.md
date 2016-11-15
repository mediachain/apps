# Museums on Mediachain
![](https://cdn-images-1.medium.com/max/1800/1*ru1HtO5OfLm3HbJQ-LTjrA.jpeg)

Earlier this year, we blogged about [how difficult it is](https://blog.mediachain.io/bringing-cultural-metadata-to-life-12cc118b2298#.qkd1u3fjj) to publish and use open data from cultural institutions because it is incompatible and fractured across many different silos.

This month, we started to crowdsource all of the open access datasets from cultural institutions across the world in Mediachain. Mediachain is the perfect place to unite these collections since it allows cultural heritage data to live in one place, be interoperable and easy to use.

Follow our progress in [this GitHub issue](https://github.com/mediachain/apps/issues/5) and join the community on [Slack](http://slack.mediachain.io)!

## Contributing data
Now that Mediachain 1.0 is live, it is really easy to add your favorite museum's collection to Mediachain! If you haven't already, check out the [concat README](http://github.com/mediachain/concat) for quick overview on how to get started.

### Museum data tutorial
The quickest way to learn how to add a museum's collection if to follow the [Ingesting MoMA](https://github.com/mediachain/apps/blob/master/museums/moma.md) tutorial. It literally took me minutes to pull down the collection data from MoMA's GitHub and publish it to Mediachain.

To see how to publish a dataset that is in a different format from MoMA's (scattered across multiple files), check out [Ingesting Cooper-Hewitt](https://github.com/mediachain/apps/blob/master/museums/cooper-hewitt.md).

If you'd like to share the steps you took to publish your favorite museum's data for others to learn from, feel free to submit a PR!

## Dataset checklist
Here is an active list of collections we're adding, with names of community members who contributed them to Mediachain! See [this issue](https://github.com/mediachain/apps/issues/5) for discussion on adding new ones. Please comment and submit a pull request if you have suggestions for more!

- [x] DPLA @vyzo
- [x] Rijksmuseum
- [x] MoMA ([@denisnazarov](https://github.com/denisnazarov)) ([guide](https://github.com/mediachain/apps/blob/master/museums/moma.md))
- [x] Cooper-Hewitt ([@denisnazarov](https://github.com/denisnazarov)) ([guide](https://github.com/mediachain/apps/blob/master/museums/cooper-hewitt.md))
- [x] [Tate](https://github.com/tategallery/collection) ([@marionzualo](https://github.com/marionzualo))  ([guide](https://github.com/mediachain/apps/blob/master/museums/tate.md))
- [x] Brooklyn Museum ([@pyython](https://github.com/pyython))
- [ ] [ACMI](https://github.com/acmilabs/collection)
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

If you add a collection, submit a PR so your name can be listed as a contributor above as well as in the node's info.

## Interacting with the data
First, follow the instructions [here](http://github.com/mediachain/concat) to get your local Mediachain node up and running.

Find the peer ID of the museum node.
```
$ mcclient listPeers -i
QmeiY2eHMwK92Zt6X4kUUC3MsjMmVb2VnGZ17DhnhRPCEQ -- Metadata for CC images from DPLA, 500px, and pexels; operated by Mediachain Labs.
QmZ6dckUhRouVr6AsBTpK6vMLVpcz1KAeJAJVQEZQ5gCek -- Metadata for CC images from flickr; operated by Mediachain Labs.
QmTVsocFCSEdPyM8dZ734GRhjvvmYyL9fShyezVkbPj17E -- Curated Museum Metadata; Operated by Mediachain Labs
```

Let's see what kind of data it has!
```
$ mcclient query -r QmTVsocFCSEdPyM8dZ734GRhjvvmYyL9fShyezVkbPj17E "SELECT namespace FROM *"
"museums.cooperhewitt.objects"
"museums.moma.artists"
"museums.moma.artworks"
"museums.rijksmuseum.artworks"
```

Let's get a count of the total items from all the museums.
```
$ mcclient query -r QmTVsocFCSEdPyM8dZ734GRhjvvmYyL9fShyezVkbPj17E "SELECT COUNT(*) FROM museums.*"
854057
```

For a guide on using the Mediachain query language to explore the data further, see the [README](https://github.com/mediachain/concat#basic-operations).
