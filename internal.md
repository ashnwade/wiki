# Gravwell Internal Logging and the Gravwell Tag

Gravwell self-ingests logs from Gravwell indexers, webservers, search agents, and ingesters. All self-ingested logs are ingested into the `gravwell` tag, and are formatted as RFC5424 syslog messages. 

Gravwell publishes the "Gravwell Kit" that uses self-ingested logs as a datasource for monitoring the health and activity of a Gravwell deployment.

## Example Gravwell Internal Logging Entry

```
<12>1 2023-09-28T17:06:10.216454-04:00 38bde4205821 webserver 400000ff webserver/login.go:317 [gw@1 sessionid="36ff77e176" host="192.168.1.122" uid="1" user="admin"] User logged in
```

The above syslog message represents a self-ingested Gravwell log message. It contains the timestamp, application name, a message, and optionally one or more key/value pairs. In this example, the webserver logged that a user has logged in. The key/value pairs indicate the username (admin), UID (1), and the host IP of the client that logged in. 

In addition, logged messages contain the file and line number of the source code that generated the log message. This information can be useful when working with Customer Support.

## Ingester Logging

In addition, Gravwell ingesters will also create logs in the gravwell tag. They follow the same format outlined above, and carry ingester-specific information in the message body. For example, simple relay logs information about it's connection status and connected clients.

```
<14>1 2023-09-28T17:15:13.895016-04:00 38bde4205821 indexer 200500cc ingestRoutines.go:428 [gw@1 ingester="simplerelay" ingesterversion="5.4.0" ingesteruuid="1c7b0528-7123-4b9f-8608-67874c5a993d" conntype="Unix" client="unix://@"] Ingest routine successfully initialized and prepared to receive entries
```

In the example above, simple relay is logging it's ingest status, along with the version, application name, UUID, and connection type.

## Security Implications

Logs generated by Gravwell never contain Gravwell-specific sensitive information, such as user passwords, keys, or the results of queries. However, Gravwell does log the queries themselves (for example: `tag=data json foo`). If your query contains sensitive information in a filter, for example, that filter text will be logged. 

```gravwell
tag=ssns json username ssn == "123-45-6789"
```

The gravwell tag can be restricted by using [CBAC](/cbac/cbac) based tag restrictions. 
