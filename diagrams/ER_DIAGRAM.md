# Entity-Relationship Diagram

## NextStop — Government Bus Tracking & Fleet Management System

---

## Unified Domain-Driven ER Diagram

This Entity-Relationship Diagram illustrates the database schema architecture for the **NextStop** system. It visualizes the core entities, their attributes, primary/foreign keys, and the relational mappings that connect the Fleet, Ticketing, Personnel, and Telemetry domains.

![ER Diagram](./er_diagram_new.png)

### Sub-System Domains Overview
The diagram natively groups into four logical domains:
1. **Fleet & Transit (Top/Center)**: Entities like `BUS`, `BUS_CAPACITY`, `ROUTE`, `STOP`, and `TRIP`.
2. **Operations & Personnel (Middle)**: Entities involving the staff such as `DRIVER`, `CONDUCTOR`, and administrative `ADMIN_USER`.
3. **Ticketing & Passengers (Bottom/Right)**: Entities focused on user experience like `APP_USER`, `USER_BOOKING`, `TICKET`, and `VOICE_QUERY`.
4. **Telemetry & Devices (Center/Left)**: Hardware and data logging tables like `DEVICE`, `HEARTBEAT`, and `OFFLINE_BATCH`.

---

## Entity Summary

### Core Entities (15 Tables)

| Entity           | Attributes | Primary Key  | Foreign Keys                          | Description                               |
|------------------|------------|--------------|---------------------------------------|-------------------------------------------|
| `BUS`            | 7          | `busId`      | —                                     | Main vehicle registry                     |
| `BUS_CAPACITY`   | 4          | `busId`      | `busId`                               | Bus seating and standing capacity tracking|
| `ROUTE`          | 5          | `routeId`    | —                                     | Pre-defined transit routes                |
| `STOP`           | 4          | —            | —                                     | Array of stops embedded in a Route        |
| `TRIP`           | 12         | `tripId`     | `busId, routeId, driverId, conductorId`| Lifecycle of a scheduled bus journey      |
| `DEVICE`         | 6          | `deviceId`   | `busId`                               | IoT tracking/Ticketing machine hardware   |
| `HEARTBEAT`      | 14         | `_id`        | `deviceId, busId, tripId`             | Real-time GPS/telemetry data logs         |
| `OFFLINE_BATCH`  | 8          | `_id`        | `deviceId`                            | Batch records synced from offline devices |
| `DRIVER`         | 8          | `driverId`   | `currentBusId`                        | Staff authorized to drive buses           |
| `CONDUCTOR`      | 8          | `conductorId`| `currentBusId`                        | Staff handling ticketing on buses         |
| `APP_USER`       | 5          | `userId`     | —                                     | Passengers using the mobile application   |
| `USER_BOOKING`   | 19         | `bookingId`  | `userId, routeId, tripId`             | Advance bookings created by app users     |
| `TICKET`         | 16         | `ticketId`   | `deviceId, tripId, routeId`           | Issued travel tickets on a trip           |
| `VOICE_QUERY`    | 19         | `queryId`    | `userId`                              | Multilingual voice requests from users    |
| `ADMIN_USER`     | 8          | `_id`        | —                                     | System administrators & dispatchers       |

---

## Relationship Summary

| Source Entity  | Target Entity | Relationship Type | Key Definition                            |
|----------------|---------------|-------------------|-------------------------------------------|
| `DEVICE`       | `BUS`         | Many-to-One       | `DEVICE.busId -> BUS.busId`               |
| `HEARTBEAT`    | `DEVICE`      | Many-to-One       | `HEARTBEAT.deviceId -> DEVICE.deviceId`   |
| `HEARTBEAT`    | `BUS`         | Many-to-One       | `HEARTBEAT.busId -> BUS.busId`            |
| `HEARTBEAT`    | `TRIP`        | Many-to-One       | `HEARTBEAT.tripId -> TRIP.tripId`         |
| `TRIP`         | `BUS`         | Many-to-One       | `TRIP.busId -> BUS.busId`                 |
| `TRIP`         | `ROUTE`       | Many-to-One       | `TRIP.routeId -> ROUTE.routeId`           |
| `TRIP`         | `DRIVER`      | Many-to-One       | `TRIP.driverId -> DRIVER.driverId`        |
| `TRIP`         | `CONDUCTOR`   | Many-to-One       | `TRIP.conductorId -> CONDUCTOR.conductorId`|
| `DRIVER`       | `BUS`         | Many-to-One       | `DRIVER.currentBusId -> BUS.busId`        |
| `CONDUCTOR`    | `BUS`         | Many-to-One       | `CONDUCTOR.currentBusId -> BUS.busId`     |
| `BUS_CAPACITY` | `BUS`         | One-to-One        | `BUS_CAPACITY.busId -> BUS.busId`         |
| `USER_BOOKING` | `APP_USER`    | Many-to-One       | `USER_BOOKING.userId -> APP_USER.userId`  |
| `USER_BOOKING` | `ROUTE`       | Many-to-One       | `USER_BOOKING.routeId -> ROUTE.routeId`   |
| `USER_BOOKING` | `TRIP`        | Many-to-One       | `USER_BOOKING.tripId -> TRIP.tripId`      |
| `TICKET`       | `DEVICE`      | Many-to-One       | `TICKET.deviceId -> DEVICE.deviceId`      |
| `TICKET`       | `TRIP`        | Many-to-One       | `TICKET.tripId -> TRIP.tripId`            |
| `TICKET`       | `ROUTE`       | Many-to-One       | `TICKET.routeId -> ROUTE.routeId`         |
| `OFFLINE_BATCH`| `DEVICE`      | Many-to-One       | `OFFLINE_BATCH.deviceId -> DEVICE.deviceId`|
| `VOICE_QUERY`  | `APP_USER`    | Many-to-One       | `VOICE_QUERY.userId -> APP_USER.userId`   |

---

## Notes

1. **Foreign Key (FK) Relations**: Entities establish relations using foreign keys to point back to primary identities. 
2. **Sub-document Pattern**: The `STOP` element, lacking a primary ID, represents an array sub-document embedded in `ROUTE` sequences. 
3. **Timestamps**: Most primary entities standardize on maintaining `createdAt` and `updatedAt` temporal attributes.
