---
sort: 1
---


# QUICK START

## LDES Server & LDES Client

This example focuses on both publishing and consuming a [Linked Data Event Stream](https://semiceu.github.io/LinkedDataEventStreams/) (LDES). We start by explaining how to setup an LDES server and publish data as an LDES, followed by the setup of the LDES client to replicate an LDES. In this example, the data examples are described with [OSLO](https://data.vlaanderen.be/) (the Flemish Interoperability Program) ontologies. The [GetStarted_VSDS](https://github.com/xdxxxdx/GetStarted_VSDS) on GitHub shows how to publish data using a FIWARE Smart Data model.

```note

This quick start example demonstrates only a small amount of the capabilities of the LDES Server. For more information about the LDES server, please consult the [LDES Server Manual](https://github.com/Informatievlaanderen/VSDS-LDESServer4J).

```

### Before starting

1.  Make sure [Docker](https://docker.com/) has been installed on your device.
2.  Local Ports _8080_, and _27017_ are accessible.
3.  The command script is written in [bash](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) code. Please modify it accordingly.

### Setup an LDES Server

- Create a local `docker-compose.yml` file with the content below.
```yaml
version: '3.3'
services:
   ldes-server:
     container_name: quick_start_ldes-server
     image: ghcr.io/informatievlaanderen/ldes-server:20230314T0913
     environment:
       - MANAGEMENT_TRACING_ENABLED=false
       - SIS_DATA=/tmp
       - SPRING_DATA_MONGODB_DATABASE=sample
       - LDES_COLLECTIONNAME=sample
       - LDES_MEMBERTYPE=https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection
       - SPRING_DATA_MONGODB_HOST=ldes-mongodb
       - SPRING_DATA_MONGODB_PORT=27017
       - LDES_HOSTNAME=http://localhost:8080
       - LDES_SHAPE=
       - VIEW_TIMESTAMPPATH=http://www.w3.org/ns/prov#generatedAtTime
       - VIEW_VERSIONOFPATH=http://purl.org/dc/terms/isVersionOf
       - VIEWS_0_FRAGMENTATIONS_0_NAME=pagination
       - VIEWS_0_NAME=by-page
       - VIEWS_0_FRAGMENTATIONS_0_CONFIG_MEMBERLIMIT=1
     ports:
       - 8080:8080
     networks:
       - ldes
     depends_on:
       - ldes-mongodb
   ldes-mongodb:
     container_name: quick_start_ldes-mongodb
     image: mongo:6.0.4
     ports:
       - 27017:27017
     networks:
       - ldes
networks:
   ldes:
       name: quick_start_network
```

-  Run `docker compose up` within the work directory of `.yml` file, to start the containers.
-  The LDES Server is now available at port `8080` and accepts members via `HTTP POST` requests.
- Browse to [http://localhost:8080/sample](http://localhost:8080/sample) to have a first look.

_The result should be as follow:_

```turtle
@prefix ldes:   <https://w3id.org/ldes#> .
@prefix rdf:    <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix sample: <http://localhost:8080/sample/> .
@prefix tree:   <https://w3id.org/tree#> .
<http://localhost:8080/sample>
        rdf:type   ldes:EventStream ;
        tree:view  sample:by-page .
sample:by-page  rdf:type  tree:Node .
```

### Add data to the LDES Server

-  Create a `sample.ttl` file with the following content:

```turtle
@prefix dc: <http://purl.org/dc/terms/> .
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix sosa: <http://www.w3.org/ns/sosa/> .
@prefix ns0: <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.> .
@prefix ns1: <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.> .
@prefix ns2: <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.> .
@prefix ns3: <https://schema.org/> .

<urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z>
  dc:isVersionOf <urn:ngsi-ld:WaterQualityObserved:woq:1> ;
  prov:generatedAtTime "2023-03-12T18:31:17.003Z"^^xsd:dateTime ;
  sosa:hasFeatureOfInterest "spt-00035-79" ;
  ns0:member <https://data.vmm.be/id/loc-00019-33>, [
    sosa:madeBySensor <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> ;
    ns1:result [ ns2:value [
        ns3:value 2.043000e+1 ;
        ns3:unitCode <https://data.vmm.be/id/CEL>
      ] ] ;
    ns1:phenomenonTime "2023-03-12T18:31:17.003Z"^^xsd:datetime ;
    ns1:observedProperty <https://data.vmm.be/concept/waterkwaliteitparameter/temperatuur> ;
    ns1:featureOfInterest <https://data.vmm.be/id/spt-00035-79> ;
    a <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement>
  ], [
    sosa:madeBySensor <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> ;
    ns1:result [ ns2:value [
        ns3:value 1442 ;
        ns3:unitCode <https://data.vmm.be/id/HP>
      ] ] ;
    ns1:phenomenonTime "2023-03-12T18:31:17.003Z"^^xsd:datetime ;
    ns1:observedProperty <https://data.vmm.be/concept/observatieparameter/hydrostatische-druk> ;
    ns1:featureOfInterest <https://data.vmm.be/id/spt-00035-79> ;
    a <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement>
  ], [
    sosa:madeBySensor <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> ;
    ns1:result [ ns2:value [
        ns3:value 6150 ;
        ns3:unitCode <https://data.vmm.be/id/G42>
      ] ] ;
    ns1:phenomenonTime "2023-03-12T18:31:17.003Z"^^xsd:datetime ;
    ns1:observedProperty <https://data.vmm.be/concept/waterkwaliteitparameter/conductiviteit> ;
    ns1:featureOfInterest <https://data.vmm.be/id/spt-00035-79> ;
    a <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement>
  ] ;
  a <https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection> .

<https://data.vmm.be/id/loc-00019-33> a <http://def.isotc211.org/iso19156/2011/SpatialSamplingFeature#SF_SpatialSamplingFeature> .
```

- Please run ```curl -X POST http://localhost:8080/sample -H "Content-Type: application/ttl" -d "@sample.ttl" ``` to post the `sample.ttl` to the LDES Server.

- The data is added to the LDES Server and part of the LDES.
- Browse to <http://localhost:8080/sample> to have a look.

_The result should be as follow:_

```turtle
@prefix ldes:   <https://w3id.org/ldes#> .
@prefix rdf:    <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix sample: <http://localhost:8080/sample/> .
@prefix tree:   <https://w3id.org/tree#> .

<http://localhost:8080/sample>
        rdf:type   ldes:EventStream ;
        tree:view  sample:by-page .

sample:by-page  rdf:type  tree:Node ;
        tree:relation  [ rdf:type   tree:Relation ;
                         tree:node  <http://localhost:8080/sample/by-page?pageNumber=1>
                       ] .
 ```      
The LDES view `by-page` now contains a relation to a fragment containing the LDES member described in `sample.ttl`. Follow the `tree:node` <http://localhost:8080/sample/by-page?pageNumber=1> to visit the first page of the LDES view <http://localhost:8080/sample/by-page>.

### Replicate an LDES with the LDES Client

- Create a `docker-compose.yml` file or extend the previous one with the following content:
```yaml
version: '3.3'
services:
      ldes-cli:
            image: ghcr.io/informatievlaanderen/ldes-cli:20230222T0959
            container_name: quick-start_ldes-client-cli
            command: "--url http://localhost:8080/sample/by-page --input-format text/turtle"
            network_mode: host
```

- Run `docker compose up ` to start the LDES Client docker container.
- The LDES Client will use the LDES view <http://localhost:8080/sample/by-page> to replicate LDES member and output them to the console of the `ldes-cli` docker container.

```
2023-03-12 21:48:00 [pool-1-thread-1] INFO be.vlaanderen.informatievlaanderen.ldes.client.cli.services.FragmentProcessor - Fragment http://localhost:8080/sample/by-page has 0 member(s)
   2023-03-12 21:48:00 [pool-1-thread-1] INFO be.vlaanderen.informatievlaanderen.ldes.client.cli.services.FragmentProcessor - Fragment http://localhost:8080/sample/by-page?pageNumber=1 has 1 member(s)
   2023-03-12 21:48:01 _:B1e68397745bc55c40e0c5eadca8646e0 <https://schema.org/unitCode> <https://data.vmm.be/id/HP> .
   2023-03-12 21:48:01 _:B1e68397745bc55c40e0c5eadca8646e0 <https://schema.org/value> "1442"^^<http://www.w3.org/2001/XMLSchema#integer> .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection> .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> <https://data.vmm.be/id/loc-00019-33> .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> _:Bf2d869f7dc0b939c5b6016ac7d1c451c .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> _:B505977c4390a72ab0da767101451604d .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> _:Bdc3ef60306183fa17844d907314f40ee .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://purl.org/dc/terms/isVersionOf> <urn:ngsi-ld:WaterQualityObserved:woq:1> .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://www.w3.org/ns/prov#generatedAtTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#dateTime> .
   2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://www.w3.org/ns/sosa/hasFeatureOfInterest> "spt-00035-79" .
   2023-03-12 21:48:01 _:B1e0a50c94effea51ea151d334d1d70a4 <https://schema.org/unitCode> <https://data.vmm.be/id/CEL> .
   2023-03-12 21:48:01 _:B1e0a50c94effea51ea151d334d1d70a4 <https://schema.org/value> "2.043000e+1"^^<http://www.w3.org/2001/XMLSchema#double> .
   2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement> .
   2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.featureOfInterest> <https://data.vmm.be/id/spt-00035-79> .
   2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.observedProperty> <https://data.vmm.be/concept/waterkwaliteitparameter/temperatuur> .
   2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.phenomenonTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#datetime> .
   2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.result> _:B0d2c29f506d81bb4b3ae1170f6b7ae96 .
   2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://www.w3.org/ns/sosa/madeBySensor> <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> .
   2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement> .
   2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.featureOfInterest> <https://data.vmm.be/id/spt-00035-79> .
   2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.observedProperty> <https://data.vmm.be/concept/observatieparameter/hydrostatische-druk> .
   2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.phenomenonTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#datetime> .
   2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.result> _:B8e72081a21073160d62ed708aed78a23 .
   2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://www.w3.org/ns/sosa/madeBySensor> <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> .
   2023-03-12 21:48:01 _:B8e72081a21073160d62ed708aed78a23 <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.value> _:B1e68397745bc55c40e0c5eadca8646e0 .
   2023-03-12 21:48:01 _:B22abd0fb7f46cf69b7367d7743860b0a <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.value> _:B66dbb43f4c55b6a44e931289145be554 .
   2023-03-12 21:48:01 <https://data.vmm.be/id/loc-00019-33> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/SpatialSamplingFeature#SF_SpatialSamplingFeature> .
   2023-03-12 21:48:01 <http://localhost:8080/sample> <https://w3id.org/tree#member> <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> .
   2023-03-12 21:48:01 _:B0d2c29f506d81bb4b3ae1170f6b7ae96 <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.value> _:B1e0a50c94effea51ea151d334d1d70a4 .
   2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement> .
   2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.featureOfInterest> <https://data.vmm.be/id/spt-00035-79> .
   2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.observedProperty> <https://data.vmm.be/concept/waterkwaliteitparameter/conductiviteit> .
   2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.phenomenonTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#datetime> .
   2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.result> _:B22abd0fb7f46cf69b7367d7743860b0a .
   2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://www.w3.org/ns/sosa/madeBySensor> <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> .
   2023-03-12 21:48:01 _:B66dbb43f4c55b6a44e931289145be554 <https://schema.org/unitCode> <https://data.vmm.be/id/G42> .
   2023-03-12 21:48:01 _:B66dbb43f4c55b6a44e931289145be554 <https://schema.org/value> "6150"^^<http://www.w3.org/2001/XMLSchema#integer> .
```


### Tear down the infrastructure and remove the volumes

Within the working directory, please run `docker rm -f $(docker ps -a -q)`


## LDES2Service

```note
***Please Note!***

_The purpose of the quick start try-out is solely to create _[Pagination fragmentation](https://github.com/Informatievlaanderen/VSDS-LDESServer4J/tree/main/ldes-fragmentisers/ldes-fragmentisers-pagination)_ for the self-generated data type to _[LDES format](https://semiceu.github.io/LinkedDataEventStreams/)_. To support other fragments, data types, or other features, e.g., retention, caching, etc., please consult _[LDES Server Manual](https://github.com/Informatievlaanderen/VSDS-LDESServer4J)_ for the configurations._

[Vlaamse Smart Data Space](https://www.vlaanderen.be/digitaal-vlaanderen/onze-oplossingen/vlaamse-smart-data-space)_ projects also provide methods for transforming data to *[LDES format](https://semiceu.github.io/LinkedDataEventStreams/)_, e.g. from _[NGSI-V2](https://vloca-kennishub.vlaanderen.be/NGSI-v2)* to *[NGSI-LD](https://en.wikipedia.org/wiki/NGSI-LD)_, from NGSI to _[OSLO](https://www.vlaanderen.be/digitaal-vlaanderen/onze-oplossingen/oslo)_ Model, from _[NGSI-V2](https://vloca-kennishub.vlaanderen.be/NGSI-v2)_ to _[LDES (LinkedDataEventStreams)](https://semiceu.github.io/LinkedDataEventStreams/).

```

```tip
Having trouble implementing this example? Please post your issue on the [VSDS Tech docs repo](https://github.com/Informatievlaanderen/VSDS-Tech-Docs/issues).

```


In this short example below, we show you how the components of the VSDS can be used to link multiple data streams across domains and systems using LDES. The whole story can be read here: [Real-Time Data Linkage via Linked Data Event Streams](https://pub.towardsai.net/real-time-data-linkage-via-linked-data-event-streams-e1aab3090b40)

### Before starting


1.  [Docker](https://docker.com/) has been installed on your device.
2.  Local Ports 8443 and 7200 are accessible.

### Start your LDES workbench


1.  Create a local [docker-compose.yml](https://github.com/samuvack/ldes-grar/blob/main/docker-compose.yml) file with the following content, or you could use the current good-to-go [GetStarted GRAR project](https://github.com/samuvack/ldes-grar):

```yaml
version: ‘3’
volumes:
  nifi-database-repository:
  nifi-flowfile-repository:
  nifi-content-repository:
  nifi-provenance-repository:
  nifi-state:
  nifi-logs:
  nifi-nars:
  nifi-conf:
networks:
  front-tier:
    driver: bridge
  back-tier:
    driver: bridge
services:
  nifi:
    image: ghcr.io/informatievlaanderen/ldes-workbench-nifi:20230214T123440 #20221109T103745
    container_name: nifi-graph
    restart: unless-stopped
    ports:
      # HTTPS
      - 8443:8443/tcp
    volumes:
      - ./nifi-extensions:/opt/nifi/nifi-current/extensions
    environment:
      - NIFI_UI_PORT=8443
      - SINGLE_USER_CREDENTIALS_USERNAME=admin
      - SINGLE_USER_CREDENTIALS_PASSWORD=admin123456789
      - NIFI_WORKFLOW_LISTEN_PORT=9005
      - NIFI_JVM_HEAP_INIT=8g
      - NIFI_JVM_HEAP_MAX=8g
  graphdb:
    image: ontotext/graphdb:10.0.2
    container_name: graphdb3
    ports:
      # HTTP
      - 7200:7200
```

To begin, the command for launching GraphDb and Apache NiFi within a docker container is as follows:

```source-shell
docker compose up --build
```
Apache Nifi runs on port 8443:8443/tcp :[https://localhost:8443](https://localhost:8443/)\
GraphDB runs on port 7200 :7200/tcp : [http://localhost:7200](http://localhost:7200/)

A data flow can be built up by dragging in building blocks in the graphical user interface of Apache NiFi. This docker container contains all the components out the latest version of the LDES workbench (e.g., latest LDES Client, LDES server, etc.). Every data stream can be transformed into a Linked Data Event Stream with a conversion data flow.

### Starting from a published [LDES](https://semiceu.github.io/LinkedDataEventStreams/)


In this example, we start from published LDES

```turtle
@prefix conceptscheme: <https://data.vlaanderen.be/id/conceptscheme/> .
@prefix generiek:      <https://data.vlaanderen.be/ns/generiek#> .
@prefix geosparql:     <http://www.opengis.net/ont/geosparql#> .
@prefix ldes:          <https://w3id.org/ldes#> .
@prefix locn:          <https://www.w3.org/ns/locn#> .
@prefix prov:          <http://www.w3.org/ns/prov#> .
@prefix rdf:           <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix relation:      <http://www.iana.org/assignments/relation/> .
@prefix terms:         <http://purl.org/dc/terms/> .
@prefix tree:          <https://w3id.org/tree#> .
https://data.vlaanderen.be/id/adres/1658513
        relation:self  "https://api.basisregisters.staging-vlaanderen.be/v2/adressen/1658513" .
[ https://data.vlaanderen.be/ns/gebouw#bestaatUit
          <https://data.vlaanderen.be/id/gebouweenheid/17631816/2022-06-27T16:23:49+02:00> ;
  generiek:lokaleIdentificator  "17631524"] .
[ https://data.vlaanderen.be/ns/gebouw#bestaatUit
<https://data.vlaanderen.be/id/gebouweenheid/6311633/2022-06-27T16:23:48+02:00> ;  generiek:lokaleIdentificator  "6310652"] .
https://data.vlaanderen.be/id/gebouweenheid/14539518/2022-06-27T16:23:32+02:00
   rdf:type                      <https://data.vlaanderen.be/ns/gebouw#Gebouweenheid> ;
        terms:isVersionOf             <https://data.vlaanderen.be/id/gebouweenheid/14539518> ;        prov:generatedAtTime          "2022-06-27T16:23:32+02:00"^^<http://www.w3.org/2001/XMLSchema#dateTime> ;
<https://data.vlaanderen.be/ns/gebouw#Gebouweenheid.adres>                <https://data.vlaanderen.be/id/adres/5162364> ;        <https://data.vlaanderen.be/ns/gebouw#Gebouweenheid.geometrie>
                [ conceptscheme:geometriemethode  conceptscheme:aangeduidDoorBeheerder ;                  locn:geometry                   [ geosparql:asGML                                    "<gml:Point srsName=\"http://www.opengis.net/def/crs/EPSG/0/31370\" xmlns:gml=\"http://www.opengis.net/gml/3.2\"><gml:pos>200647.71 210393.46</gml:pos></gml:Point>"^^geosparql:gmlLiteral ]                ] ;        <https://data.vlaanderen.be/ns/gebouw#Gebouweenheid.status>
                conceptscheme:gerealiseerd ;       <https://data.vlaanderen.be/ns/gebouw#functie>                conceptscheme:nietGekend ;
        generiek:lokaleIdentificator  "14539518" ;        generiek:naamruimte           "https://data.vlaanderen.be/id/gebouweenheid" ;        generiek:versieIdentificator  "2022-06-27T16:23:32+02:00" .
<https://data.vlaanderen.be/id/adres/2213034>        relation:self  "https://api.basisregisters.staging-vlaanderen.be/v2/adressen/2213034" .
```


### Consuming an LDES


For this purpose, the three Linked Data Event streams are stored in a [GraphDB](https://graphdb.ontotext.com/) to facilitate efficient and effective data consumption. GraphDB supports complex semantic queries and inference, making discovering meaningful relationships between different data sources possible.

![Afbeelding met grafiek Automatisch gegenereerde beschrijving](../images/LdesToGrapDB.PNG)

1. Start Apache NiFi on localhost:8433

2. Import data flow by importing [Apache NiFi configuration file](https://raw.githubusercontent.com/samuvack/ldes-grar/main/NiFi_Flow.json). See [this site for more info](https://www.alasdairb.com/posts/importing-individual-flow-definitions-in-nifi/)

3. Add [LDES endpoint](https://grar.smartdataspace.dev-vlaanderen.be/building-units/by-page) in the LDES client

4. Start Apache NiFi data flow

![](../images/exampleTwoFlow.png)

5. Open GraphDB on localhost:7200/sparql

6. Run a semantic query with SPARQL

![](../images/GrapDB.png)

Below you find an example of a SPARQL query. This query returns triples with information about the parcel LDES.

```turtle
PREFIX adres: <https://data.vlaanderen.be/id/adres/>
PREFIX gebouw: <https://data.vlaanderen.be/id/?gebouweenheid/>
PREFIX gebouweenheid: <https://data.vlaanderen.be/ns/gebouw#>
PREFIX prov: <http://www.w3.org/ns/prov#>
PREFIX generiek: <https://data.vlaanderen.be/ns/generiek#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
construct {
?gebouweenheid prov:generatedAtTime ?generatedAtTime .
?gebouweenheid rdf:type ?type .?gebouweenheid gebouweenheid:Gebouweenheid.geometrie ?geometrie .
?gebouweenheid gebouweenheid:Gebouweenheid.status ?status .
?gebouweenheid gebouweenheid:functie ?functie .
?gebouweenheid generiek:lokaleIdentificator ?lokaleIdentificator .
?gebouweenheid generiek:naamruimte ?naamruimte .
?gebouweenheid generiek:versieIdentificator ?versieIdentificator .  
} where { 
	?gebouweenheid <https://data.vlaanderen.be/ns/gebouw#Gebouweenheid.adres> <https://data.vlaanderen.be/id/adres/1864311> .
    OPTIONAL{
    ?gebouweenheid prov:generatedAtTime ?generatedAtTime .
	?gebouweenheid rdf:type ?type .
	?gebouweenheid gebouweenheid:Gebouweenheid.geometrie ?geometrie .
	?gebouweenheid gebouweenheid:Gebouweenheid.status ?status .
	?gebouweenheid gebouweenheid:functie ?functie .
	?gebouweenheid generiek:lokaleIdentificator ?lokaleIdentificator .
	?gebouweenheid generiek:naamruimte ?naamruimte .
	?gebouweenheid generiek:versieIdentificator ?versieIdentificator .  
}}
```
This SPARQL query returns multiple triples with information about building units. These triples can then be converted into an RDF file.

The RDF file should be as follow:
```turtle
@prefix ns1: <https://data.vlaanderen.be/ns/generiek#> .
@prefix ns2: <https://data.vlaanderen.be/ns/gebouw#> .
@prefix ns3: <http://www.w3.org/ns/prov#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
<https://data.vlaanderen.be/id/gebouweenheid/14635685> a ns2:Gebouweenheid ;
    ns3:generatedAtTime "2022-06-27T20:51:02+02:00"^^xsd:dateTime ;    ns2:Gebouweenheid.geometrie [ ] ;    ns2:Gebouweenheid.status <https://data.vlaanderen.be/id/conceptscheme/gerealiseerd> ;    
ns2:functie <https://data.vlaanderen.be/id/conceptscheme/nietGekend> ;    ns1:lokaleIdentificator "14635685" ;
ns1:naamruimte "https://data.vlaanderen.be/id/gebouweenheid" ;    ns1:versieIdentificator "2022-06-27T20:51:02+02:00" .
```


