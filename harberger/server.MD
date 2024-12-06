# Harberger Server Messages
Here listed is the set of events (divided by phases) sent by the server to the players. These messages are asynchronous and players are expected to react upon them, choosing an action available to them in that specific phase. Keep also in mind that different players have different roles, and their possible actions (within a phase) may differ.

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

## Phase #0

### 

## Phase #1

## Phase #2

## Phase #3

## Phase #4

## Phase #5

## Phase #6

## Phase #7

## Phase #8

## Phase #9
