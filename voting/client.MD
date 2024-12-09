# Voting

## Common to All Phases
A list of client messages that can be sent in any phase
### join
It is possible to join (rejoin) at any phase, if the player has a recovery key.
```
{
    "gameId": 15,
    "type": "join",
    "recovery": "cxeed81f6ko35cyya9l7043blukewu7awtafql12jpwwhri97v4apdxtgt7icqc0"
}
```
## Phase 0 (Introduction)
### player-is-ready
The player notifies the server when he/she is ready.
```
{
    "gameId": 15,
    "type": "player-is-ready"
}
```
## Phase 1 (Presentation)
## Phase 2 (Deliberation)
### chat-with-players
A player sends a message to a channel (identified by the list of its participants, including the sender)
```
{
    "gameId": 15,
    "type": "chat-with-players",
    "to": [
        2,
        3
    ],
    "text": "I don't know"
}
```
## Phase 3 (Request)
### compensation-request
A player sends a compensation request, the desired amount to let the project be developed.
```
{
    "gameId": 15,
    "type": "compensation-request",
    "compensationRequests": [
        null,
        155000
    ]
}
```
## Phase 4 (Offer)
### compensation-offer
The developer sends back a compensation offer, the same for all owners.
```
{
    "gameId": 15,
    "type": "compensation-offer",
    "compensationOffers": [
        null,
        123000
    ]
}
```
## Phase 5 (Deliberation)
A player sends a message to a channel (identified by the list of its participants, including the sender)
```
{
    "gameId": 15,
    "type": "chat-with-players",
    "to": [
        2,
        3
    ],
    "text": "I don't know"
}
```
## Phase 6 (Voting)
###
Owners cast a vote. The vote is on the condition index (0 in this case is the "no project" condition)
```
{
    "gameId": 15,
    "type": "compensation-decision",
    "compensationDecisions": [
        0
    ]
}
```
## Phase 7 (Results)
