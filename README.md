# spatium-server-spec
The specification for the spatium server

## Format
Data payloads will be sent as serialized JSON objects.
Each object will have the following properties
| Field | Type             | Description
|-------|------------------|-----------------------------
| `o`   | `integer.letter` | The opcode for this payload
| `d`   | `?object`        | The data for this payload

## Opcodes
| Opcode | Name        | Direction | Data                                                                 | Description                                                                                                                                                               |
| ------ | ----------- | --------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.a    | heartbeat   | C2S       |                                                                      | Sent every 1000 milliseconds after the handshake.                                                                                                                         |
| 0.b    | heartbeat   | S2C       |                                                                      | Sent as an immediate response to 0a.                                                                                                                                      |
| 1.a    | handshake   | C2S       | {username: string}                                                   | First payload sent by the client to establish authentication.                                                                                                             |
| 1.b    | handshake   | S2C       | {success: boolean}                                                   | Sent as an immediate response to 1a. Returns the success of the handshake.                                                                                                |
| 2.a    | ping        | C2S       |                                                                      | Used to check the ping of the server between heartbeats.                                                                                                                  |
| 2.b    | ping        | S2C       |                                                                      | Sent as an immediate response to 2a.                                                                                                                                      |
| 3.a    | subscribe   | C2S       | {events: string\[\]}                                                 | Used to subscribe to specified events.                                                                                                                                    |
| 3.b    | subscribe   | S2C       | {events: string\[\], unsucess?: {event: string, reason: string}\[\]} | Sent as an immediate response to 3a. Returns the list of events that the client is currently subscribed to. Returns the list of events that weren't able to be subscribed |
| 4.a    | unsubscribe | C2S       | {events: string\[\]}                                                 | Used to unsubscribe to specified events.                                                                                                                                  |
| 4.b    | unsubscribe | S2C       | {events: string\[\], unsucess?: {event: string, reason: string}\[\]} | Same as 3.b but for unsubscribing                                                                                                                                         |
| 5.a    | event       | S2C       | {event: string, data: object}                                        | Used to notify the client of subscribed events                                                                                                                            |
