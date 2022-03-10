## SpaceTraders v2.0.0-alpha Preview

Hello everyone and welcome to the SpaceTraders v2.0-alpha preview! We are excited to share progress with our supporters and hope you enjoy the new changes to the game.

We've re-thought some elements of the API from lessons learned with our v1.0 release, and hope you will enjoy some of the fleshed out ship mechanics, larger universe to explore, and new ideas that we are still  working on behind the scenes. We really want the 2.0 API to be beginner friendly and reliably stable for developers to build on top of.

We appreciate all of the positive comments and feedback, and look forward to a second year of building SpaceTraders!

## Overview of the Alpha / Beta / Public releases

The alpha release will be a period of about a month where we fix any critical bugs and make significant changes to API endpoints if they are necessary. The alpha phase will be shared mostly with our supporters and some close members of the community, so please don't advertise the release too broadly.

Having said that, the alpha won't be a secret, so there is no harm if you want to share it directly with friends.

Following the alpha will be a public beta release, at which point it should be relatively safe to build an SDK on top of the API. We'll continue to add a few new features, but will be hesitant to make any significant breaking changes. We expect the beta to be a few weeks as we confirm the API is scaling well and performance issues are addressed.

Then we will finally update all of the documentation and website to point to the new 2.0 release.

## Alpha Goals

Our goals for the early alpha are to gather feedback so we can guarantee API stability for those that want to start building SDKs. Once we go into a beta release, we will be much less likely to change any specific endpoint, and after a full release we will strive to only make backwards-compatible changes.

## What can you do

We're genuinely happy to just have people play with the API, and sharing any feedback or progress will be super motivating for us. If you want to help in a specific way, we're looking for the following feedback from the alpha release:

- Find and report bugs or exploits in the game, such as the ability to trade cargo your ship doesn't contain
- Look for typos or errors in the game data, symbols, etc.
- Let us know if performance is an issue for any of the endpoints
- Tell us which endpoints are painful to use or game mechanics that are tedious / uninteresting\
- Anything that you think will be difficult for us to add later without breaking how the API works

Again the goal is to catch significant changes that need to be made before the beta release. Thanks for any time you are able to provide to test it out!

## New V2 Alpha Terminology

The 2.0 version of the API has been significantly reworked and introduces some new terminology. Before reviewing the new endpoints in the next section, it's worth reading through some of the new terms and what they mean.

- **Account** - you can register an account (not required) with your email address and discord handle and it will persist across resets. We will eventually add registration to the website, but for now you can do it via API calls. You will also be able to register your patreon contribution for special in-game recognition.
- **Agent** - when you start a new game, you register as an agent and choose a symbol (4-8 characters) as your call sign. All of your ships, credits, etc. exist under your agent entity, and you can only have one agent at a time under your account. You will be able to start a new game by retiring your agent and creating a new one.
- **Sector** - a slice of the universe filled with systems of different types such as star systems, black holes, or nebulae.
- **System** - a collection of waypoints orbiting around a central feature such as a star or black hole. Systems contain an array of waypoints on an x, y plane.
- **Waypoint** - a location within a system that you can travel to and interact with. Some common types of waypoints include planets, asteroid fields and jump gates.
- **Ship** - your ships can navigate between waypoints and jump across systems. They consist of a frame, reactor, engine and various modules and mounts.
- **Ship Frame** - the structure of a ship which determines the number of modules and mounts that can be installed.
- **Ship Reactor** - the power module of the ship and limits the types of power-intensive modules and mounts that can be installed.
- **Ship Engine** - the engine of your ship and determines how fast your ship travels when navigating at slow-than-light speeds.
- **Ship Module** - an internal module that can be added or removed from your ship. Common modules include cargo bays and passenger cabins.
- **Ship Mount** - an external mount that can be added or removed from your ship. Common mounts include mining lasers and sensor arrays.
- **Fuel** - a resource that is consumed by your ship when navigating. You can refuel your ship, and one unit of the FUEL good adds 100 units to your fuel to your ship's tank.
- **Crew** - the staff that run your ship and keep it operating at peek performance. Crew can be recruited throughout the universe and will eventually be subject to changes in morale, which will impact how your ship performs. Ships which are understaffed will perform poorly such as longer cooldowns, slower navigation, and more damage to the ship while navigating.
- **Drone** - drone ships are the simplest type of ship to automate. They don't require fuel or crew, but are quite slow and incapable of jumping between systems without access to a jump gate. Drones also wear down quickly over time and need to be repaired or replaced.
- **Navigate** - slower-than-light travel between waypoints. Most ship actions will fail while your ship is in-transit between waypoints. Wait until your ship has arrived before attempting any actions.
- **Jump** - faster than light travel between systems. Jumping requires a jump drive or jump gate, and is instantaneous but subject to a cooldown between jumps.
- **Survey** - find specific yields or ORE and other materials at a waypoint. Surveys return a list of signatures for extraction. These signatures are time-sensitive and will eventually expire as they move out of reach of your ship.
- **Extract** - executes an extraction at a waypoint such as an asteroid field using one of your ships. Fills your cargo with random yields from that waypoint, or you can pass a survey signature and generate more targeted yields.
- **Yield** - the types and quantities of ores or gases that can be extracted from a waypoint.
- **Scan** - generates a list of arriving or departing ships from a waypoint. Later in our roadmap, you will be able to interdict some of these ships.
- **Chart** - most systems are unexplored and need to be charted for other agents to see the details of the system. Submitting the chart will allow other agents to see the query the system, but sometimes keeping a system's details secret can be advantageous.
- **Cooldown** - actions such as jumping, scanning and extracting will put your ship on a cooldown for that action. You should wait until the number of seconds have passed on the cooldown before attempting the action again.
- **Contract** - a set of terms that will remit payment on completion. Contracts are generated through factions, and might request your agent to procure goods of a certain type, or transport of goods to a location by a certain time.
- **Delivery** - transfers goods from your ship's cargo bay into the contract.
- **Orbit** - when your ship arrives at a waypoint, it will be in orbit. Your ship must be in orbit to execute certain actions such as a scan or extraction. You can transition your ship from docked to orbit.
- **Dock** - you can dock your ship at a waypoint after navigating to it. You will be able to execute market trades, and other planet-side actions once you've docked at a waypoint.
- **Jettison** - jettison cargo from your ship into space to make room for more valuable cargo or to speed up your ship while navigating
- **Trade** - all items in the universe have a unique Trade Symbol in all caps, such as IRON_ORE, NANOBOTS and REACTOR_FISSION_I
- **Market** - most populated waypoints have a market where you can buy and sell goods by Trade Symbol. Each market will have a list of imports, exports and commodities that they trade in. Commodities rarely trade at a profit, but running a trade route between an export and import will bring much higer profits. Markets evolve with player activity, prices will rise and fall with changes in trade quantity, and new imports or exports will be added and removed as the market grows.
- **Tariff** - some factions charge a tariff, which is a flat tax added to each unit of a good traded.
- **Shipyards** - players can purchase ships at shipyards and later execute maintanence on a ship or install new modules and mounts.

## New API Endpoints

Here is a list of the new endpoints for the terms above. For this alpha-preview, some of the parameters for an endpoint are not fully implemented, but we've included them here to give an indication of what will be supported.

Each endpoint also has an example response you can retrieve by sending `stub=true` as a query parameter or in your POST body. You can also view a stubbed response for an error code by sending `errorCode={code}` with the stub parameter.

All routes that are prefixed with `/my` will require an agent access token to be passed as an `Authorization: Bearer {token}` header in the request.

When registering your account, you will be given a different type of token called an Account Token, which allows you to create a new Agent or update your account details such as email address, discord handle or patreon code.

All routes targeting a specific ship, such as `/my/ships/COBRA-00AF/survey` will put a lock on your ship until the request is resolved. This means you can only take a single action at a time for your ship. Sending two simaltaneous actions for a single ship will throw a 409 conflict response code.

All routes with the prefix `/systems` will return limited data until an agent submits the chart for that system or you have a ship present in the system.

All actions are typically sent as a POST request and return a 201 status code. All query endpoints are sent as a GET request and will return a 200. Errors are detailed in the response body of a failed request, with a message and unique error code.

Static game data can be queried and should be cached using the `/game/data` endpoint. Most objects returned in the response of a request will include only the symbols for static data, which you will want to map to the objects from the game data endpoint.

```bash
# view agent details
/my/agent

# view account details
/my/account

# view all ships
/my/ships

# view ship details
/my/ships/EMBER-01

# jump to a target system
/my/ships/EMBER-01/jump destination=X1-OE

# navigate between waypoints (modes coming soon)
/my/ships/EMBER-01/navigate mode=CRUISE destination=OE-PM00A

# dock at a waypoint if permitted
/my/ships/EMBER-01/dock mode=STEALTH

# put your ship into orbit around a waypoint
/my/ships/EMBER-01/orbit mode=BLOCKADE,SPY,PATROL

# acquire specific yield signatures in an asteroid field for extraction
/my/ships/EMBER-01/survey

# extract raw materials from a waypoint such as an asteroid field or gas cloud
# send the full survey object as the payload to target a specific signature
/my/ships/EMBER-01/extract survey='{signature: "...", deposits: "...", expiration: "..."}'

# purchase cargo and load it into your ship
/my/ships/EMBER-01/purchase symbol=HEAVY_MACHINERY quantity=99999

# sell cargo and unload it from your ship
/my/ships/EMBER-01/sell symbol=HEAVY_MACHINERY quantity=99999

# jettison cargo from your ship
/my/ships/EMBER-01/jettison symbol=HEAVY_MACHINERY quantity=99999

# view all systems
/systems

# view system details
/systems/X1-OE

# submit system information into the public repository of charts
/systems/X1-OE/chart

# view waypoint details
/systems/X1-OE/waypoints/X1-OE-001A

# view all shipyards in a system
/systems/X1-OE/shipyards

# view all ships for sell at a shipyard
/systems/X1-OE/shipyards/X1-OE-001A/ships

# view all markets in a system
/systems/X1-OE/markets

# view all trades at a given market
/systems/X1-OE/markets/X1-OE-001A

# view waypoints that list the given trade
/systems/X1-OE/trades/IRON_ORE

# list all static data for the game
/game/data
```

## Getting Started

If you are ready to dive in, just hit the endpoint to register as a new agent. Don't worry too much about account creation right now - temporary accounts will be setup when creating a new agent. We will likely still be doing hard resets during the alpha.

```bash
https POST v2-0-0-alpha.spacetraders.io/agents symbol=YOUR_SYMBOL faction=COMMERCE_REPUBLIC
```

When starting a new game, you'll have a command ship to fly around and a contract with the Commerce Republic that you can accept for some credits. Try completing the contract, or just fly around and begin trading and exploring. You can also purchase some low-upkeep drones to begin automating trade routes.

## Alpha Roadmap

We're still hard at work completing items we have envisioned for the alpha release. We're working on full API documentation in Spotlight Studio, seeding new types of ships and balancing stats, and so much more.

Here is a list of what we are working on behind the scenes:

- Full API documentation
- Seeding shipyards across the sector
- Command Points that are expended to control your ships
- A ship logs endpoint that keeps track of all your ship's actions and events that occur
- Markets evolving and systems growing / collapsing with agent activity
- Seeding unique factions across the universe and new contracts
- Ability to scavenge, salvage and run planet-side expeditions to discover hidden ruins and valuable relics
- Bi-weekly seasons with leaderboards for various achievements
- New documentation website with guides and explanation of game mechanics
- Tweaking API throttling
- Faction licenses to patrol and trade
- Ability to interdict ships as a patrol or pirate
- Shuttle populations around to grow new systems
- Constructing goods with the micro-fabricator / micro-refinery modules
