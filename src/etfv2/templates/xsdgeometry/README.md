# Template XSD + geometrie
## Goed te weten vooraf
1. de validator maakt gebruik van generieke XQuery-code (2 bestanden) voor de verwerking. De verwijzing hiernaar wordt autmatisch opgenomen, het is dus niet nodig hier nog iets voor te doen. Wel is het belangrijk dat deze code staat in de directory ../../projects/generic/. Het gaat om de bestanden:
  1. ets-generic-bsxets.xq
  1. testquery.xq
  1. schematron-bsxets.xq
  1. schematron-query-prolog.xq
1. het aanmaken gebeurt met XSLTs. In onderstaande stappen worden de transformaties via een command-line tool uitgevoerd, een schil om de Java tool Saxon. Via een XML editor zijn deze transformaties ook uit te voeren.
1. *Tip:* voor bepaalde modellen van Geonovum is geen standaard GML FeatureCollection of CityGML Model in gebruik. Zoals voor StUF modellen. Hiervoor zijn aparte TestObjectTypes gemaakt. Zie https://github.com/Geonovum/etf-stdtot/blob/master/Geonovum.md voor de TestObjectTypes.

Een nieuwe validator maken met XSD en geometrie validatie:
1. kopieer de directorystructuur van dit template en geef de directory ```xsdgeometry``` een logische naam van de validator. Gebruik bij voorkeur geen spaties.
1. Tip: maak een tekstdocument waarin je de UUIDs opslaat. Bijvoorbeeld het bestand UUID.md uit het template. Er komen er namelijk verschillende terug.
1. maak een Tag aan met de volgende stappen:
  1. ga naar de directory: ```include-metadata```
  1. genereer een UUID voor de bestandsnaam: https://www.uuidgenerator.net/version4. Bijvoorbeeld: ```XXX-XXX```
  1. hernoem het bestand dat begint met ```Tag-EID....xml``` naar: ```Tag-EIDXXX-XXX.xml```. Let op: laat dus ```Tag-EID``` staan in de bestandsnaam
  1. gebruik dezelfde UUID in het XML bestand ```Tag-EID...xml``` : geef dit op in het attribuut ```id="EID..."```, dus dat wordt dan ```id="EIDXXX-XXX"```
  1. pas de elementen voor label en description aan. Desgewenst ook de priority (voor volgorde van tests als een test object van hetzelfde resource type gebruikt wordt)

1. xsd + gml encoding validatie:
  1. ga naar de directory ```1_xsdgml```
  1. genereer een XSD + GML encoding BSX ETS bestand met XSLT, op basis van het eerder gegenereerde Tag-bestand en de TTB id:
  ```
  saxonb-xslt ../include-metadata/Tag-EIDf969a29a-0c58-4738-af67-2e17e894ef4d.xml /home/thijs/code/Geonovum/ETF/github/Geonovum_forks/etf-ets-repository/utils/etf-xsdandgmlencoding-bsxets.xsl schemaURL=http://register.geostandaarden.nl/xmlschema/imgeo/1.3/imgeo0302/horizontaal/imgeo0302_msg_horizontaal.xsd translationTemplateId=245c67e5-6d28-493e-9dc6-a23de3d81cc0 testObjectTypeId=e1d4a306-7a78-4a3b-ae2d-cf5f0810853e > xsdgmlencoding-bsxets.xml  
  ```
  1. noteer de UUID van de xsd validator, open het bestand xsdgmlencoding-bsxets.xml en noteer de id in het attribuut: /etf:ExecutableTestSuite@id (zonder EID)
1. gebruik als uuid voor de standaard GML geometrie test: ```6c675042-ef12-11e8-d217-09173f13e4c5``` of GML geometrie + SF GML ```06fb92cc-ef13-11e8-1038-09173f13e4c5``` en verwijder de directory  ```2_geometrytest```*of* maak een custom Geometrie test aan:
  1. ga naar de directory ```2_geometrytest```
  1. genereer obv Tag doc, TTB en testObjectType een BSX ETS (let op de bestandsnaam):
  ```
  saxonb-xslt ../include-metadata/Tag-EIDf969a29a-0c58-4738-af67-2e17e894ef4d.xml /home/thijs/code/Geonovum/ETF/github/Geonovum_forks/etf-ets-repository/utils/tag_2_etf_geometry_ets.xsl translationTemplateId=245c67e5-6d28-493e-9dc6-a23de3d81cc0 testObjectTypeId=e1d4a306-7a78-4a3b-ae2d-cf5f0810853e >
   geometrytest-bsxets.xml
  ```
1. maak een bestand voor de validators samen. Genereer een all-bsxets.xml. Ga naar de directory ```3_all``` en gebruik de volgende UUIDs in de parameters:
  1. tagid
  1. translationtemplateid: standaard TTB 245c67e5-6d28-493e-9dc6-a23de3d81cc0
  1. executableTestSuite 2 UUIDs van xsd en geometrytest
  1. (optioneel: testobjecttype, als anders dan GML FeatureCollection)
  1. Voorbeeld script:
  ```
  saxonb-xslt ../include-metadata/Tag-EIDafe2f9f2-e603-42b4-bd6f-dfa52243752e.xml /home/thijs/code/Geonovum/ETF/github/Geonovum_forks/etf-ets-repository/utils/etf-all-xsd-geometry.xsl dependencyIdXsd=3f674143-fd27-11e7-1863-09173f13e4c5 dependencyIdGeometry=6c675042-ef12-11e8-d217-09173f13e4c5 translationTemplateId=245c67e5-6d28-493e-9dc6-a23de3d81cc0 testObjectTypeId=e1d4a306-7a78-4a3b-ae2d-cf5f0810853e > all-bsxets.xml
  ```
