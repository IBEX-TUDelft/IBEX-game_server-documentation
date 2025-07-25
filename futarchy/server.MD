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
}
```
### phase-instructions
Legacy: ignore. The phase instructions are now fetched from a static file.

```
{
    "type": "phase-instructions",
    "data": {}
}
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
## Phase 0 (Introduction)
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
## Phase 1 (Presentation)
## Phase 2 (Declaration)
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
## Phase 3 (Speculation)
### speculation-received
Just a feedback message from the server to a speculator that his choice has been received.
```
{
    "type": "speculation-received",
    "data": {}
}
```
## Phase 4 (Reconciliation)
### value-signals
The social profit (sum of taxes paid) is divided into shares and some are returned to the community, which can trade them before theif final value (depending on the taxes really collected) is made known.
During this phase the signals (indicators of what the final price might be) are made known to players, so that they can get some orientation how to move on this market. The public signal is known to everyone, each player receives private signals (just known as signals). Both conditions are of interest in Futarchy during the market phase. Notice that the taxRate is updated for later use.
```
{
    "type": "value-signals",
    "data": {
        "signals": [
            10425,
            6092,
            0
        ],
        "publicSignal": [
            4719,
            4002.9,
            0
        ],
        "taxRate": 33
    }
}
```
## Phase 5 (Transition)
## Phase 6 (Market)
### add-order
Informs all players that someone has added an order. Since multiple markets are open at the same time (one per condition), the client has to keep in mind which order belongs to which market
```
{
    "type": "add-order",
    "data": {
        "order": {
            "id": 1,
            "sender": 5,
            "price": 6500,
            "quantity": 1,
            "type": "ask",
            "condition": 1
        }
    }
}
```
### asset-movement
Inform the players involved that a trade has happened, and that they should update their local wallets for that condition.
```
{
    "type": "asset-movement",
    "data": {
        "condition": 0,
        "movement": {
            "type": "sale",
            "quantity": 1,
            "price": 9850,
            "total": 9850
        },
        "balance": 309850,
        "shares": 29
    }
}
```
### contract-fulfilled
Informs all players that a trade (in the context of one condition) has happened.
```
{
    "type": "contract-fulfilled",
    "data": {
        "from": 2,
        "to": 4,
        "price": 9850,
        "condition": 0,
        "median": 9850
    }
}
```
### delete-order
Informs all players that an order for one condition is not available any more.
```
{
    "type": "delete-order",
    "data": {
        "order": {
            "id": 1,
            "type": "ask",
            "condition": 0
        }
    }
}
```
### order-refused
An order can be refused for several reasons (like lack of funds, or all shares already for sale)
```
{
    "type": "order-refused",
    "data": {
        "message": `Could not fulfill your order: not enough funds`,
        "placeholder": "could-not-find-bids"
    }
}
```
### winning-condition
At the end of the market phase it becomes apparent which is the winning condition
```
{
    "type": "winning-condition",
    "data": {
        "winningCondition": 0
    }
}
```
### profit
This kind of message informs that there was a profit. It is just informative and can be disregarded in favor of the round summary coming later at the end of the round. The format differs depending on the recipient. Speculators will riceive one profit notification per speculation attempt (they can strike multiple target in one speculation phase).
Please note the difference with Harbeger: the profit there was known earlier. Here one has to wait to know which is the winning condition to see what world is selected (only the transactions and speculations under that condition will happen) before any profit can materialize.
Speculator format:
```
{
    "type": "profit",
    "data": {
        "round": 1,
        "owner": 3,
        "sniped": true,
        "snipeProfit": 1000,
        "condition": 0,
        "declaration": 480000
    }
}
```
Owner format (notice that the snipeProfit is the profit for the sniper, so its a loss for the owner):
```
{
    "type": "profit",
    "data": {
        "round": 1,
        "phase": 4,
        "property": 3,
        "condition": 0,
        "value": 482000,
        "declaration": 480000,
        "sniped": true,
        "speculator": [
            5
        ],
        "snipeProfit": 1000,
        "taxes": 4800,
        "owner": 3,
        "role": 3,
        "total": 476200
    }
}
```
### first-snipes
Information on the speculation actions (snipes) inflicted or suffered that really happened: those executed under the winning condition, the others are discarded. They come as a list with player (attacker) and target. The profit (for the speculator) is also indicated
```
{
    "type": "first-snipes",
    "data": {
        "snipes": [
            {
                "player": {
                    "number": 4,
                    "role": 1
                },
                "target": {
                    "number": 1,
                    "role": 3
                },
                "profit": 6000
            }
        ]
    }
}
```
## Phase 7 (Declaration)
### declaration-received
Feedback from the server after a player (owner or developer) submits a declaration
```
{
    "type": "declaration-received",
    "data": {}
}
```
### declarations-published
See the same in phase 2
```
{
    "type": "declarations-published",
    "data": {
        "declarations": [
            {
                "id": 1,
                "name": "Parental Dinosaur Lot",
                "owner": "Coffee",
                "role": 3,
                "number": 1,
                "d": [
                    583000,
                    0,
                    0
                ],
                "available": [
                    true,
                    false,
                    false
                ]
            },
            {
                "id": 2,
                "name": "Stupid Elk Lot",
                "owner": "Rose",
                "role": 2,
                "number": 2,
                "d": [
                    384000,
                    0,
                    0
                ],
                "available": [
                    true,
                    false,
                    false
                ]
            },
            {
                "id": 3,
                "name": "Coherent Ant Lot",
                "owner": "Ivory",
                "role": 3,
                "number": 3,
                "d": [
                    481000,
                    0,
                    0
                ],
                "available": [
                    true,
                    false,
                    false
                ]
            }
        ],
        "winningCondition": 0
    }
}
```
## Phase 8 (Speculation)
### speculation-received
Just a feedback message from the server to a speculator that his choice has been received.
```
{
    "type": "speculation-received",
    "data": {}
}
```
### profit
This kind of message informs that there was a profit. It is just informative and can be disregarded in favor of the round summary coming later at the end of the round. The format differs depending on the recipient. Speculators will riceive one profit notification per speculation attempt (they can strike multiple target in one speculation phase)
Speculator format:
```
{
    "type": "profit",
    "data": {
        "round": 1,
        "owner": 3,
        "sniped": true,
        "snipeProfit": 1000,
        "condition": 0,
        "declaration": 480000
    }
}
```
Owner format (notice that the snipeProfit is the profit for the sniper, so its a loss for the owner):
```
{
    "type": "profit",
    "data": {
        "round": 1,
        "phase": 4,
        "property": 3,
        "condition": 0,
        "value": 482000,
        "declaration": 480000,
        "sniped": true,
        "speculator": [
            5
        ],
        "snipeProfit": 1000,
        "taxes": 4800,
        "owner": 3,
        "role": 3,
        "total": 476200
    }
}
```
### final-price
This event does not appear in the first speculation phase (phase 3). It informs all players on what is the final share price for the winning condition.
```
{
    "type": "final-price",
    "data": {
        "price": 4778.4,
        "winningCondition": 0
    }
}
```
### tax-income
How much a player has made by keeping the tax shares. A better report with all profit/losses will come later.
```
{
    "type": "tax-income",
    "data": {
        "amount": 23449,
        "condition": 0
    }
}
```
### total-profit
How much a player a player has made during the round. A better report with all profit/losses will come later.
```
{
    "type": "total-profit",
    "data": {
        "amount": 990569
    }
}
```
### second-snipes
A list of speculations happened in the second speculation phase where a specific player was involved (as attacker or target).
```
{
    "type": "second-snipes",
    "data": {
        "snipes": [
            {
                "player": {
                    "number": 4,
                    "role": 1
                },
                "target": {
                    "number": 1,
                    "role": 3
                },
                "profit": 750
            },
            {
                "player": {
                    "number": 5,
                    "role": 1
                },
                "target": {
                    "number": 1,
                    "role": 3
                },
                "profit": 750
            }
        ]
    }
}
```
## Phase 9 (Results)
### round-end
The round is about to end and the client should start making preparations for the next round.
```
{
    "type": "round-end",
    "data": {
        "round": 1
    }
}
```
### round-summary
A complete report on the player's performance during the round. This report can be used for benchmarking the agent performance.
```
{
    "type": "round-summary",
    "data": {
        "round": 1,
        "condition": 0,
        "value": 586000,
        "firstDeclaration": 574000,
        "firstTaxes": 5740,
        "firstRepurchase": -6000,
        "snipes": [
            {
                "player": {
                    "number": 4,
                    "role": 1
                },
                "target": {
                    "number": 1,
                    "role": 3
                },
                "profit": 750
            },
            {
                "player": {
                    "number": 5,
                    "role": 1
                },
                "target": {
                    "number": 1,
                    "role": 3
                },
                "profit": 750
            }
        ],
        "market": {
            "balance": 50000,
            "shares": 7,
            "price": 4778.4
        },
        "secondDeclaration": 583000,
        "secondTaxes": 192390,
        "secondRepurchase": -1500
    }
}
```
### reward
A reward won by a player. This happens only when the game is over. This report can be used for benchmarking the agent performance.
```
{
    "type": "reward",
    "data": {
        "number": 1,
        "round": 1,
        "reward": 38.19,
        "basePoints": 200000,
        "showupFee": 5,
        "gameFee": 33.19,
        "profit": 463818.8,
        "points": 663818.8,
        "factor": 20000,
        "exchange": 0.00005,
        "paymentToken": "HV"
    }
}
```
### game-over
The game is over. Sent only after the final round is complete.
```
{
    "type": "game-over",
    "data": {}
}
```
