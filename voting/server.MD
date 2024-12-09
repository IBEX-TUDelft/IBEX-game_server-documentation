# Voting
A list of asynchronous events the server triggers and sends to the clients listening to its websocket in the form of json messages for the voting game.
## Common to All Phases
These messages are common to all phases, meaning that they can be issues at any time in all phases.
### phase-transition
The server has moved to the next phase
```
{
    "type": "phase-transition",
    "data": {
        "round": 0,
        "phase": 0
    }
}
```
### phase-instructions
Legacy: before the phase instructions were sent by server, now they are read from a static file. This message needs to be removed.
```
{
    "type": "phase-instructions",
    "data": {}
}
```
### ready-received
Just a feedback message after the player declares to be ready (by pressing the ready button in the UI, for example, but the agents must do so programmatically)
```
{
    "type": "ready-received",
    "data": {}
}
```
### set-timer
The way the server declares that the phase will end at a specific time (millis from epoch).
```
{
    "type": "set-timer",
    "data": {
        "end": 1733496994884
    }
}
```
### reset-timer
Informs that a previously activated timer is not active any more.
```
{
    "type": "reset-timer",
    "data": {}
}
```
## Phase 0 (Introduction)
### assign-role
Despite its humble name, all the initial private information is made known to a player with this message. This message is sent privately to each player after all of them have declared to be ready.
* property (as in real estate ownership): contains a lot of fancy information (including a legacy name), but the most important is the real value, included in the v section, one value per condition. 
* boundaries: value limits (low and high) for each property in each combination role/condition. The third condition (currently unused) has been removed from here.
* conditions: a description of the possible conditions
```
{
    "type": "assign-role",
    "data": {
        "role": 3,
        "property": {
            "id": 1,
            "owner": 1,
            "name": "Fascinating Goldfish Lot",
            "v": [
                562000,
                338000,
                150000
            ]
        },
        "boundaries": {
            "developer": {
                "noProject": {
                    "low": 200000,
                    "high": 500000
                },
                "projectA": {
                    "low": 500000,
                    "high": 2750000
                }
            },
            "owner": {
                "noProject": {
                    "low": 350000,
                    "high": 600000
                },
                "projectA": {
                    "low": 150000,
                    "high": 350000
                }
            }
        },
        "id": 0,
        "number": 1,
        "tag": "Owner 1",
        "conditions": [
            {
                "name": "No Project",
                "id": 0,
                "parameter": "no_project",
                "key": "noProject"
            },
            {
                "name": "Project",
                "id": 1,
                "parameter": "project_a",
                "key": "projectA"
            }
        ]
    }
}
```
### players-known
Some basic information about other players: most importantly their role and their number (for connecting later on)
```
{
    "type": "players-known",
    "data": {
        "players": [
            {
                "number": 1,
                "role": 3,
                "tag": "Owner 1"
            },
            {
                "number": 2,
                "role": 2,
                "tag": "Developer"
            },
            {
                "number": 3,
                "role": 3,
                "tag": "Owner 2"
            }
        ]
    }
}
```
## Phase 1 (Presentation)
During this short lived phase players are expected to have a look at the initial information available. This phase will expire automatically after a timer triggers the transition.
## Phase 2 (Deliberation)
### message-received
During this phase players exchange messages on different channels. Each channel is identified by its participants (as an array of ids). The messages are forwarded only to the participants.
```
{
    "type": "message-received",
    "data": {
        "sender": 1,
        "to": [
            2,
            3
        ],
        "number": 0,
        "text": "I don't know",
        "time": 1733497007316
    }
}
```
## Phase 3 (Request)
### compensation-request-received
A feedback message sent from the server to acknowledge it has received the compensation request.
Owner format:
```
{
    "type": "compensation-request-received",
    "data": {
        "compensationRequests": [
            null,
            155000
        ]
    }
}
```
### compensation-requests-received
This message is similar to the previous one but is intended for the developer, who must be able to see all compensation requests to prepare an offer. Notice the slight difference (in bold) in the names:
_compensation-request-received_ vs _compensation-request**s**-received_
```
{
    "type": "compensation-requests-received",
    "data": {
        "compensationRequests": [
            {
                "number": 1,
                "compensationRequests": [
                    null,
                    155000
                ]
            },
            {
                "number": 2
            },
            {
                "number": 3,
                "compensationRequests": [
                    null,
                    205000
                ]
            }
        ]
    }
}
```
## Phase 4 (Offer)
## Phase 5 (Deliberation)
## Phase 6 (Voting)
## Phase 7 (Results)
