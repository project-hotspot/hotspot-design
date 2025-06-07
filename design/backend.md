# Backend Design Doc

## Components

The backend will be divided into several components:

### Ingestion Service

The mobile application will send gRPC requests to the ingestion service.
The ingestion service will be responsible for ingesting that data and
validating that the data comes from a mobile app. It should then
unpack the data and act as a producer, writing it to the ingestion Kafka topic for processing.

The ingestion service should be fast enough to support our requirement of real-time insights on packed places.

This will be written in Go.

An event will follow the schema

```go
type Event struct  {
  // timestamp in seconds since epoch
  // generated when the user's device sends its location
  timestamp int64
  // GPS string
  // latitude and longitude pairs
  location string
  // phone number
  user string
  // region, ie by country
  region string
}

```

### Kafka

Our Kafka cluster will be divided into the following topics:

- ingestion_events
- location_and_minute
- location_user_day

These topics will be partitioned based on geographic region in order to allow for horizontal scaling. Granularity will be determined as we scale.

Each Kafka event is just a key and value. The key is what Kafka uses to partition the event stream.

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

- location (count of how many people are in a location, for display on heatmap) and minute
- location and user, agg by day (the count at a location/user pair is how many minutes the user has spent at the location on that day)

To aggregate by location, we want to group GPS coordinates together if they fall in the same "grid space", defined as a lat, long of (ABC.DEFGHI0 - ABC.DEFGHI9, ABC.DEFGHI0 - ABC.DEFGHI9). We need to determine the correct grid size (decimal points). TODO

The service will take the aggregated data and determine:

- the number of devices in a location at a given time
- the locations that have the highest number of devices at a given time
- how much time a user has spent at a given location

We'll use Kafka Streams to do this. We'll write this in Java.

### Persistence

We will use Druid as our OLAP for persistence. It's easily queryable with DruidSQL and so we can
get our insights and location data in real-time.

TODO - see if druid can handle querying by time/date quickly

ideally so that we can query by how many people were in a location on a day

TODO - see if druid can handle some of the aggregation

## Deployment

The ingestion and processing services will be deployed as services on a Kubernetes cluster.

The Kafka cluster will also be deployed on the same Kubernetes cluster.
