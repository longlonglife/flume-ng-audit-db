# Apache Flume customizations for developing a secure and universal database auditing platform

A highly scalable, secure and central repository that stores consolidated audit data and optionally listener, 
alert and OS log events generated by the database instances. This central platform will be used for reporting, 
alerting and security policy management. The reports will provide a holistic view of activity across all databases 
and will include compliance reports, activity reports and privilege reports. The alerting mechanism will 
detect and alert on abnormal activity, potential intrusion and much more. As audit data is vital record of 
activity, to protect this information the central repository will reside outside of existing databases and most 
likely in Hadoop eco-system.

## Developed components

Several implementations have been made for adapting Flume, both in the source and sink side.

* AuditSource: a custom source which is able to collect audit data from database instances. This source needs a reader and a deserializer, some are already implemented:
    * Readers, create AuditEvents from audit data:
        * ReliableJdbcAuditEventReader (default): reads audit events from tables. It uses JDBC driver for connecting to database, so many types of databases are compatible. It provides a reliable way to read audit data from tables in order to avoid data loss.
    * Deserializer, convert AuditEvents into Flume Events:
        * JSONAuditEventDeserializer (default): generates events which body is a JSON with all fields contained in AuditEvent.
        * TextAuditEventDeserializer: generates events which body is a string with all fields contained in AuditEvent.
* For some sinks, you may need to implement a custom parser for parsing Flume Events to Avro records:
    * JSONtoAvroParser: for Kite sink, this parser converts Flume Events which body is JSON into Avro records.
    * JSONtoElasticSearchEventSerializer: for Elasticsearch sink, this parser converts Flume Events which body is JSON into Elasticsearch XContentBuilder.
    
## Configuration

### AuditSource

In order to use AuditSource as source in your Flume agent, you need to specify the type of agent source as:

```
<agent_name>.sources.<source_name>.type = ch.cern.db.audit.flume.source.AuditSource 
```

You do not need to specify a reader if you are going to use ReliableJdbcAuditEventReader since this one is the default. If you want to use other other reader use the following parameter.

```
<agent_name>.sources.<source_name>.reader = jdbc
```

If you want to develop a custom Reader, make sure that it implements ReliableEventReader and create a nested Builder class. To use your custom reader, you need to configure .reader parameter with the FQCN.

Deserializer needs also to be configured. Use below parameter with json or text value.

```
<agent_name>.sources.<source_name>.deserializer = [json|text] 
```

If you want to develop a custom deserializer, make sure that it implements AuditEventDeserializer and create a nested Builder class. To use your custom deserializer, you need to configure .deserializer parameter with the FQCN.












 
