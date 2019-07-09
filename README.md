The below is an example of how the current document MODS Application Profile for LibraryCloud could be rendered in markdown (.md) format. There's a helpful [cheat sheet for markdown syntax here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).
GitHub's version of Markdown is [GitHub Flavord Markdown (GFM)](https://github.github.com/gfm/). It allows for tables and other useful display features.

View the [prettified file on GitHub](https://github.com/joemull/Metadata/blob/master/README.md).

---

# MODS Application Profile for LibraryCloud
_Draft, rev. 2019-06-28_

## Overview

Harvard’s LibraryCloud service provides API access to descriptive metadata for Harvard Library resources. LibraryCloud metadata is openly available to the public. Anyone can use the API to find, gather, and repurpose the metadata. It also is used within Harvard Library applications--for example, by serving metadata to [Harvard Digital Collections](https://library.harvard.edu/digital-collections) and [CURIOSity Digital Collections](https://curiosity.lib.harvard.edu/)--and it underlies sites and services developed throughout the Harvard community. It also supports Harvard’s partnerships with initiatives such as DPLA. As such, the metadata aims to balance internal and external requirements and expectations.

LibraryCloud aggregates descriptive metadata from a variety of sources, mainly

- Alma, the integrated library system where the bulk of library cataloging is created
- JSTOR Forum, where most Harvard Library visual resources are described
- ArchivesSpace, for archival materials

Each source uses different metadata standards and vocabularies appropriate to its scope and function. To facilitate searching across and reusing the disparate metadata aggregated in LibraryCloud, the source metadata is converted into a common format, [MODS](https://www.loc.gov/standards/mods/).

The records undergo other transformations, normalizations, and enrichments to improve their interoperability and usefulness in the aggregated environment:

-	Incoming records are split into separate records when they represent multiple resources.
-	The names of Harvard libraries and archives are normalized into a standard full form, and a short form for faceting.
-	Text equivalents are added when the original metadata contains coded values for language and country/state of publication.
-	Records that describe digital resources in Harvard’s Digital Repository Service (DRS) are augmented with a subset of administrative metadata about those resources.
-	Records included in CURIOSity collections or other curatorial or administrative sets will include brief information about those sets.

Note that LibraryCloud is not the database of record for this metadata. The metadata in LibraryCloud is neither definitive nor exhaustive. Additional metadata and more specifically-defined metadata can be found in the source systems that provide metadata to LibraryCloud.

## MODS (Metadata Object Description Schema)
MODS version 3.6 is the base format of all metadata returned by the LibraryCloud Item API. The [documentation](https://www.loc.gov/standards/mods/mods-outline-3-6.html) of the MODS standard is comprehensive; therefore, Harvard’s profile will focus on implementation-specific and source-specific aspects of the metadata in LibraryCloud.

There are six sources of descriptive metadata records in LibraryCloud:

| Source Name | Source Code | Number of Records | Source Format | Transformation to MODS |
|---|---|---:|---|---|
| Alma | MH:ALMA | >15,000,000 | MARCXML | A [lightly customized version](https://github.com/harvard-library/librarycloud_ingest/blob/release/1.5.0/src/main/resources/MARC21slim2MODS3-6.xsl) of the Library of Congress MARC-to-MODS stylesheet MARC21slim2MODS3-6.xsl. |
| ArchivesSpace | MH:OASIS | >2,300,000 | Encoded Archival Description (EAD) | EAD files are converted to individual MODS records for each [archival component](https://github.com/harvard-library/librarycloud_ingest/blob/release/1.5.0/src/main/resources/eadcomponent2mods.xsl). |
| JSTOR Forum | MH:VIA | >7,000,000 | VIA XML | JSTOR Forum’s SSIO XML is first converted to Harvard’s legacy [VIA format](http://hul.harvard.edu/ois/xml/xsd/via/newvia.xsd), and then [from VIA to MODS](https://github.com/harvard-library/librarycloud_ingest/blob/release/1.5.0/src/main/resources/viacomponent2mods.xsl). Individual MODS records are created for each JSTOR Forum image record, whether or not the image has been digitized. |
| Iranian Oral History Project | MH:IOHP | ~900 | Custom | [Custom](https://github.com/harvard-library/librarycloud_ingest/blob/master/src/main/resources/mpcol2mods.xsl) |
| Jacques Burkhardt Scientific Drawings | MH:MCZArtwork | ~1,000 | Custom | [Custom](https://github.com/harvard-library/librarycloud_ingest/blob/master/src/main/resources/mcz2mods.xsl) |
| Milman Parry Collection of Oral Literature | MH:MHPL | ~1,800 | Custom | [Custom](https://github.com/harvard-library/librarycloud_ingest/blob/master/src/main/resources/iohp2mods.xsl) |

## Record Splitting
<!-- This section is very important; not sure where to put it. -Robin -->

Each incoming record will be transformed into one __or more__ MODS records. A record will be split during this process if it represents more than resource according to specific criteria per contributing source.

### Alma
If the incoming record contains more than one DRS URL for deliverable digital content (i.e., not counting a preview URL, such as a thumbnail image), one record will be created with all URLs plus all physical locations AND a separate record will be created for each digital manifestation.

The MODS `recordIdentifier` for each of the split records will concatenate the original Alma record identifier, an underscore, and the URN portion of the DRS URL to the deliverable content.

> [Example](https://api.lib.harvard.edu/v2/items/990088020470203941_HBS.Baker:10771779)

### JSTOR Forum
If the incoming record contains more than one item (`<display:DR>`), a separate MODS record will be created for each item, each record containing information about the work plus information about one of the items.  There is no record in LibraryCloud for the Work alone or the work with all items.

The MODS `recordIdentifier` for the split records will concatenate the Work record identifier, an underscore, and either 1) the URN portion of the DRS URL to the deliverable content, or 2) the item record identifier, if the item does not reference digital content.
<!-- Sadly, the above needs even more detail, since prior record identifiers are used for records created before the migration to JSTOR Forum, and JSTOR Forum record identifiers for records created in JSTOR Forum. -Robin -->

> [Example, Case 1](https://api.lib.harvard.edu/v2/items/S19482_urn-3:FHCL:3116579)

> [Example, Case 2](https://api.lib.harvard.edu/v2/items/S19482_olvsurrogate186373)

### ArchivesSpace
For each finding aid (Encoded Archival Description [EAD] file), a separate MODS record will be created for every described component. No record will be created for the archival resource (aka collection) itself, because that would redundant with the corresponding collection-level cataloging record from Alma. Each component record will inherit key fields from the hierarchy of the finding aid to ensure that the archival object is placed in context.

The MODS `recordIdentifier` for each of the split records will be the component id (Ref ID) of the archival object record.

> [Example](https://api.lib.harvard.edu/v2/items/hou01365c02879)

## MODS Structure
<!--This section is the most unformed/problematic. The special topics really are fundamental; the field-by-field profile less so, to my mind at least. But Normally, I discuss the element usage in the element list below, but our use of the relatedItem hierarchy is so fundamental to understanding the metadata that I wanted to highlight it. I’m sure how to do that effectively, though. - Robin -->

MODS consists of 20 top-level elements or element wrappers, all of which are optional and repeatable. Top-level elements may have subelements that, taken together within an instance of a top element, represent a single concept.

### Special Topics: Hierarchical Description
One of the MODS elements, `relatedItem`, allows for great flexibility in the way the description of a resource is structured that has implications for applications that consume the metadata.

All MODS top-level elements are valid within `relatedItem`. `relatedItem` has many uses, but one is crucial to the aggregation of metadata in LibraryCloud: it enables nested, hierarchical whole/part description.

Records from JSTOR Forum and from ArchivesSpace both take advantage of this hierarchical structure, but in different ways. In both cases, the record overall represents one resource (which may be a compound resource, such as a folder of letters that are not individually described) and a larger context for it. However, in ArchivesSpace records, the description moves from narrower to broader, while in JSTOR Forum, the description moves from the broader context to the specific item. __For ArchivesSpace and JSTOR Forum records, `relatedItem` information is necessary to provide context or specificity about the described resource.__

The type and displayLabel attributes in the `relatedItem` element indicate the kind of relationship:

| Element | Relationship |
| --- | --- |
| `<relatedItem type=”host”>` | A larger context of which the described resource is a part. |
| `<relatedItem type=”host” displayLabel=”collection”>` | The largest unit –the collection—of which the resource is a part. |
| `<relatedItem type=”constituent”>` | An item which is part of or representative of the primary object of description. |

Records for archival components from ArchivesSpace start with the description of one component of a collection, followed by __any number__ of levels of description that provide the context of that component within the collection.

For example, note the nested uses of `relatedItem` in [this record](https://api.lib.harvard.edu/v2/items?recordIdentifier=hou01365c02879).

| Element | Relationship |
| --- | --- |
| Record |  |
| __Sub-Series |	`<relatedItem type=”host”>` |
| ____Series | `<relatedItem type=”host”>` |
| ______Collection | `<relatedItem type=”host” displayLabel=”collection”>` |

```xml
<?xml version="1.0" encoding="UTF-8"?>
<results xmlns="http://api.lib.harvard.edu/v2/item" xmlns:HarvardDRS="http://hul.harvard.edu/ois/xml/ns/HarvardDRS" xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:librarycloud="http://hul.harvard.edu/ois/xml/ns/librarycloud" xmlns:marc="http://www.loc.gov/MARC21/slim" xmlns:mods="http://www.loc.gov/mods/v3" xmlns:oai_dc="http://www.openarchives.org/OAI/2.0/oai_dc/" xmlns:sets="http://hul.harvard.edu/ois/xml/ns/sets" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <pagination>
    <maxPageableSet>100000</maxPageableSet>
    <numFound>1</numFound>
    <query>recordIdentifier=hou01365c02879</query>
    <limit>10</limit>
    <start>0</start>
  </pagination>
  <items>
    <mods:mods>
      <mods:titleInfo>
        <mods:title>Updike, John. Squirrels mating : autograph manuscript and typescripts with autograph manuscript annotations and corrections, 1987.</mods:title>
      </mods:titleInfo>
      <mods:originInfo>
        <mods:dateCreated point="start">1987</mods:dateCreated>
        <mods:dateCreated point="end">1987</mods:dateCreated>
        <mods:dateCreated>1987.</mods:dateCreated>
      </mods:originInfo>
      <mods:physicalDescription>
        <mods:extent>1 folders</mods:extent>
      </mods:physicalDescription>
      <mods:physicalDescription>
        <mods:note type="organization">otherlevel</mods:note>
      </mods:physicalDescription>
      <mods:identifier>(2705).</mods:identifier>
      <mods:language>
        <mods:languageTerm authority="iso639-2b" type="code">und</mods:languageTerm>
        <mods:languageTerm authority="iso639-2b" type="text">Undefined</mods:languageTerm>
      </mods:language>
      <mods:accessCondition>There are no restrictions on physical access to a majority of this material.   A majority of this collection is shelved offsite. Retrieval requires advance notice. Check with Houghton Public Services staff.   Access to items (359) - (387), (780a), (5434), (6889a) and (7392) - (7395) is restricted until 2029 October 1. Consult curatorial staff. No copying including photography of restricted items is allowed.</mods:accessCondition>
      <mods:relatedItem otherType="HOLLIS for Archival Discovery record">
        <mods:location>
          <mods:url>https://id.lib.harvard.edu/ead/c/hou01365c02879/catalog</mods:url>
        </mods:location>
      </mods:relatedItem>
      <mods:recordInfo>
        <mods:recordChangeDate encoding="iso8601">20190618</mods:recordChangeDate>
        <mods:recordIdentifier source="MH:OASIS">hou01365c02879</mods:recordIdentifier>
      </mods:recordInfo>
      <mods:relatedItem type="host">
        <mods:titleInfo>
          <mods:title>F. Poetry</mods:title>
        </mods:titleInfo>
        <mods:recordInfo>
          <mods:recordIdentifier>hou01365c02509</mods:recordIdentifier>
        </mods:recordInfo>
        <mods:relatedItem type="host">
          <mods:titleInfo>
            <mods:title>I. Compositions</mods:title>
          </mods:titleInfo>
          <mods:recordInfo>
            <mods:recordIdentifier>hou01365c00001</mods:recordIdentifier>
          </mods:recordInfo>
          <mods:relatedItem type="host" displayLabel="collection">
            <mods:location>
              <mods:physicalLocation valueURI="http://isni.org/isni/0000000121904234" displayLabel="Harvard repository" type="repository">Houghton Library, Harvard University</mods:physicalLocation>
            </mods:location>
            <mods:identifier>MS Am 1793</mods:identifier>
            <mods:titleInfo>
              <mods:title>John Updike papers</mods:title>
            </mods:titleInfo>
            <mods:originInfo>
              <mods:dateCreated point="start">1940</mods:dateCreated>
              <mods:dateCreated point="end">2009</mods:dateCreated>
              <mods:dateCreated>1940-2009</mods:dateCreated>
            </mods:originInfo>
            <mods:recordInfo>
              <mods:recordIdentifier>hou01365</mods:recordIdentifier>
            </mods:recordInfo>
            <mods:relatedItem otherType="HOLLIS record">
              <mods:location>
                <mods:url>https://id.lib.harvard.edu/alma/990006018390203941/catalog</mods:url>
              </mods:location>
            </mods:relatedItem>
            <mods:relatedItem otherType="Finding Aid">
              <mods:location>
                <mods:url>https://id.lib.harvard.edu/ead/hou01365/catalog</mods:url>
              </mods:location>
            </mods:relatedItem>
            <mods:extension>
              <librarycloud:HarvardRepositories xmlns="http://www.loc.gov/mods/v3">
                <librarycloud:HarvardRepository>Houghton</librarycloud:HarvardRepository>
              </librarycloud:HarvardRepositories>
            </mods:extension>
          </mods:relatedItem>
        </mods:relatedItem>
      </mods:relatedItem>
      <mods:extension>
        <librarycloud:librarycloud xmlns="http://www.loc.gov/mods/v3">
          <librarycloud:originalDocument>https://s3.amazonaws.com/harvard.ead/hou01365.xml</librarycloud:originalDocument>
          <librarycloud:processingDate>2019-06-18T09:52Z</librarycloud:processingDate>
        </librarycloud:librarycloud>
      </mods:extension>
      <mods:location />
    </mods:mods>
  </items>
</results>
```

Records from JSTOR Forum, in contrast, start with the broader description of a painting, building, event, etc., and may have 0-2 additional levels of description for a specific view, such as a perspective, a detail, or a verso.

Here is an [example record](https://api.lib.harvard.edu/v2/items?recordIdentifier=W209586_urn-3:VIT.BB:25445424):

| Element | Relationship |
| --- | --- |
| Painting |  |
| __X-Ray (detail) | `<relatedItem type=”constituent”>` |

<!-- ```
<?xml version="1.0" encoding="UTF-8"?>
<results xmlns="http://api.lib.harvard.edu/v2/item" xmlns:HarvardDRS="http://hul.harvard.edu/ois/xml/ns/HarvardDRS" xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:librarycloud="http://hul.harvard.edu/ois/xml/ns/librarycloud" xmlns:marc="http://www.loc.gov/MARC21/slim" xmlns:mods="http://www.loc.gov/mods/v3" xmlns:oai_dc="http://www.openarchives.org/OAI/2.0/oai_dc/" xmlns:sets="http://hul.harvard.edu/ois/xml/ns/sets" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <pagination>
    <maxPageableSet>100000</maxPageableSet>
    <numFound>1</numFound>
    <query>recordIdentifier=W209586_urn-3:VIT.BB:25445424</query>
    <limit>10</limit>
    <start>0</start>
  </pagination>
  <items>
    <mods:mods xsi:schemaLocation="http://www.loc.gov/mods/v3 http://www.loc.gov/standards/mods/v3/mods-3-6.xsd">
      <mods:titleInfo>
        <mods:title>Salvator Mundi</mods:title>
      </mods:titleInfo>
      <mods:name>
        <mods:namePart>Antonello da Messina</mods:namePart>
        <mods:namePart type="date">ca.1430-1479, Italian</mods:namePart>
        <mods:role>
          <mods:roleTerm>creator</mods:roleTerm>
        </mods:role>
        <mods:role>
          <mods:roleTerm>artist</mods:roleTerm>
        </mods:role>
      </mods:name>
      <mods:typeOfResource>still image</mods:typeOfResource>
      <mods:genre>paintings</mods:genre>
      <mods:originInfo>
        <mods:dateOther keyDate="yes">1465</mods:dateOther>
        <mods:dateCreated point="start">1465</mods:dateCreated>
        <mods:dateCreated point="end">1465</mods:dateCreated>
        <mods:dateCreated>1465</mods:dateCreated>
      </mods:originInfo>
      <mods:physicalDescription>
        <mods:extent>38.7 x 29.8 cm.</mods:extent>
      </mods:physicalDescription>
      <mods:extension>
        <cdwalite:cultureWrap xmlns:cdwalite="http://www.getty.edu/research/conducting_research/standards/cdwa/cdwalite" xmlns="http://www.loc.gov/mods/v3" xmlns:xlink="http://www.w3.org/TR/xlink">
          <cdwalite:culture>Italian</cdwalite:culture>
        </cdwalite:cultureWrap>
      </mods:extension>
      <mods:location>
        <mods:physicalLocation type="repository">National Gallery (Great Britain), London, England</mods:physicalLocation>
        <mods:shelfLocator>673</mods:shelfLocator>
      </mods:location>
      <mods:relatedItem type="constituent">
        <mods:titleInfo>
          <mods:title>X-Ray (detail)</mods:title>
        </mods:titleInfo>
        <mods:name>
          <mods:namePart>Burroughs, Alan</mods:namePart>
          <mods:namePart type="date">1897-1965, American</mods:namePart>
          <mods:role>
            <mods:roleTerm>creator</mods:roleTerm>
          </mods:role>
          <mods:role>
            <mods:roleTerm>photographer</mods:roleTerm>
          </mods:role>
        </mods:name>
        <mods:name>
          <mods:namePart>National Gallery</mods:namePart>
          <mods:namePart type="date">British art museum, London, founded 1824</mods:namePart>
          <mods:role>
            <mods:roleTerm>associated name</mods:roleTerm>
          </mods:role>
        </mods:name>
        <mods:typeOfResource>still image</mods:typeOfResource>
        <mods:physicalDescription>
          <mods:extent>238 x 285 mm</mods:extent>
        </mods:physicalDescription>
        <mods:note>Historical: x-ray photo taken by Alan Burroughs, Fogg Art Museum, September 1927. Inscriptions: owner's stamp on verso.</mods:note>
        <mods:subject>
          <mods:topic>stamps (marks)</mods:topic>
        </mods:subject>
        <mods:extension>
          <cdwalite:indexingMaterialsTechSet xmlns:cdwalite="http://www.getty.edu/research/conducting_research/standards/cdwa/cdwalite" xmlns="http://www.loc.gov/mods/v3" xmlns:xlink="http://www.w3.org/TR/xlink">
            <cdwalite:termMaterialsTech>gelatin silver prints</cdwalite:termMaterialsTech>
          </cdwalite:indexingMaterialsTechSet>
        </mods:extension>
        <mods:classification>V 41 oversize OV 3</mods:classification>
        <mods:location>
          <mods:url displayLabel="Full Image" note="unrestricted" access="raw object">https://nrs.harvard.edu/urn-3:VIT.BB:25445424</mods:url>
          <mods:url displayLabel="Thumbnail" access="preview">https://nrs.harvard.edu/urn-3:VIT.BB:25445424?width=150&amp;height=150&amp;usethumb=y</mods:url>
        </mods:location>
        <mods:location>
          <mods:physicalLocation displayLabel="Harvard repository" type="repository">Biblioteca Berenson, Fototeca, Villa I Tatti - The Harvard University Center for Italian Renaissance Studies</mods:physicalLocation>
          <mods:shelfLocator>400089_1</mods:shelfLocator>
        </mods:location>
        <mods:accessCondition displayLabel="copyright" type="useAndReproduction">Access Restrictions: This copy is furnished for study purposes only. Written authorization must be obtained for all other uses.</mods:accessCondition>
        <mods:recordInfo>
          <mods:recordIdentifier>13022620</mods:recordIdentifier>
        </mods:recordInfo>
        <mods:extension>
          <librarycloud:HarvardRepositories xmlns="http://www.loc.gov/mods/v3" xmlns:xlink="http://www.w3.org/TR/xlink">
            <librarycloud:HarvardRepository>Biblioteca Berenson</librarycloud:HarvardRepository>
          </librarycloud:HarvardRepositories>
        </mods:extension>
      </mods:relatedItem>
      <mods:relatedItem otherType="HOLLIS Images record">
        <mods:location>
          <mods:url>https://id.lib.harvard.edu/images/olvwork209586/urn-3:VIT.BB:25445424/catalog</mods:url>
        </mods:location>
      </mods:relatedItem>
      <mods:recordInfo>
        <mods:recordContentSource authority="marcorg">MH</mods:recordContentSource>
        <mods:recordContentSource authority="marcorg">MH-VIA</mods:recordContentSource>
        <mods:recordChangeDate encoding="iso8601">20141227</mods:recordChangeDate>
        <mods:recordIdentifier source="MH:VIA">W209586_urn-3:VIT.BB:25445424</mods:recordIdentifier>
        <mods:languageOfCataloging>
          <mods:languageTerm>eng</mods:languageTerm>
        </mods:languageOfCataloging>
      </mods:recordInfo>
      <mods:language>
        <mods:languageTerm type="code">zxx</mods:languageTerm>
        <mods:languageTerm type="text">No linguistic content</mods:languageTerm>
      </mods:language>
      <mods:extension>
        <HarvardDRS:DRSMetadata xmlns="http://www.loc.gov/mods/v3" xmlns:xlink="http://www.w3.org/TR/xlink">
          <HarvardDRS:inDRS>true</HarvardDRS:inDRS>
          <HarvardDRS:accessFlag>P</HarvardDRS:accessFlag>
          <HarvardDRS:contentModel>STILL IMAGE</HarvardDRS:contentModel>
          <HarvardDRS:uriType>IDS</HarvardDRS:uriType>
          <HarvardDRS:fileDeliveryURL>https://nrs.harvard.edu/urn-3:VIT.BB:25445424</HarvardDRS:fileDeliveryURL>
          <HarvardDRS:ownerCode>VIT.BERE</HarvardDRS:ownerCode>
          <HarvardDRS:ownerCodeDisplayName>Biblioteca Berenson</HarvardDRS:ownerCodeDisplayName>
          <HarvardDRS:lastModifiedDate>2016-02-18T16:37:48.316Z</HarvardDRS:lastModifiedDate>
        </HarvardDRS:DRSMetadata>
      </mods:extension>
      <mods:extension>
        <librarycloud:librarycloud xmlns="http://www.loc.gov/mods/v3" xmlns:xlink="http://www.w3.org/TR/xlink">
          <librarycloud:availableTo>Everyone</librarycloud:availableTo>
          <librarycloud:digitalFormats>
            <librarycloud:digitalFormat>Images</librarycloud:digitalFormat>
          </librarycloud:digitalFormats>
          <librarycloud:originalDocument>https://s3.amazonaws.com/via-presto/prod/olvwork209586.xml</librarycloud:originalDocument>
          <librarycloud:processingDate>2019-06-29T11:51Z</librarycloud:processingDate>
        </librarycloud:librarycloud>
      </mods:extension>
      <mods:location>
        <mods:url displayLabel="Harvard Digital Collections" access="object in context">https://id.lib.harvard.edu/digital_collections/W209586_urn-3:VIT.BB:25445424</mods:url>
      </mods:location>
    </mods:mods>
  </items>
</results>
``` -->

Here is [another example](https://api.lib.harvard.edu/v2/items?recordIdentifier=G80_olvsurrogate307431):

| Element | Relationship |
| --- | --- |
| Quilt series |  |
| __One Quilt | `<relatedItem type=”constituent”>` |
| ____Total view of the quilt | `<relatedItem type=”constituent”>` |

### Special Topics: Non-Latin Script Metadata
Non-Latin script may appear in LibraryCloud records from any source. However, the MODS `altRepGroup` attribute is only used in Alma records to designate paired elements containing corresponding information in transliteration and in vernacular script.

If an `altRepGroup` attribute is present with a value other than “00”, there will be another element with an identical `altRepGroup` value containing a representation of some or all of the element information in a different script (e.g., Arabic).

> [Example](https://api.lib.harvard.edu/v2/items?recordIdentifier=990000773410203941)


```xml
<mods:titleInfo altRepGroup="02">
  <mods:nonSort>al- </mods:nonSort>
  <mods:title>Nahḍah al-Isrāʼīlīyah wa-tārīkhuhā al-khālid</mods:title>
  <mods:subTitle>
    muzayyinan bi-rusūm ʻuẓmāʼ al-Isrāʼīlīyīn fī al-ʻālam
  </mods:subTitle>
</mods:titleInfo>

<mods:titleInfo altRepGroup="02">
  <mods:title>نهضة الاسرائيلية وتاريخها الخالد</mods:title>
  <mods:subTitle>مزينا برسوم عظماء الاسرائيلية في العالم</mods:subTitle>
</mods:titleInfo>
```

Non-Latin script metadata may occur in records from other sources, but it will not be marked for association with its transliteration.

## Profile by MODS Top-Level Element
#### `titleInfo`

Most, but not all, records will include at least one top-level `titleInfo` element. The exception is a subset of archival component records from ArchivesSpace. Lacking a `titleInfo`, these are required to have a top-level `originInfo`/`dateCreated`. See an [example](https://api.lib.harvard.edu/v2/items?q=hou02652c00990).

##### Attribute Usage: `type` or `otherType`
- `type` values: abbreviated, translated, alternative, uniform
- `otherType` values are not controlled
- `titleInfo` elements that contain neither `type` nor `otherType` attributes can be considered primary titles
##### Subelements
`titleInfo` is a wrapper element

| Subelement | May occur in records from |
|:---|:---|
| `title` | Alma, JSTOR Forum, ArchivesSpace |
| `subTitle` | Alma |
| `partNumber` | Alma |
| `nonSort` | Alma |

When multiple subelements are used, their order is important and should be retained in displays to insure intelligibility.

#### `name`

TBD

#### `typeOfResource`
`typeOfResource` appears in Alma and JSTOR Forum records; it is not present in ArchivesSpace records. While repeatable, it is not, in fact, repeated.
Alma records can contain any of the values enumerated in the MODS 3.6 schema. All JSTOR Forum records will have the value `“still image”`.

##### Attribute Usage
`collection = "yes"`
Records from Alma representing manuscript material will contain the `typeOfResource` attribute `manuscript = "yes"`

`manuscript = "yes"`
Records from Alma representing manuscript material will contain the `typeOfResource` attribute `manuscript = "yes"`

***genre***
The *genre* element is present in many Alma records, nearly all JSTOR Forum records, and all Jacques Burkhardt records.
It is not present in ArchivesSpace records, Iranian Oral History, or Milman Parry records.

***originalInfo*** is a wrapper element for information about the creation or issurance of the resource.

| Subelement | May occur in records from |
|:---|:---|
| place/placeTerm | Alma, JSTOR Forum, ArchivesSpace |
| publisher | Alma |
| copyrightDate | Alma |
| dateCreated | Alma, JSTOR Forum, ArchivesSpace |
| dateIssued | Alma |
| dateOther | JSTOR Forum |

***place***
The *place* wrapper element will contain one or more *placeTerm* elements. If more than one *placeTerm* is present within a single *place* element, all *placeTerms* contain different representations of the same place.

[Example](https://api.lib.harvard.edu/v2/items/990026310040203941)

```xml
<mods:place>
<mods:placeTerm authority="marccountry" type="code">miu</mods:placeTerm>
<mods:placeTerm authority="marccountry" type="text">Michigan</mods:placeTerm>
</mods:place>
<mods:place>
<mods:placeTerm type="text">Detroit</mods:placeTerm>
</mods:place>
```

Note the use of the attribute *type = "code"* in Alma records.

**Date Elements**
MODS supports seven different elements to express dates associated with the creation of a resource. Any of these may appear in LibraryCloud, but generally ArchivesSpace and JSTOR Forum dates will be in the *dateCreated* element, while Alma dates will use *dateIssued*.

***Date Elements Attribute Usage***
Date elements with no attributes or with the keyDate attribute are free-text date expressions suitable for display.

Some attributes are important for applications that use LibraryCloud metadata:

*encoding*
The encoding attribute will only occur in records from Alma, and the only value is “marc”. These date elements are designed to support date and date-range searching and are best omitted from displays.

*point*
These date elements designate the start and end of a date range. They support date-range searching and can be omitted from displays. There will typically be another date element better suited for display in the same *originInfo* element or in a sibling *originInfo* element.

*keyDate*
The keyDate attribute only appears in JSTOR Forum records. It duplicates dateCreated [lacking attributes] in *dateOther* with keyDate="yes", e.g.,

```xml
<mods:originInfo>
  <mods:dateOther keyDate="yes">early/mid 20th century</mods:dateOther>
  <mods:dateCreated point="start">1910</mods:dateCreated>
  <mods:dateCreated point="end">1960</mods:dateCreated>
  <mods:dateCreated>early/mid 20th century</mods:dateCreated>
</mods:originInfo>
```

***language***
All Alma records contain one or more coded language designation in a *languageTerm* element. The primary language code will also be available as text in a second *languageTerm* element in the same language parent element. *languageTerms* for different languages, as opposed to different ways of expressing the same language, will be in separate language elements.  

Use the *type* attribute to select between code and text values.

``` xml
<mods:language>
<mods:languageTerm authority="iso639-2b" type="code">per </mods:languageTerm>
<mods:languageTerm authority="iso639-2b" type="text">Persian </mods:languageTerm>
```

JSTOR Forum records do not contain language information, so for the purposes of LibraryCloud, the following has been added to all records derived from JSTOR Forum:

``` xml
<mods:languageTerm type="code">zxx</mods:languageTerm>
<mods:languageTerm type="text">No linguistic content</mods:languageTerm>
```

In ArchivesSpace records, language is set at the collection-level and may not be accurate for all items in the collection. For that reason, item level records derived from ArchivesSpace contain.

``` xml
<mods:languageTerm authority="iso639-2b" type="code">und</mods:languageTerm>
<mods:languageTerm authority="iso639-2b" type="text">Undefined</mods:languageTerm>
```

***location***
The two most important uses of location in LibraryCloud records are for 1) information about the Harvard repository holding the material, and 2) links to digital content.

***physicalLocation***
There can be none, one, or several *location* elements each containing one *physicalLocation*. *physicalLocations* that represent Harvard holdings will contain the attribute *displayLabel="Harvard repository"*. Most of these will also contain a valueURI attribute with an URI from International Standard Name Identifier (ISNI) or the Library of Congress Name Authority File (LCNAF).

***url***

- mods:url access="raw object" designates deliverable digital content in the DRS.
- mods:url access="preview" designates thumbnail images for content in the DRS.
- mods:url access="object in context" links to the bibliographic record for the digital content from the DRS within a curated collection site. The specific collection will be indicated with a displayLabel attribute, e.g., displayLabel="Harvard Digital Collections".

***physicalDescription*** TBD
(extent, form, note)

***abstract***
Records from Alma, JSTOR Forum, and ArchivesSpace may contain in the *abstract* element.

***tableOfContents***
Only Alma records can contain in the *tableOfContents* element.

***targetAudience*** and _part_** are almost non-existent in LibraryCloud.

***note*** TBD

***subject***
*subject* is a wrapper element.

***classification***
In LibraryCloud, only Alma ...

***relatedItem***
ALMA
The MODS *relatedItem type* attribute contains enumerated values: preceding, succeeding, original, host, constituent, series, otherVersion, otherFormat, isReferencedBy, references, reviewOf. Examples in Appendix TBD.

Alternatively, the *otherType* attribute ...
See Special Topics: Hierarchical Description for other uses of *relatedItem*.

***accessCondition***
*displayLabel - "copyright"*
*type = "useAndReproduction"*

***extension***
LibraryCloud records will contain one or more MODS <extension> elements to augment the basic descriptive metadata record.
Some extensions carry descriptive metadata that cannot otherwise be expressed in MODS: style, culture, and materials, techniques, and support.
- cdwalite:cultureWrap
- cdwalite:indexingMaterialsTechSet
- cdwalite:styleWrap

The other category of extensions supplements the original metadata with related metadata from other systems, with administrative information, or with metadata values normalized to support particular functions. These extensions are
- DRSMetadata  
- librarycloud
- sets

For details, see Appendix 1: MODS Extensions, below.

***recordInfo***
A LibraryCloud record will contain one or more *recordInfo* wrapper elements, with at most one occurrence per level of hierarchy. That is, each *relatedItem* may also include a *recordInfo* element that applies specifically to the content of that related item. The primary *recordInfo* — the one that applies to the record as a whole--occurs at the top level of the record. Only the primary *recordInfo* will include the source attribute.
The subelements within the primary *recordInfo* element vary by source, but all will include *recordIdentifier* and *recordChangeDate*.

JSTOR Forum Example:
``` xml
 <mods:recordInfo>
<mods:recordContentSource authority="marcorg">MH</mods:recordContentSource>
<mods:recordContentSource authority="marcorg">MH-VIA </mods:recordContentSource>
<mods:recordChangeDate encoding="iso8601">20181109</mods:recordChangeDate>
<mods:recordIdentifier source="MH:VIA">8000464237_urn-3:FHCL.JUD:9806298</mods:recordIdentifier>
<mods:languageOfCataloging>
<mods:languageTerm>eng</mods:languageTerm>
</mods:languageOfCataloging>
</mods:recordInfo>
```

Alma Example:
``` xml
<mods:recordInfo>
<mods:recordCreationDate encoding="marc">821202</mods:recordCreationDate>
<mods:recordChangeDate encoding="iso8601">20180528</mods:recordChangeDate>
<mods:recordIdentifier source="MH:ALMA">990000000020203941</mods:recordIdentifier>
<mods:recordOrigin>
Converted from MARCXML to MODS version 3.6 using MARC21slim2MODS3-6.xsl (Revision 1.117 2017/02/14)
</mods:recordOrigin>
</mods:recordInfo>
```

ArchivesSpace Example:
``` xml
<mods:recordInfo>
<mods:recordChangeDate encoding="iso8601">20190523</mods:recordChangeDate>
<mods:recordIdentifier source="MH:OASIS">hua03010c00264</mods:recordIdentifier>
</mods:recordInfo>
```

##Appendix 1: MODS Extensions

**cdwalite:cultureWrap**
*cultureWrap* is a wrapper element containing a single *cdwalite: culture* element. Multiple cultures will appear in seperate extensions. This extension only occurs in JSTOR Forum records.

``` xml
<mods:extension>
<cdwalite:cultureWrap xmlns:cdwalite="http://www.getty.edu/research/conducting_research/standards/cdwa/cdwalite">
<cdwalite:culture>Egyptian</cdwalite:culture>
</cdwalite:cultureWrap>
</mods:extension>
```

**cdwalite:indexingMaterialsTechSet**
*indexingMaterialsTechSet* is a wrapper element containing a single *cdwalite:termMaterialsTech* element. Multiple materials or techniques will appear in separate extensions. This extension only occurs in JSTOR Forum records.

``` xml
<mods:extension>
<cdwalite:indexingMaterialsTechSet xmlns:cdwalite="http://www.getty.edu/research/conducting_research/standards/cdwa/cdwalite">
<cdwalite:termMaterialsTech>granite</cdwalite:termMaterialsTech>
</cdwalite:indexingMaterialsTechSet>
</mods:extension>
```

**cdwalite:styleWrap**
*styleWrap* is a wrapper element containing a single *cdwalite:style* element. Multiple styles will appear in separate extensions. This extension only occurs in JSTOR Forum records.

Example:

``` xml
<mods:extension>
<cdwalite:styleWrap xmlns:cdwalite="http://www.getty.edu/research/conducting_research/standards/cdwa/cdwalite">
<cdwalite:style>New Kingdom</cdwalite:style>
</cdwalite:styleWrap>
</mods:extension>
```
