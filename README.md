Matadisco
=========

Matadisco makes datasets more discoverable. It uses [ATProto] for publishing records that point to the metadata.

For more details about the background and the ideas behind the project refer to the [announcement blog post of the IPFS Foundation] as well as a [more technical one at @vmx' blog].

This is the central entry point for the Matadisco project. It's the place to discuss ideas and issues.


Lexicon schema
--------------

The central piece of Matadisco is the [Lexicon schema]. To make it easier to read, the [MLF] syntax is used:

```mlf
/// A Matadisco record
record matadisco {
    /// The time the original metadata/data was published
    publishedAt!: Datetime,
    /// A URI that links to resource containing the metadata
    resource!: Uri,
    preview: preview,
    tags: tags,
}

/// Preview of the data
def type preview = {
    /// The media type the preview has
    mimeType!: string,
    /// The URL to the preview
    url: Uri,
};

/// Tags that describe the metadata. A tag might have a corresponding top-level key with the same name.
def type tags = tag[] constrained {
    maxLength: 20,
};

/// A single tag
inline type tag = string constrained {
    minLength: 1,
    maxLength: 200,
};
```

See [cx.vmx.matadisco.json] for the ATProto Lexicon schema in its usual JSON format.

This Lexicon is expected to evolve when more people start using it to publish data. You can already add arbitrary fields to ATProto records, but it would make sense to work together on common fields to increase interoperability on those.

Generating the JSON format from MLF file (it's expected that Rust is setup properly):

```console
> cargo install --git https://tangled.org/stavola.xyz/mlf mlf-cli
> mlf generate lexicon --input cx.vmx.matadisco.mlf  --output ./ --flat
```


Publishing data
---------------

Publishing Matadisco records needs some service to run. Ideally it's integrated directly into the publishing pipeline of the metadata records themselves. Though you can also run a third party service. Projects doing that (please open a PRs to add your own project):

 - [sentinel-to-atproto]: Publishes records sourced from [Element 84's Earth Search STAC catalogue]. It's using a scheduled [Cloudflare Worker] that queries the catalogue every few minutes.
 - [gdi-de-csw-to-atproto]: Publishes records sourced from the [German geodata catalogue geodatenkatalog.de]. It's using schduled GitHub workers. The the XML based [CSW] requests needs a bit of processing and sometimes thousands of records get updated at once.They are then published stretched over a longer period of time.


Consuming data
--------------

Metadisco records can be used to build your own portals or workflows.

 - [matadisco-viewer]: Very basic viewer of all incoming Metadisco records in real-time.


[ATProto]: https://atproto.com/
[announcement blog post of the IPFS Foundation]: https://ipfsfoundation.org/matadisco-can-we-bootstrap-public-data-discovery-with-atproto/
[more technical one at @vmx' blog]: https://vmx.cx/cgi-bin/blog/index.cgi/matadisco%3A2026-03-23%3Aen%2CATProto%2Cgeo
[Lexicon Schema]: https://atproto.com/specs/lexicon
[MLF]: https://mlf.lol/
[cx.vmx.matadisco.json]: cx.vmx.matadisco.json
 [sentinel-to-atproto]: https://github.com/vmx/sentinel-to-atproto/
[Element 84's Earth Search STAC catalogue]: https://radiantearth.github.io/stac-browser/#/external/earth-search.aws.element84.com/v1/collections/sentinel-2-l2a
[Cloudflare Worker]: https://workers.cloudflare.com/
[gdi-de-csw-to-atproto]: https://github.com/vmx/gdi-de-csw-to-atproto/
[German geodata catalogue geodatenkatalog.de]: https://www.gdi-de.org/en/practice-projects/technical-components/spatial-data-catalogue
[CSW]: https://en.wikipedia.org/wiki/Catalogue_Service_for_the_Web
[matadisco-viewer]: https://github.com/vmx/matadisco-viewer/
