The below is an example of how the current document MODS Application Profile for LibraryCloud could be rendered in markdown (.md) format. There's a helpful [cheat sheet for markdown syntax here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).
GitHub's version of Markdown is [_GitHub Flavord Markdown (GFM)_](https://github.github.com/gfm/). It allows for tables and other useful display features.

---

# MODS Structure

MODS consists of 20 top-level elements or element wrappers, all of which are optional and repeatable. Top-level elements may have subelements that, taken together within an instance of a top element, represent a single concept.

## Special Topics: Hierarchical Description
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

```
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

Here is [another example](https://api.lib.harvard.edu/v2/items?recordIdentifier=G80_olvsurrogate307431).

| Element | Relationship |
| --- | --- |
| Quilt series |  |
| __One Quilt | `<relatedItem type=”constituent”>` |
| ____Total view of the quilt | `<relatedItem type=”constituent”>` |
