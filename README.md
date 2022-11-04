# SeaLiT-ResearchSpace

SeaLiT-ResearchSpace is a web application for Semantic Search of maritime history data. Developed by FORTH-ICS in the context of _SeaLiT: Seafaring Lives in Transition, Mediterranean Maritime Labour and Shipping, 1850s-1920s_, an international research project funded by the ERC Starting Grant 2016, hosted at the Institute of Mediterranean Studies (FORTH-IMS). The application is built on the ResearchSpace platform.

## Built on the ResearchSpace platform

SeaLiT-ResearchSpace is the result of a particular configuration of the ResearchSpace platform, for the needs of the SeaLiT project. ResearchSpace is an open source collaborative environment for humanities and cultural heritage research using knowledge representation and Semantic Web technologies, developed by the British Museum (see http://researchspace.org and https://github.com/researchspace/researchspace for details).

## SeaLiT-ResearchSpace deployment via Docker - How to use the provided file structure

To setup a new SeaLiT-ResearchSpace instance, go through the following steps:  
1. Install docker in a ubuntu machine according to the guidelines: https://github.com/researchspace/researchspace/blob/master/README.md#setup-with-docker
2. Install the ResearchSpace docker image provided by the British Museum.  
`docker pull researchspace/platform:3.3.0`  
3. Install the Blazegraph docker image provided by the British Museum.  
`docker pull researchspacepublic/blazegraph:2.2.0-RC-2016_12_09-researchspace-geo`  
4. Copy the currently provided file structure in a <home> location.  
IMPORTANT: Set the owner of the folders `/apps` and `/runtime-data` to `systemd-timesync`  
`chown -R systemd-timesync <home>/runtime-data/data`  
`chown -R systemd-timesync <home>/runtime-data/config`  
`chown -R systemd-timesync <home>/runtime-data/ldp`  
5. Run the docker containers  
5.1. First, run the Blazegraph container (here named as blazegraph_geo, running at port 10314)  
`docker run --name blazegraph_geo -d --restart=always -p 10314:8080 --env JAVA_OPTS="-Xmx4g" -v <home>/blazegraph-data:/blazegraph-data researchspacepublic/blazegraph:2.2.0-RC-2016_12_09-researchspace-geo`  
5.2. Test whether blazegraph runs properly by opening: `http://<host-ip>:10314/blazegraph`  
5.3. Then, run the ResearchSpace container (here named as metaphactory_sealit, running at port 10315)  
`docker run --name metaphactory_sealit -d --restart=always -p 10315:8080 --link blazegraph_geo:blazegraph --volume <home>/apps:/apps -v  <home>/runtime-data/config:/runtime-data/config -v <home>/runtime-data/config/shiro.ini:/runtime-data/config/shiro.ini -v <home>/runtime-data/data:/runtime-data/data -v <home>/runtime-data/ldp:/runtime-data/ldp  --env JAVA_OPTS="" --env PLATFORM_OPTS="-Dconfig.environment.sparqlEndpoint=http://blazegraph:8080/blazegraph/sparql -Dlog4j.configurationFile=file:///var/lib/jetty/webapps/etc/log4j2.xml -Dconfig.environment.shiroAuthenticationFilter=authcBasic,anon,authc" researchspace/platform:3.3.0`  
5.4. Test whether ResearchSpace runs properly by opening: `http://<host-ip>:10315`  
6. Finally, import data and materialization into blazegraph. (See **Rdf data ingestion - Materialization**" below) 

## Login - Change the admin-user after installation  
The application is open to exploration to any user. Any user can build semantic queries and view the results. However to make any data or configuration changes, one must be an authenticated user. The admin-user is responsible for enabling user authentication by creating user accounts in the system through the `http://<host-ip>:10315/resource/Admin:Security` page. At first run, the admin-user can login the system with the default initial credentials (user: admin, pass: admin) and then password must be changed for security reasons.  

## Current installation and running instance  

Current installation is based on ResearchSpace docker images:
- researchspace/platform:3.3.0 (https://hub.docker.com/r/researchspace/platform/tags)
- blazegraph:2.2.0-RC-2016_12_09-researchspace-geo (https://hub.docker.com/r/researchspacepublic/blazegraph/tags)

You can visit the online application running at **http://rs.sealitproject.eu**

## Rdf data ingestion - Materialization  

A sample of data and the required materialized data are provided in `/sample-data` and `/materialized-data` respectively, serialized in trig format files. To import these into the configured blazegraph repository, you can apply either a blazegraph ingestion procedure or do that by using the Admin:DataImportExport page of the platform (`http://<host-ip>:10315/resource/Admin:DataImportExport`, login is required for this action).  
The materialized-data contain the appropriate semantic inferences (rdf data as well) in order to enable the Semantic Search functionality.
The materialization queries, that produce the materialized-data, can be dynamically produced through the platform template:
`http://<host-ip>:10315/resource/sealit:MaterializeRelations`.  
You can also view the queries produced into the online application in `http://rs.sealitproject.eu/resource/sealit:MaterializeRelations`. The resulted queries, shown on the page, can be copied manually and then executed in the sparql endpoint via your own ingestion bulk process.  
Finally, the materialization queries, can also be found into the `/materialization_queries.txt` file.



####### Useful docker commands  
`docker container stop metaphactory_sealit`  
`docker rm --force metaphactory_sealit`  
`docker container stop blazegraph_geo`  
`docker rm --force blazegraph_geo`  
`docker container inspect metaphactory_sealit`  
"# SeaLiT-ResearchSpace" 

## Acknowledgements ##

This work has received funding from the European Union's Horizon 2020 research and innovation programme under i) the European Research Council (ERC) grant agreement No 714437 ([Project SeaLiT](https://sealitproject.eu/)), and ii) the Marie Sklodowska-Curie grant agreement No 890861 ([Project ReKnow](https://reknow.ics.forth.gr/)).
