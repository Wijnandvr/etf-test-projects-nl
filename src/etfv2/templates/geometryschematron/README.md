# Aanmaken validator XSD + schematron tests
## Goed te weten vooraf
1. de validator maakt gebruik van generieke XQuery-code (2 bestanden) voor de verwerking. De verwijzing hiernaar wordt autmatisch opgenomen, het is dus niet nodig hier nog iets voor te doen. Wel is het belangrijk dat deze code staat in de directory ../../projects/generic/. Het gaat om de bestanden:
  1. ets-generic-bsxets.xq
  1. testquery.xq
  1. schematron-bsxets.xq
  1. schematron-query-prolog.xq
1. het aanmaken gebeurt met XSLTs. In onderstaande stappen worden de transformaties via een command-line tool uitgevoerd, een schil om de Java tool Saxon. Via een XML editor zijn deze transformaties ook uit te voeren.
1. *Tip:* voor bepaalde modellen van Geonovum is geen standaard GML FeatureCollection of CityGML Model in gebruik. Zoals voor StUF modellen. Hiervoor zijn aparte TestObjectTypes gemaakt. Zie https://github.com/Geonovum/etf-stdtot/blob/master/Geonovum.md voor de TestObjectTypes.

## Stappen
Een nieuwe validator maken met geometrie en schematron validatie gaat volgens de volgende stappen:
1. kopieer de directorystructuur van dit template en geef de directory ```geometryschematron``` een logische naam van de validator. Gebruik bij voorkeur geen spaties.
1. Tip: maak een tekstdocument waarin je de UUIDs opslaat. Bijvoorbeeld het bestand UUID.md uit het template. Er komen er namelijk verschillende terug.
1. maak een Tag aan met de volgende stappen:
  1. ga naar de directory: ```include-metadata```
  1. genereer een UUID voor de bestandsnaam: https://www.uuidgenerator.net/version4. Bijvoorbeeld: ```XXX-XXX```
  1. hernoem het bestand dat begint met ```Tag-EID....xml``` naar: ```Tag-EIDXXX-XXX.xml```. Let op: laat dus ```Tag-EID``` staan in de bestandsnaam
  1. gebruik dezelfde UUID in het XML bestand ```Tag-EID...xml``` : geef dit op in het attribuut ```id="EID..."```, dus dat wordt dan ```id="EIDXXX-XXX"```
  1. pas de elementen voor label en description aan. Desgewenst ook de priority (voor volgorde van tests als een test object van hetzelfde resource type gebruikt wordt)
1. Maak een Translation Template Bundle o.b.v. de Schematron file:
  1. ga naar de directory: ```include-metadata```
  1. Translation Template Bundle (TTB) aanmaken (met zelf gegenereerde TTB id: een nieuwe UUID via https://www.uuidgenerator.net/version4, bijv 2c4eb556-265c-4bb6-982f-a5574ae3252b in het voorbeeld hieronder). Let op! gebruik een testObjectTypeId uit de lijst met IDs van http://docs.etf-validator.net/v2.0/Developer_manuals/Developing_Executable_Test_Suites.html#basex-test-object-types. Bijvoorbeeld voor GML: e1d4a306-7a78-4a3b-ae2d-cf5f0810853e (ZONDER EID in het script voor de TTB!)
    1. Let op de bestandsnaam: die moet dezelfde id bevatten als translationTemplateId bij het aanroepen van de XSL
    1. Code voor genereren TTB xml bestand:
    ```
    saxonb-xslt /home/thijs/code/Geonovum/ETF/github/etf-test-projects-nl/src/data/GML3.2\ SF2/schematron.sch /home/thijs/code/Geonovum/ETF/github/Geonovum_forks/etf-ets-repository/utils/schematron_2_etf_translation_template_bundle.xsl testObjectTypeId=e1d4a306-7a78-4a3b-ae2d-cf5f0810853e translationTemplateId=2c4eb556-265c-4bb6-982f-a5574ae3252b > TranslationTemplateBundle-EID2c4eb556-265c-4bb6-982f-a5574ae3252b.xml
    ```
1. Geometrie test:
  1. ga naar de directory ```1_geometrytest```
  1. genereer obv Tag doc, TTB (Nota bene: dit is dezelfde TTB voor alle geometrytests 245c67e5-6d28-493e-9dc6-a23de3d81cc0, gebruik een andere UUID dan de TTB die hierboven is gemaakt) en testObjectType een BSX ETS (let op de bestandsnaam):  
  ```
  saxonb-xslt ../include-metadata/Tag-EIDf969a29a-0c58-4738-af67-2e17e894ef4d.xml /home/thijs/code/Geonovum/ETF/github/Geonovum_forks/etf-ets-repository/utils/tag_2_etf_geometry_ets.xsl translationTemplateId=245c67e5-6d28-493e-9dc6-a23de3d81cc0 testObjectTypeId=e1d4a306-7a78-4a3b-ae2d-cf5f0810853e >  geometrytest-bsxets.xml
  ```
  1. noteer de UUID van deze gegenereerde test. De UUID staat in het ..bsxets.xml bestand in de root-tag, bij id="EID...". De UUID is zonder EID ervoor.
1. Schematron,
  1. ga naar de directory ```2_schematrontest```
  1. genereer obv schematron, TagID, TTB en testObjectType een BSX ETS (let op de bestandsnaam):
  ```  
  saxonb-xslt /home/thijs/code/Geonovum/ETF/github/etf-test-projects-nl/src/data/GML3.2\ SF2/schematron.sch /home/thijs/code/Geonovum/ETF/github/Geonovum_forks/etf-ets-repository/utils/schematron_2_etf_ets.xsl tagId=f969a29a-0c58-4738-af67-2e17e894ef4d translationTemplateId=2c4eb556-265c-4bb6-982f-a5574ae3252b testObjectTypeId=e1d4a306-7a78-4a3b-ae2d-cf5f0810853e >  schematron-bsxets.xml
  ```
  1. noteer de UUID van deze gegenereerde test. De UUID staat in het ..bsxets.xml bestand in de root-tag, bij id="EID...". De UUID is zonder EID ervoor.
1. maak een bestand voor de validators samen. Genereer een all-bsxets.xml. Ga naar de directory ```3_all``` en gebruik de volgende UUIDs in de parameters:
  1. tagid
  1. translationtemplateid
  1. executableTestSuite 2 UUIDs van xsd en schematron
  1. (optioneel: testobjecttype)
  1. Voorbeeld commando:
  ```
  saxonb-xslt ../include-metadata/Tag-EIDafe2f9f2-e603-42b4-bd6f-dfa52243752e.xml /home/thijs/code/Geonovum/ETF/github/Geonovum_forks/etf-ets-repository/utils/etf-all-geometry-schematron.xsl dependencyIdGeometry=3f674143-fd27-11e7-1863-09173f13e4c5 dependencyIdSchematron=050e4572-fd28-11e7-d212-09173f13e4c5 translationTemplateId=13837fe8-eb23-4421-b2cb-1e2704bf618b testObjectTypeId=e1d4a306-7a78-4a3b-ae2d-cf5f0810853e > all-bsxets.xml
  ```
