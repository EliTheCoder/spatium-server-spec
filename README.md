# spatium-server-spec
The specification for the spatium server version 1.1

## Format
Data payloads will be sent as serialized JSON objects.
Each object will have the following properties
| Field | Type             | Description
|-------|------------------|-----------------------------
| `o`   | `integer.letter` | The opcode for this payload
| `d`   | `?object`        | The data for this payload
| `s`   | `?integer`       | The sequence number for this payload. The sequence number increases by 1 each time a payload is sent by the server and starts with `Opcode 1.b handshake` at 0. This is not required for serverbound payloads.

## Opcodes
| Opcode | Name      | Direction | Data                                                    | Description                                                                  |
| ------ | --------- | --------- | ------------------------------------------------------- | ---------------------------------------------------------------------------- |
| 0.a    | heartbeat | C2S       |                                                         | Sent every 1000 milliseconds after the handshake.                            |
| 0.b    | heartbeat | S2C       |                                                         | Sent as an immediate response to 0.a.                                        |
| 1.a    | handshake | C2S       | `{username: string, version: string, team?: number}`    | First payload sent by the client to establish authentication.                |
| 1.b    | handshake | S2C       | `{success: boolean, data?: BoardData, team?: number}`   | Sent as an immediate response to 1.a. Returns the success of the handshake.  |
| 2.a    | ping      | C2S       |                                                         | Used to check the ping of the server between heartbeats.                     |
| 2.b    | ping      | S2C       |                                                         | Sent as an immediate response to 2.a.                                        |
| 3.a    | move      | C2S       | `{move: Move}`                                          | Used to make a move on the board.                                            |
| 3.b    | move      | S2C       | `{success: boolean}`                                    | Sent as an immediate response to 3.a. Returns the success of the move.       |
| 3.c    | move      | S2C       | `{move: Move, team: number}`                            | Sent when an opponent player makes a move.                                   |
| 4.a    | state     | C2S       |                                                         | Used to check the current state of the game.                                 |
| 4.b    | state     | S2C       | `{data: BoardData}`                                     | Sent as an immediate response to 4.a. Returns the state of the board.        |
| 5.a    | draw      | C2S       |                                                         | Used to offer a draw.                                                        |
| 5.b    | draw      | S2C       | `{success: boolean}`                                    | Sent as an immediate response to 5.a. Returns the success of the draw offer. |
| 5.c    | draw      | S2C       |                                                         | Sent when an opponent player offers a draw or accepts a draw offer.          |
| 5.d    | undraw    | C2S       |                                                         | Used to cancel a draw offer or decline a draw offer.                         |
| 5.e    | undraw    | S2C       | `{success: boolean}`                                    | Sent as an immediate response to 5.d. Returns the success of the undraw.     |
| 5.f    | undraw    | S2C       |                                                         | Sent when an opponent cancels a draw offer or declines a draw offer.         |
| 6.a    | resign    | C2S       |                                                         | Used to resign the game.                                                     |
| 6.b    | resign    | S2C       |                                                         | Sent when an opponent resigns the game.                                      |

## GameState
`GameState` is an object that contains all of the data of the game's state. It has the following format.
```
{
  id: string,
  status: number, // 0 for waiting, 1 for playing, 2 for finished
  initialState: string,
  moves: Move[]
}
```

## Example
```ts
let payload = {
  o: "1.b",
  s: 0,
  d: {
    success: true,
    data: {
      id: "uCgUx",
      status: 0,
      initialState: "pppp/pppp",
      moves: []
    }
  }
}

socket.send(JSON.stringify(payload));
```
