# System Design Document: Uber Nearest Driver Matching

## 1. Overview
This document describes the system design for Uber’s driver-rider matching service, focusing on how the platform finds the nearest available driver when a rider requests a trip.

---

## 2. Requirements

### Functional Requirements
- Rider can request a trip from their current location.
- System must identify and assign the nearest available driver.
- Drivers must receive trip requests in real-time.
- Riders must be notified of driver assignment and ETA.

### Non-Functional Requirements
- **Low latency**: Matching should occur within seconds.
- **Scalability**: Handle millions of concurrent requests globally.
- **Reliability**: Ensure consistent driver assignment even under high load.
- **Accuracy**: Correctly calculate proximity using geospatial data.

---

## 3. High-Level Architecture

### Components
- **Rider App**: Sends trip request with GPS coordinates.
- **Driver App**: Continuously streams driver’s location and availability status.
- **Backend Services**:
  - **Request Handler**: Receives rider requests.
  - **Location Service**: Manages real-time driver locations.
  - **Matching Engine**: Finds nearest driver using geospatial queries.
  - **Notification Service**: Sends assignment updates to rider and driver.
- **Databases**:
  - **Real-Time Location Store** (e.g., Redis, Cassandra, or specialized geo-index DB).
  - **Persistent Store** for trip history and analytics.

---

## 4. Data Flow

1. Rider requests a trip → Rider App sends GPS coordinates to backend.
2. Backend Request Handler forwards request to Matching Engine.
3. Matching Engine queries Location Service for nearby drivers.
4. Location Service uses **geospatial indexing** (e.g., Haversine formula, KD-Tree, or Geohash) to find drivers within a radius.
5. Matching Engine applies filters:
   - Driver availability
   - Driver rating
   - Surge pricing zones
6. Nearest driver is selected and notified.
7. Rider receives driver details and ETA.

---

## 5. Key Design Considerations

### Location Updates
- Drivers send location updates every few seconds.
- Updates stored in **in-memory geo-indexed database** for fast retrieval.

### Geospatial Indexing
- Use **Geohash** or **QuadTree** to partition map into grids.
- Efficiently query drivers within a bounding box.
- Apply **Haversine formula** to calculate exact distance.

### Scalability
- Partition data by city/region.
- Use **Kafka** or **Pub/Sub** for streaming driver location updates.
- Horizontal scaling of Matching Engine.

### Fault Tolerance
- Replicate location data across multiple nodes.
- Retry mechanism for failed assignments.


### Sequence
1. Rider requests trip.
2. Backend forwards request.
3. Matching Engine queries Location Service.
4. Location Service returns nearest drivers.
5. Matching Engine selects best driver.
6. Driver App receives trip request.
7. Rider App receives driver details.

---

## 7. Technology Choices

- **Backend**: Java/Go/Python microservices
- **Databases**: Redis (real-time), Cassandra/Postgres (persistent)
- **Message Queue**: Kafka / Google Pub/Sub
- **Geospatial Indexing**: Geohash, Haversine formula
- **Load Balancing**: Nginx / Envoy

---

## 8. Future Enhancements
- Machine learning models for driver selection (considering traffic, driver acceptance rate, rider preferences).
- Predictive ETA calculation using historical traffic patterns.
- Dynamic surge pricing integration.

---

## 9. Conclusion
This design ensures low-latency, scalable, and reliable nearest-driver matching for Uber. By combining real-time location streaming, geospatial indexing, and efficient matching algorithms, the system can handle millions of concurrent requests globally.