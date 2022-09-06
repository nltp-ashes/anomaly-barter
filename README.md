# Anomaly NPC Bartering

This is a lightweight framework that allows for barters to be performed between the player and various NPCs.

A barter is defined as an exchange of one quantity of goods for another.

## Accessing Barters
There will be an extra button in trade UI. Click it and see what happens.

## Stipulations
These are restated in game.
- Bartering consists of 'give' and 'take' items.
- Give items are what the player receives as part of the deal.
- Take items are items that the player will hand over.
- Taken ammo must be a full box (usually 15 rounds usually for handgun and small rifle rounds, 10 for shotshells and large rifle rounds)
- Taken multiuse items (vodka, medkits) must have all uses to be considered.
- Taken items with condition must have the condition be at least 90% (adjustable in MCM)
- Taken items with parts require a minimum condition of 60% each, non-negotiable.

## Repeatability
- Some barters are subject to limitations. There are two main types:
- Fixed barters are tied to a save. Once performed, they may not be repeated.
- Restock barters refresh once the trader restocks their inventory.

# Integration Guide
1. Create a file `barter_yourcustom.ltx` under file path `configs\barter`.
2. Sample barter with fields explained.
```
[sid_jelly_suit]
trader          = m_trader 
give            = stalker_outfit 
take            = ammo_9x18_fmj,1,medkit,1,wpn_pm,1
type            = anyOf
precondition    = barter_core.check_limit,barter_core.check_goodwill
postcondition   = barter_core.increment_limit
restricted      = stalker
repeat          = fixed
limit           = 1
faction         = stalker
goodwill        = 100
desc            = st_sid_jelly_suit
desc_done       = st_sid_jelly_suit_done
```
In plain english:

The above barter is offered by Sid, exchanging one stalker suit for either some 9x18 rounds, a medkit, or a PM. This is a one-time only deal (never resets), is only offered to stalker players, and requires 100 stalker goodwill as a precondition.

## Table of fields 

| Field | Type | Description |
|-------|------|-------------|
|trader | string | Section of the trader to register barter for. |
| give | string | Item to be offered. Right now limitation is to a single instance of one item  as given reward.  Any invalid sections will render the barter invalid. |
|take|string (comma separated)| Format is `section,quantity` repeated at least once. Any invalid sections will render the barter invalid. |
|type|string (`allOf/anyOf`)|- allOf (Default): Requires ALL take items present to execute barter. <br> - anyOf: Requires any of the given take items (respecting quanitity). On barter, actor will be prompted to select one.|
|precondition|string (function refs)|These functions will be executed to check eligibility. If false is returned, the barter will be prevented. Functions are executed in order and ALL functions must return true to proceed. |
|postcondition|string (function refs)|These functions are executed after the barter is done.|
|restricted| string (comma separated factions)|OPTIONAL. Requires actor be one of the listed factions in order to view the barter. Defaults to all factions eligible (assuming friendliness)|
|repeat| `fixed/restock`|OPTIONAL. Leveraged by barter_core.check_limit. This determines the type of repeatability for the barter. Valid keys are either 'fixed` or 'restock'|
|limit|float|Max number of times barter can be performed before reset.|
|faction| string (faction)|OPTIONAL. Goes in tandem with `goodwill` key, is the 'given' faction for the goodwill requirement.|
|goodwill|integer|Requires the specified amount of goodwill for the given faction before allowing the barter.|
|desc|string|String key of message displayed when barter is selected.|
|des_done|string|String key of message displayed when max limit of barters is reached.|
3. Specified trader should have your item in barter menu.
4. Custom functions can be specified based oh what conditions you want your barter to show up.

## Available 