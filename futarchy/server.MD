# Futarchy Server Messages
Here listed is the set of events (divided by phases) sent by the server to the players. These messages are asynchronous and players are expected to react upon them, choosing an action available to them in that specific phase. Keep also in mind that different players have different roles, and their possible actions (within a phase) may differ.
Some events can be received in any phase, some are phase specific. Notice that in Futarchy both conditions will remain active until the market phase is complete.

## All Phases
### phase-transition
All players are informed that a new phase (and possibly round) has begun with this message. It is received at the beginning of each phase.
```
{
    "type": "phase-transition",
    "data": {
        "round": 1,
        "phase": 0
    }
},
```
### phase-instructions
Legacy: ignore. The phase instructions are now fetched from a static file.

```
{}
```
### set-timer
Informs the player that the current phase has a timer, and it will end at a precise moment (expressed millis since epoc). It should create some pressure on the player.
```
{
        "type": "set-timer",
        "data": {
            "end": 1733481461657
        }
}
```
### reset-timer
Informs when a timer is not longer necessary.
```
{
    "type": "reset-timer",
    "data": {}
},
```
## Phase 0
### ready-received
After a player click "ready", the server acknowledges it has received the status update.
```
{
    "type": "ready-received",
    "data": {}
}
```
### assign-role
After everyone has clicked ready, the roles are assigned. This message contains much more than that. Most player properties have one value for each condition: notice that there are 3 possible conditions, but only 2 are currently used for simplicity.
* wallet: for each condition there is a balance, share and cash for speculating
* property (as in real estate ownership): contains a lot of fancy information (including a legacy name), but the most important is the real value, included in the v section, one value per condition. The field d indicates the last declaration (per condition) and speculators is a list of speculators who acted against the player (per condition) the last time.
* boundaries: value limits (low and high) for each property in each combination role/condition. The third condition has been removed from here, speculators have no property ownership.
* taxRate: the current tax rate. It is set to the initial or the final tax rate value.
* initialTaxRate: the tax rate applied during the first declaration phase
* finalTaxRate: the tax rate applied during the second declaration phase
* conditions: a description of the possible conditions
```
{
                "type": "assign-role",
                "data": {
                    "role": 3,
                    "wallet": [
                        {
                            "balance": 50000,
                            "shares": 7,
                            "cashForSniping": 250000
                        },
                        {
                            "balance": 60000,
                            "shares": 6,
                            "cashForSniping": 250000
                        },
                        {
                            "balance": 60000,
                            "shares": 6,
                            "cashForSniping": 250000
                        }
                    ],
                    "property": {
                        "id": 1,
                        "owner": 1,
                        "name": "Parental Dinosaur Lot",
                        "v": [
                            586000,
                            216000,
                            150000
                        ],
                        "d": [
                            583000,
                            0,
                            0
                        ],
                        "speculators": [
                            [
                                4,
                                5
                            ],
                            [],
                            []
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
                    "taxRate": 1,
                    "initialTaxRate": 1,
                    "finalTaxRate": 33,
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
A list of player numbers, roles and tags describing the other players, quite useful to communicate and hoard information on others.
Roles:
1. Owner
2. Developer
3. Speculator
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
            },
            {
                "number": 4,
                "role": 1,
                "tag": "Speculator 1"
            },
            {
                "number": 5,
                "role": 1,
                "tag": "Speculator 2"
            }
        ]
    }
}
```
## Phase 1
## Phase 2
### declaration-received
Feedback from the server after a player (owner or developer) submits a declaration
```
{
    "type": "declaration-received",
    "data": {}
}
```
### declarations-published
After all property owners (including the developer) have published they declarations, they are made public to everyone. Speculators will use this information in the next phase to target owners who have understated the value of their property.
Relevant fields:
* number: the property owner number. All other identificators (id, name, owner) are ignored.
* d: declaration per condition: notice that the third condition is currently not used.
* available: currently not used due to a change in the game mechanics. Before only one speculator could strike the same property (per condition)
At this stage the winning condition is not yet known, unlike it happens with Harberger
```
{
                "type": "declarations-published",
                "data": {
                    "declarations": [
                        {
                            "id": 1,
                            "name": "Ripe Pigeon Lot",
                            "owner": "Fuchsia",
                            "role": 3,
                            "number": 1,
                            "d": [
                                574000,
                                206000,
                                0
                            ],
                            "available": [
                                true,
                                true,
                                true
                            ]
                        },
                        {
                            "id": 2,
                            "name": "Itchy Gamefowl Lot",
                            "owner": "Cyan",
                            "role": 2,
                            "number": 2,
                            "d": [
                                378000,
                                814000,
                                0
                            ],
                            "available": [
                                true,
                                true,
                                true
                            ]
                        },
                        {
                            "id": 3,
                            "name": "Squealing Minnow Lot",
                            "owner": "Lavender",
                            "role": 3,
                            "number": 3,
                            "d": [
                                478000,
                                193000,
                                0
                            ],
                            "available": [
                                true,
                                true,
                                true
                            ]
                        }
                    ]
                }
            }
```
## Phase 3
## Phase 4
## Phase 5
## Phase 6
## Phase 7
## Phase 8
## Phase 9
