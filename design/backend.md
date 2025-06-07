# Backend Design Doc

## Components

The backend will be divided into several components:

### Ingestion Service

The mobile application will send gRPC requests to the ingestion service.
The ingestion service will be responsible for ingesting that data and
validating that the data comes from a mobile app. It should then
unpack the data and act as a producer, writing it to the ingestion Kafka topic for processing.

The ingestion service should be fast enough to support our requirement of real-time insights on packed places.

### Kafka

Our Kafka cluster will be divided into the following topics:

- ingestion
- aggregated

These topics will be partitioned based on geographic region in order to allow for horizontal scaling. Granularity will be determined as we scale.

### Processing Service

The processing service will be responsible for processing the data
and generating the following insights:

- the number of devices in a location at a given time
  - fulfills requirement "how many people were at a location at a specific time"
- the locations that have the highest number of devices at a given time
  - fulfills requirement "what locations are popular"
- how much time a user has spent at a given location
  - fulfills requirement "where you spend your time"

The streaming service will operate on our data stream. It will use Kafka's streaming capabilities to take the real-time location data and aggregate it by:

- location and time
- location and user

The service will take the aggregated data and determine:

- the number of devices in a location at a given time
- the locations that have the highest number of devices at a given time
- how much time a user has spent at a given location

TODO - storage layer for how this stuff gets persisted, and also how this gets exposed to users interested in these insights

## Deployment

The ingestion and processing services will be deployed as services on a Kubernetes cluster.

The Kafka cluster will also be deployed on the same Kubernetes cluster.
