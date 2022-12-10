# What you do

- you will register as a new agent and receive an API token
- you will gain a command ship and 100,000 credits in a starter system
- you will use the API to explore the universe and make money
- you will buy a mining ship, navigate to a neary asteroid field, and mine for resources
- you will find a nearby market to sell your resources
- you will explore new markets to find new goods to trade, better ships to buy, and upgrades for your ships
- you will scavenge derelict ships for parts and sell them for a profit
- you will send envoys to other factions to gain access to their markets
- you will send envoys to make trade deals not listed in the public markets
- you will search for lost artifacts and sell them for a profit or use them to upgrade your ships
- you will recruit crew members to help you run your ships
- you will chart new systems, discovering new factions and markets along the way

# Game Restrictions

- the API has a limit of 60 requests per minute
- you can only have one active agent per account
- account email must be verified to purchase ships
- you can only have 10 ships in your fleet at a time (including your command ship)
- each ship is limited to a single action at a time (don't send two requests for the same ship concurrently)
- your fleet must stay in range of your command ship (about 100 lightyears)
- you must maintain the condition of your ships or they will slow down considerably
- you must maintain crew morale or your ship will suffer from reduced performance and your crew may abandon your ship
- crew wages are due hourly and paid whenever your ship docks at a civilized waypoint
- you must maintain your ship's fuel or it may end up stranded without an ability to navigate
- ships can take damage when docking planet-side, orbiting dangerous waypoints, or when extracting resources from asteroids
- several factors will determine your ship's travel time including ship condition, engine stats, speed setting, crew morale, etc.
- more fuel is expended when traveling at higher speeds or when trying to leave a planet with atmospheric drag or high gravity
- uncharted systems will not have all details available until it has been charted by an agent
- most market data will only be available if you have a ship present at the given waypoint
- jump gates are typically restricted to other jump gates within the same faction
- most actions will result in a reactor cooldown, which will prevent your ship from taking other actions until the cooldown has expired
- markets have a limit on how many units can be bought or sold at a given time, which is the same as the market's trade volume

# Factions and Reputation

- faction contracts often provide better prices than public markets but may be more difficult to fulfill
- contracts grant some amount of payment upon accepting a contract, and the rest upon completing it
- you can send an envoy to generate new contracts with a faction
- failing to accept a generated contract will result in a minor loss of faction reputation
- failing to complete an accepted contract will result in a loss of faction reputation
- fulfilling a contract will result in a gain of faction reputation
- trading with a faction will marginally increase your reputation with that faction
- some markets are restricted until you have reached a certain threshold of reputation with a faction

# Markets

- a market lists imports and exports, recent transactions, and the current price of goods
- exports are typically listed at a lower purchasing price and imports listed at a higher selling price
- all other goods are considered "exchange" and are mostly driven by agent activity
- prices are driven by supply and demand, and typically change over time
- the listed imports and exports of a market also evolve over time, and some goods may become unavailable
- shipyard listings are also dynamically priced based on player activity
- you can send envoys to attempt to purchase or sell goods not listed in the public market
- off-market goods are typically traded in very low volumes and prices are typically volatile

# Universe

- the universe is procedurally generated with a few starter systems and factions
- the universe consists of systems, and systems are filled with waypoints, such as planets or asteroid fields
- each waypoint has an x, y coordinate, type and traits, and any orbitals that may be present
- a waypoint and it's orbitals share the same x, y coordinate

# Navigation

- jump gates are a type of waypoint that connects systems together, allowing your ship to jump to a new system instantly
- navigating between waypoints involves choosing a destination and waiting for your ship to arrive
- ships are either docked or in-orbit at a waypoint, which may limit what actions you can take

# Generate an SDK from the API definition

You can generate an SDK from the API definition using [OpenAPI Generator](https://openapi-generator.tech/).

```bash
openapi-generator generate \
 -i reference/SpaceTraders.json \
 -o spacetraders-sdk \
 -g typescript-axios \
 --skip-validate-spec \
 --additional-properties=supportsES6=true
```

# Install HTTpie

You can install [HTTpie](https://httpie.io/) using [Homebrew](https://brew.sh/).

```bash
brew install httpie
```

# Register a temporary Account

You can register a temporary account using the [Register](https://api-server-2-0-0-rc-2-vxxwq5xqdq-uc.a.run.app/register) endpoint.

```json http
{
  "method": "POST",
  "url": "https://api-server-2-0-0-rc-2-vxxwq5xqdq-uc.a.run.app/register",
  "headers": {
    "Content-Type": "application/json"
  },
  "body": {
    "symbol": "YOUR_AGENT_SYMBOL",
    "faction": "ASA"
  }
}
```
