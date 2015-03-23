#UMD Fedora 4

This is a maven project that overlays UMD's custom configuration files to the fcrepo-webapp and builds artifact ready for deployment to the umd server.

## Building & running fcrepo4 from source

System Requirements
* Java 7
* Maven 3

```bash
$ cd /apps/git
$ git clone https://github.com/mohideen/umdfcrepo4.git
$ cd fcrepo4
$ mvn install
$ MAVEN_OPTS="-Xmx512m" mvn jetty:run
```

**TODO**: Update github url after moving project to umd's github.

If you have problems building fcrepo4 with the above settings, you may need to also pass
options to the JaCoCo code coverage plugin:

```bash
$ MAVEN_OPTS="-Xmx1024m -XX:MaxPermSize=1024m" mvn -Djacoco.agent.it.arg="-XX:MaxPermSize=1024m -Xmx1024m" -Djacoco.agent.ut.arg="-XX:MaxPermSize=256m -Xmx1024m"  clean install
```


That's it! Your Fedora repository is up and running at: [http://localhost:8080/rest/](http://localhost:8080/rest/)

## Running the JMS Message Consumer and Indexers

Fedora 4 uses a JMS message consumer webapp to communicate with external services (indexers, triplestores, etc.). This webapp is distributed as a separate project from the Fedora 4 core, at https://github.com/fcrepo4/fcrepo-message-consumer

To set up these additional services, follow these steps:

1. Set up Fuseki
2. Set up Solr
3. Set up the Fedora 4 JMS Consumer

### Fuseki Triplestore (3030)

[Download](http://jena.apache.org/documentation/serving_data/#download-fuseki) a distribution tarball of Fuskei. At the time of writing, 1.1.1 is the latest version.

```bash
$ cd /apps
$ tar xvzf ~/Downloads/jena-fuseki-1.1.1-distribution.tar.gz
$ cd jena-fuseki-1.1.1
$ ./fuseki-server --update --mem /test
```

This will bring up a running instance of Fuseki on port 3030. Check <http://localhost:3030/> to verify. Note that this invocation does not persist the triples to disk.

### Solr (8983)

Set up your Fedora 4 following these instructions: <https://wiki.duraspace.org/display/FEDORA40/Solr+Indexing+Quick+Guide>

[Download](http://lucene.apache.org/solr/mirrors-solr-latest-redir.html) the latest Solr binary distribution. At the time of writing, 4.10.2 is the latest version.

```bash
$ cd /apps
$ tar xvzf ~/Downloads/solr-4.10.2.tgz
$ cd solr-4.10.2/example
$ java -jar start.jar
```

### JMS Message Consumer (9090)

We will be running the JMS message consumer on port 9090, so as not to conflict with the Fedora repository itself running on 8080.

To set up and run the JMS message consumer from source:

```bash
$ cd /apps/git
$ git clone https://github.com/fcrepo4/fcrepo-message-consumer
$ cd fcrepo-message-consumer
$ mvn install
$ cd fcrepo-message-consumer-webapp
$ MAVEN_OPTS="-Xmx750M -XX:MaxPermSize=300M" mvn -Djetty.port=9090 jetty:run
```

See also <https://wiki.duraspace.org/display/FEDORA40/Indexer+Configuration>