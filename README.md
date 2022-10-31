# SeaLiT-ResearchSpace

Semantic Search of maritime history data. Developed by FORTH-ICS in the context of SeaLiT (Seafaring Lives in Transition, Mediterranean Maritime Labour and Shipping, 1850s-1920s), an international research project funded by the ERC Starting Grant 2016, hosted at the Institute of Mediterranean Studies(FORTH-IMS). Built on the ResearchSpace platform.

## Built on the ResearchSpace platform

SeaLiT-ResearchSpace is a web application for searching/browsing SeaLiT semantic data produced from the transcription of maritime data from historical archives. The application is the result of a particular configuration of the ResearchSpace platform, for the needs of the SeaLiT project. ResearchSpace is an open source collaborative environment for humanities and cultural heritage research using knowledge representation and Semantic Web technologies, developed by the British Museum (see http://researchspace.org and https://github.com/researchspace/researchspace for details).

## SeaLiT-ResearchSpace deployment via Docker - How to use the provided file structure

To setup a new SeaLiT-ResearchSpace instance, go through the following steps:  
1. install docker in a ubuntu machine according to the guidelines: https://github.com/researchspace/researchspace/blob/master/README.md#setup-with-docker
2. install the ResearchSpace docker image provided by the British Museum.  
`docker pull researchspace/platform:3.3.0`  
3. install the Blazegraph docker image provided by the British Museum.  
`docker pull researchspacepublic/blazegraph:2.2.0-RC-2016_12_09-researchspace-geo`  
4. Copy the currently provided file structure in a <home> location.  
IMPORTANT: Set the owner of the folders `/apps` and `/runtime-data` to `systemd-timesync`  
`chown -R systemd-timesync <home>/runtime-data/data`  
`chown -R systemd-timesync <home>/runtime-data/config`  
`chown -R systemd-timesync <home>/runtime-data/ldp`  
5. First, run the Blazegraph container (here named as blazegraph_geo, running at port 10314)  
`docker run --name blazegraph_geo -d --restart=always -p 10314:8080 --env JAVA_OPTS="-Xmx4g" -v <home>/blazegraph-data:/blazegraph-data researchspacepublic/blazegraph:2.2.0-RC-2016_12_09-researchspace-geo`  
5.1. Test whether blazegraph runs properly by opening: http://<ip>:10314/blazegraph
6. Then, run the ResearchSpace container (here named as metaphactory_sealit, running at port 10315)  
`docker run --name metaphactory_sealit -d --restart=always -p 10315:8080 --link blazegraph_geo:blazegraph --volume <home>/apps:/apps -v  <home>/runtime-data/config:/runtime-data/config -v <home>/runtime-data/config/shiro.ini:/runtime-data/config/shiro.ini -v <home>/runtime-data/data:/runtime-data/data -v <home>/runtime-data/ldp:/runtime-data/ldp  --env JAVA_OPTS="" --env PLATFORM_OPTS="-Dconfig.environment.sparqlEndpoint=http://blazegraph:8080/blazegraph/sparql -Dlog4j.configurationFile=file:///var/lib/jetty/webapps/etc/log4j2.xml -Dconfig.environment.shiroAuthenticationFilter=authcBasic,anon,authc" researchspace/platform:3.3.0` 
6.1. Test whether ResearchSpace runs properly by opening: http://<ip>:10315
7. Import rdf data and all the trig files found in folder `materialized-data` into the blazegraph repository. 

### Current installation is based on ResearchSpace docker images:
- researchspace/platform:3.3.0 (https://hub.docker.com/r/researchspace/platform/tags)
- blazegraph:2.2.0-RC-2016_12_09-researchspace-geo (https://hub.docker.com/r/researchspacepublic/blazegraph/tags)

## Rdf data ingestion - Materialization
The materialization queries that produce the triples that support the Semantic Search functionality, can be dynamically produced by the next platform template:
http://rs.sealitproject.eu/resource/sealit:MaterializeRelations

## Running instance 
***http://rs.sealitproject.eu***


##### Useful docker commands
`docker container stop metaphactory_sealit`  
`docker rm --force metaphactory_sealit`  
`docker container stop blazegraph_geo`  
`docker rm --force blazegraph_geo`  
`docker container inspect metaphactory_sealit`  
"# SeaLiT-ResearchSpace" 
