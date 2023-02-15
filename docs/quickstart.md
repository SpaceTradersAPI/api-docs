# Quickstart Guide

SpaceTraders is a fleet management game where all players operate in the same universe. You can use the API to explore star systems, manage your ships, make money, and grow your empire (or stop others from doing the same).

Typical commands in the game include: purchasing ships, navigating to waypoints, mining asteroids, trading goods, taking on bounties, jumping to new systems, and more.

You can use the API to automate your fleet or build a client for you and other players to play.

<!-- theme: info -->

> #### Alpha 2.0
>
> The game is a work in progress. We are currently in the alpha stage of development. We are actively working on the game and adding new features. If you have any questions or feedback, please join our [Discord](https://discord.gg/QB8zyNW7RH) and let us know.

### Game Overview

The following is a general overview of the game and what you can do as a player (some features are not yet implemented):

- [x] you can register as a new agent which will grant you an API token, a command ship with 100,000 credits, and a faction contract in a starter system
- [x] you can use the API to explore the universe and make money by mining, trading, scavenging, and bounty hunting (scavenging and bounty hunting WIP)
- [x] you can purchase ships and upgrade them with new modules and mounts
- [x] you can chart new systems, discovering new factions and locations with valuable resources
- [x] you can grow systems by fulfilling faction contracts and supplying markets with necessary goods
- [x] you can connect trade routes between systems to automate the flow of goods
- [x] you can refine ores, minerals and other resources into valuable goods
- [ ] you can scavenge derelict ships for parts and sell them or use them to upgrade your ships
- [ ] you can take on bounties to earn credits or pirate other players trade routes
- [ ] you can send envoys to meet new factions and generate contracts or unique trade deals
- [ ] you can search for ancient artifacts on distant planets and sell them for a profit
- [ ] you can recruit crew members to help you run your ships

### Check Server Status

All of the steps in this guide are executed from the command line. You will want to install [HTTPie](http://httpie.io/docs#installation) to follow along. If you are on a Mac and you have homebrew installed, you can execute the following:

```bash
brew install httpie
```

You can then hit any API endpoint by simplying typing `https` along with the http method and the url. You can test this by hitting the game status endpoint which will tell us if the game is currently available to play:

```bash
https GET v2.api.spacetraders.io
```

```json
{
  "status": "SpaceTraders is currently online and available to play",
  "stats": {
    "agents": 26,
    "ships": 30,
    "systems": 4,
    "waypoints": 11
  },
  "leaderboards": {
    "mostCredits": [],
    "mostSubmittedCharts": []
  }
}
```

<!-- theme: info -->

> #### After The Basics
>
> After you are comfortable with the basics, you may want to generate a client library for your favorite programming language. Our API documentation follows the OpenAPI specification, so you can use any tool that supports OpenAPI, such as [OpenAPI Generator](https://openapi-generator.tech/) to create a client library.

### Generate An Access Token

To access most API endpoints other than the game status, you will need to generate an API token by registering as an agent.

Every agent is identified by a unique call sign (such as `ZER0` or `SP4CE_TR4DER`) and a starting faction. You can choose any symbol you want, but it must be unique. You can choose from one of the following starter factions:

- [x] `COSMIC` - The Cosmic Engineers are a group of highly advanced scientists and engineers who seek to terraform and colonize new worlds, pushing the boundaries of technology and exploration.
- [x] `VOID` - The Voidfarers are a group of nomadic traders and adventurers who travel the galaxy in search of riches and adventure, willing to take risks and explore the unknown.
- [x] `GALACTIC` - The Galactic Alliance is a coalition of planets and factions that have banded together for mutual protection and support, working together to defend against external threats and promote cooperation.
- [x] `QUANTUM` - The Quantum Federation is a group of planets and factions that have joined together to share knowledge and technology, using their collective expertise to advance the science and technology of the galaxy.
- [x] `DOMINION` - The Stellar Dominion is a powerful, expansionist faction that seeks to conquer and control as many systems as possible, using their advanced technology and military might to dominate their rivals.
- [x] `ASTRO` - The Astro-Salvage Alliance is a group of scavengers and salvagers who search the galaxy for ancient artifacts and valuable technology, often combing through old ship battlegrounds and derelict space stations.
- [x] `CORSAIRS` - The Seventh Space Corsairs are a feared group of pirates and raiders who operate throughout the galaxy, preying on merchant ships and plundering valuable cargo.

```bash
https POST v2.api.spacetraders.io/register \
  symbol=ZER0 \
  faction=COSMIC
```

```json
{
  "data": {
    "token": "eyJhbGciOiJSUzI1NiIsI...",
    "agent": {
      /* ... */
    },
    "contract": {
      /* ... */
    },
    "faction": {
      /* ... */
    },
    "ship": {
      /* ... */
    }
  }
}
```

You will see a JSON object with your token and agent details along with your starting contract, ship and faction.

Copy your token somewhere safe and set it as a variable in your command prompt. You will need this token to access all of the other endpoints in this quickstart. You should also set a few more variables to make it easier to copy and paste the commands in this guide. The following is an example of how you might do this in a bash shell:

```bash
apiToken=<your-token-here>
shipSymbol=<your-ship-symbol-here>
contractId=<your-contract-id>
apiUrl=v2.api.spacetraders.io
```

<!-- theme: warning -->

> #### Keep Your Token Safe
>
> During the alpha phase, you will need to save your token and use it for all API requests. Keep in mind anyone with this token can access your agent and make changes to your fleet. We will be adding a token management portal in the future to make it easier to manage your tokens.

### Contracts

When registering as a new agent, you will be assigned your first faction contract. Your contract is a set of objectives that you must complete to earn credits and advance your reputation. You can view your current contract by calling the `/my/contracts` endpoint:

```bash
https -A bearer -a $apiToken GET $apiUrl/my/contracts/$contractId
```

```json
{
  "data": [
    {
      "id": "clc0u1d210005p4ys90philn5",
      "factionSymbol": "COMMERCE_REPUBLIC",
      "type": "PROCUREMENT",
      "terms": {
        "deadline": "2022-12-30T18:14:46.104Z",
        "payment": {
          "onAccepted": 58800,
          "onFulfilled": 235200
        },
        "deliver": [
          {
            "tradeSymbol": "QUARTZ_SAND",
            "destinationSymbol": "X1-OE-PM",
            "unitsRequired": 14000,
            "unitsFulfilled": 0
          }
        ]
      },
      "accepted": false,
      "fulfilled": false,
      "expiration": "2022-12-26T18:14:46.103Z"
    }
  ],
  "meta": {
    "total": 1,
    "page": 1,
    "limit": 20
  }
}
```

#### Accept Contract

Contracts will typically provide a small payment on acceptance and the final ammount on completion. Contracts also expire after a certain amount of time, so you will need to accept and complete them in time or take a reputation hit with the faction. Also note that contracts that are not accepted will also expire and result in a minor loss to reputation.

You can accept a contract by calling the `/my/contracts/:contractId/accept` endpoint:

```bash
https -A bearer -a $apiToken POST $apiUrl/my/contracts/$contractId/accept
```

```json
{
  "data": {
    "contract": {
      "id": "clc0u1d210005p4ys90philn5",
      "factionSymbol": "COMMERCE_REPUBLIC",
      "type": "PROCUREMENT",
      "terms": {
        "deadline": "2022-12-30T18:14:46.104Z",
        "payment": {
          "onAccepted": 58800,
          "onFulfilled": 235200
        },
        "deliver": [
          {
            "tradeSymbol": "QUARTZ_SAND",
            "destinationSymbol": "X1-OE-PM",
            "unitsRequired": 14000,
            "unitsFulfilled": 0
          }
        ]
      },
      "accepted": true,
      "fulfilled": false,
      "expiration": "2022-12-26T18:14:46.103Z"
    },
    "agent": {
      "accountId": "clc0u1cvk0000p4ysdxjcuy2e",
      "symbol": "595559",
      "headquarters": "X1-OE-PM",
      "credits": 125000
    }
  }
}
```

Now that you have accepted the contract, let's look at how you can fulfill it by mining and delivering the required cargo.

### Your Fleet

Your fleet is a collection of ships that you own and control. You can purchase ships from shipyards and upgrade them with new modules and mounts.

During the alpha release agents will start with a command ship. You can purchase additional ships from shipyards such as mining drones or freighters to help you transport cargo.

#### View your ships

You can view your ships by calling the `/my/ships` endpoint:

```bash
https -A bearer -a $apiToken GET $apiUrl/my/ships
```

```json
{
  "data": [
    {
      "symbol": "ELITE-1",
      "nav": {
        "systemSymbol": "X1-ST66",
        "waypointSymbol": "X1-ST66-52115F",
        "route": {
          "departure": {
            "symbol": "X1-ST66-22150C",
            "type": "PLANET",
            "systemSymbol": "X1-ST66",
            "x": -6,
            "y": -7
          },
          "destination": {
            "symbol": "X1-ST66-52115F",
            "type": "ASTEROID_FIELD",
            "systemSymbol": "X1-ST66",
            "x": 28,
            "y": -12
          },
          "arrival": "2022-12-19T16:37:43.487Z"
        },
        "status": "IN_ORBIT",
        "flightMode": "CRUISE"
      },
      "crew": {
        "current": 62,
        "capacity": 80,
        "required": 62,
        "rotation": "STRICT",
        "morale": 100,
        "wages": 0
      },
      "fuel": {
        "current": 1166,
        "capacity": 1200,
        "consumed": { "amount": 34, "timestamp": "2022-12-19T16:36:20.487Z" }
      },
      "frame": {
        "symbol": "FRAME_FRIGATE",
        "name": "Frame Frigate",
        "description": "A medium-sized, multi-purpose spacecraft, often used for combat, transport, or support operations.",
        "moduleSlots": 8,
        "mountingPoints": 5,
        "fuelCapacity": 1200,
        "condition": 100,
        "requirements": { "power": 8, "crew": 25 }
      },
      "reactor": {
        "symbol": "REACTOR_FISSION_I",
        "name": "Fission Reactor I",
        "description": "A basic fission power reactor, used to generate electricity from nuclear fission reactions.",
        "condition": 100,
        "powerOutput": 27,
        "requirements": { "crew": 8 }
      },
      "engine": {
        "symbol": "ENGINE_ION_DRIVE_II",
        "name": "Ion Drive II",
        "description": "An advanced propulsion system that uses ionized particles to generate high-speed, low-thrust acceleration, with improved efficiency and performance.",
        "condition": 100,
        "speed": 30,
        "requirements": { "power": 6, "crew": 8 }
      },
      "modules": [
        {
          "symbol": "MODULE_CARGO_HOLD_I",
          "name": "Cargo Hold",
          "description": "A module that increases a ship's cargo capacity.",
          "capacity": 30,
          "requirements": { "crew": 0, "power": 1, "slots": 1 }
        },
        {
          "symbol": "MODULE_CARGO_HOLD_I",
          "name": "Cargo Hold",
          "description": "A module that increases a ship's cargo capacity.",
          "capacity": 30,
          "requirements": { "crew": 0, "power": 1, "slots": 1 }
        },
        {
          "symbol": "MODULE_CREW_QUARTERS_I",
          "name": "Crew Quarters",
          "description": "A module that provides living space and amenities for the crew.",
          "capacity": 40,
          "requirements": { "crew": 2, "power": 1, "slots": 1 }
        },
        {
          "symbol": "MODULE_CREW_QUARTERS_I",
          "name": "Crew Quarters",
          "description": "A module that provides living space and amenities for the crew.",
          "capacity": 40,
          "requirements": { "crew": 2, "power": 1, "slots": 1 }
        },
        {
          "symbol": "MODULE_ENVOY_QUARTERS_I",
          "name": "Envoy Quarters",
          "description": "A module that provides living space and amenities for VIP passengers or diplomatic envoys.",
          "capacity": 1,
          "requirements": { "crew": 5, "power": 2, "slots": 2 }
        },
        {
          "symbol": "MODULE_JUMP_DRIVE_I",
          "name": "Jump Drive I",
          "description": "A basic antimatter jump drive that allows for instantaneous short-range interdimensional travel.",
          "range": 500,
          "requirements": { "crew": 10, "power": 4, "slots": 1 }
        },
        {
          "symbol": "MODULE_WARP_DRIVE_I",
          "name": "Warp Drive I",
          "description": "A basic warp drive that allows for short-range interstellar travel.",
          "range": 2000,
          "requirements": { "crew": 2, "power": 3, "slots": 1 }
        }
      ],
      "mounts": [
        {
          "symbol": "MOUNT_SENSOR_ARRAY_I",
          "name": "Sensor Array I",
          "description": "A basic sensor array that improves a ship's ability to detect and track other objects in space.",
          "strength": 1,
          "requirements": { "crew": 0, "power": 1 }
        },
        {
          "symbol": "MOUNT_MINING_LASER_I",
          "name": "Mining Laser I",
          "description": "A basic mining laser that can be used to extract valuable minerals from asteroids and other space objects.",
          "strength": 10,
          "requirements": { "crew": 0, "power": 1 }
        },
        {
          "symbol": "MOUNT_GAS_SIPHON_I",
          "name": "Gas Siphon I",
          "description": "A basic gas siphon that can extract gas from gas giants and other gas-rich bodies.",
          "requirements": { "crew": 0, "power": 1 }
        }
      ],
      "registration": {
        "name": "ELITE-1",
        "factionSymbol": "COSMIC",
        "role": "COMMAND"
      },
      "cargo": {
        "capacity": 60,
        "units": 15,
        "inventory": [
          {
            "symbol": "ANTIMATTER",
            "name": "Antimatter",
            "description": "A highly valuable and dangerous substance used for advanced propulsion and weapons systems.",
            "units": 15
          }
        ]
      }
    }
  ],
  "meta": { "total": 1, "page": 1, "limit": 20 }
}
```

This response contains a list of your ships, including nav details, cargo, and ship build.

Your command ship comes equipped with a mining laser and survey mount, which you can use to extract minerals and ores from asteroids. Every ship has a symbol which is a unique identifier for that ship. You can use this symbol to refer to a specific ship in other API calls.

To view a specific ship, use the following command:

```bash
https -A bearer -a $apiToken GET $apiUrl/my/ships/$shipSymbol
```

### Scan nearby waypoints

Your command ship is also equipped with a sensor array, which you can use to scan nearby waypoints. A waypoint is a location in space that you can travel to such as a planet or asteroid field.

You can scan a waypoint to determine what type of location it is, any orbitals that are present, and any traits that it has including whether or not it has a shipyard or marketplace (to buy and sell goods).

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/scan/waypoints
```

```json
{
  "data": {
    "cooldown": {
      "shipSymbol": "DC4ECA-1",
      "totalSeconds": 70,
      "remainingSeconds": 69,
      "expiration": "2022-12-23T22:17:09.284Z"
    },
    "waypoints": [
      {
        "systemSymbol": "X1-OE",
        "symbol": "X1-OE-PM",
        "type": "PLANET",
        "x": -5,
        "y": 9,
        "orbitals": [
          {
            "symbol": "X1-OE-PM01"
          }
        ],
        "traits": [
          {
            "symbol": "OVERCROWDED",
            "name": "Overcrowded",
            "description": ""
          },
          {
            "symbol": "HIGH_TECH",
            "name": "High Tech",
            "description": ""
          },
          {
            "symbol": "BUREAUCRATIC",
            "name": "Bureaucratic",
            "description": ""
          },
          {
            "symbol": "TEMPERATE",
            "name": "Temperate",
            "description": ""
          },
          {
            "symbol": "MARKETPLACE",
            "name": "Marketplace",
            "description": ""
          },
          {
            "symbol": "SHIPYARD",
            "name": "Shipyard",
            "description": ""
          }
        ],
        "chart": {
          "submittedBy": "COMMERCE_REPUBLIC",
          "submittedOn": "2022-12-23T22:15:56.240Z"
        },
        "faction": {
          "symbol": "COMMERCE_REPUBLIC"
        }
      },
      {
        "systemSymbol": "X1-OE",
        "symbol": "X1-OE-PM01",
        "type": "MOON",
        "x": -5,
        "y": 9,
        "orbitals": [],
        "traits": [
          {
            "symbol": "WEAK_GRAVITY",
            "name": "Weak Gravity",
            "description": ""
          },
          {
            "symbol": "SCATTERED_SETTLEMENTS",
            "name": "Scattered Settlements",
            "description": ""
          },
          {
            "symbol": "MARKETPLACE",
            "name": "Marketplace",
            "description": ""
          }
        ],
        "chart": {
          "submittedBy": "COMMERCE_REPUBLIC",
          "submittedOn": "2022-12-23T22:15:56.285Z"
        },
        "faction": {
          "symbol": "COMMERCE_REPUBLIC"
        }
      },
      {
        "systemSymbol": "X1-OE",
        "symbol": "X1-OE-A005",
        "type": "ASTEROID_FIELD",
        "x": 18,
        "y": 25,
        "orbitals": [],
        "traits": [
          {
            "symbol": "COMMON_METAL_DEPOSITS",
            "name": "Common Metal Deposits",
            "description": ""
          }
        ],
        "chart": {
          "submittedBy": "MINERS_COLLECTIVE",
          "submittedOn": "2022-12-23T22:15:56.301Z"
        },
        "faction": {
          "symbol": "MINERS_COLLECTIVE"
        }
      },
      {
        "systemSymbol": "X1-OE",
        "symbol": "X1-OE-EL70",
        "type": "ORBITAL_STATION",
        "x": 42,
        "y": 28,
        "orbitals": [],
        "traits": [
          {
            "symbol": "TRADING_HUB",
            "name": "Trading Hub",
            "description": ""
          },
          {
            "symbol": "MARKETPLACE",
            "name": "Marketplace",
            "description": ""
          }
        ],
        "chart": {
          "submittedBy": "COMMERCE_REPUBLIC",
          "submittedOn": "2022-12-23T22:15:56.308Z"
        },
        "faction": {
          "symbol": "COMMERCE_REPUBLIC"
        }
      },
      {
        "systemSymbol": "X1-OE",
        "symbol": "X1-OE-25X",
        "type": "JUMP_GATE",
        "x": 50,
        "y": -34,
        "orbitals": [],
        "traits": [],
        "chart": {
          "submittedBy": "SPACERS_GUILD",
          "submittedOn": "2022-12-23T22:15:56.321Z"
        },
        "faction": {
          "symbol": "SPACERS_GUILD"
        }
      }
    ]
  }
}
```

You will notice in the response that your ship has a cooldown after executing a scan. Most ship actions such as scanning, mining, or surveying will trigger a ship cooldown, preventing you from performing another action until the cooldown has expired.

#### Viewing Systems and Waypoints

You can also view system and waypoint details using the following endpoints. Note some details are unavailable unless you have a ship present in the system or at the waypoint, such as the exact pricing details of a market or shipyard.

```bash
https -A bearer -a $apiToken GET $apiUrl/systems
https -A bearer -a $apiToken GET $apiUrl/systems/$systemSymbol
https -A bearer -a $apiToken GET $apiUrl/systems/$systemSymbol/waypoints
https -A bearer -a $apiToken GET $apiUrl/systems/$systemSymbol/waypoints/$waypointSymbol
https -A bearer -a $apiToken GET $apiUrl/systems/$systemSymbol/waypoints/$waypointSymbol/market
https -A bearer -a $apiToken GET $apiUrl/systems/$systemSymbol/waypoints/$waypointSymbol/shipyard
https -A bearer -a $apiToken GET $apiUrl/systems/$systemSymbol/waypoints/$waypointSymbol/jump-gate
```

#### Navigate to an asteroid field

Find a waypoint with a type of ASTEROID_FIELD and navigate your command ship to it. Once your ship has arrived, we will be able to survey and mine the asteroid field for valuable minerals and ores.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/navigate \
  waypointSymbol=$waypointSymbol
```

```json
{
  "data": {
    "nav": {
      "systemSymbol": "X1-OE",
      "waypointSymbol": "X1-OE-A005",
      "route": {
        "departure": {
          "symbol": "X1-OE-PM",
          "type": "PLANET",
          "systemSymbol": "X1-OE",
          "x": -6,
          "y": -7
        },
        "destination": {
          "symbol": "X1-OE-A005",
          "type": "ASTEROID_FIELD",
          "systemSymbol": "X1-OE",
          "x": -29,
          "y": 1
        },
        "arrival": "2022-12-23T18:27:01.514Z"
      },
      "status": "IN_TRANSIT",
      "flightMode": "CRUISE"
    },
    "fuel": {
      "current": 176,
      "capacity": 100,
      "consumed": {
        "amount": 24,
        "timestamp": "2022-12-23T18:14:46.515Z"
      }
    }
  }
}
```

Note the route departure and destination details. The departure is the waypoint your ship is currently at, and the destination is the waypoint you are navigating to. The arrival timestamp is the time when your ship will arrive at the destination. While your ship is in-transit, you will not be able to take any other actions.

#### Refuel your ship

You will also notice that your ship has consumed fuel upon navigation. You will want to refuel your ship whenever possible or risk getting stranded in space. Once your ship arrives at it's destination, you can refuel it using the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/refuel
```

```json
{
  "data": {
    "agent": {
      "accountId": "clc0u1cxh0000p4z0l6k6vuol",
      "symbol": "860A44",
      "headquarters": "X1-OE-PM",
      "credits": 999912799
    },
    "fuel": {
      "current": 900,
      "capacity": 900,
      "consumed": {
        "amount": 100,
        "timestamp": "2022-12-23T18:14:46.032Z"
      }
    }
  }
}
```

### Asteroid Mining

Asteroids contain valuable minerals and ores that can be extracted and sold for profit. You can mine asteroids using the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/extract
```

```json
{
  "data": {
    "extraction": {
      "shipSymbol": "0B03EA-1",
      "yield": {
        "symbol": "ALUMINUM_ORE",
        "units": 1
      }
    },
    "cooldown": {
      "shipSymbol": "0B03EA-1",
      "totalSeconds": 70,
      "remainingSeconds": 69,
      "expiration": "2022-12-23T18:15:59.212Z"
    },
    "cargo": {
      "capacity": 30,
      "units": 30,
      "inventory": [
        {
          "symbol": "ALUMINUM_ORE",
          "name": "Aluminum Ore",
          "description": "A valuable ore used in the production of aluminum and other alloys. Aluminum ore is an essential component in the construction of ship hulls and other structural components.",
          "units": 30
        }
      ]
    }
  }
}
```

If you are targeting specific minerals or ores, you will want to survey the asteroid field for targeted mining locations.

#### Surveying

Surveying an asteroid field will reveal the locations of specific mineral and ore deposits. You can survey an asteroid field using the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/survey
```

```json
{
  "data": {
    "cooldown": {
      "shipSymbol": "7C8E51-1",
      "totalSeconds": 69,
      "remainingSeconds": 69,
      "expiration": "2022-12-23T18:15:59.073Z"
    },
    "surveys": [
      {
        "signature": "X1-OE-A005-99501F",
        "symbol": "X1-OE-A005",
        "deposits": [
          {
            "symbol": "ALUMINUM_ORE"
          },
          {
            "symbol": "ICE_WATER"
          },
          {
            "symbol": "SILICON_CRYSTALS"
          }
        ],
        "expiration": "2022-12-23T19:04:01.088Z",
        "size": "SMALL"
      }
    ]
  }
}
```

Copy the entire object of the survey you want to target. You will need this information to extract those deposits from the asteroid field.

#### Extracting from surveys

Once you have surveyed an asteroid field, you can extract specific minerals and ores from the field using the following endpoint.

Note you will need to paste the entire survey object as a JSON string in the body of the request.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/extract \
  survey="{\"signature\":\"X1-OE-A005-99501F\",\"symbol\":\"X1-OE-A005\",\"deposits\":[{\"symbol\":\"ALUMINUM_ORE\"},{\"symbol\":\"ICE_WATER\"},{\"symbol\":\"SILICON_CRYSTALS\"}],\"expiration\":\"2022-12-23T19:04:01.088Z\",\"size\":\"SMALL\"}"
```

The ship will have a much higher chance of extracting ores and minerals from the list of deposits in the survey.

### Contract Delivery

Once your cargo hold is full and you have some of the desired minerals and ores for your contract, you should navigate to the destination waypoint of your contract.

Upon arrival, you can deliver the goods using the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/contracts/$contractId/deliver \
  shipSymbol=$shipSymbol \
  tradeSymbol=$tradeSymbol \
  units=999
```

```json
{
  "data": {
    "contract": {
      "id": "clc0u1d210005p4ys90philn5",
      "factionSymbol": "COMMERCE_REPUBLIC",
      "type": "PROCUREMENT",
      "terms": {
        "deadline": "2022-12-23T18:15:46.639Z",
        "payment": {
          "onAccepted": 58800,
          "onFulfilled": 235200
        },
        "deliver": [
          {
            "tradeSymbol": "QUARTZ_SAND",
            "destinationSymbol": "X1-OE-PM",
            "unitsRequired": 14000,
            "unitsFulfilled": 1
          }
        ]
      },
      "accepted": true,
      "fulfilled": false,
      "expiration": "2022-12-26T18:14:46.103Z"
    },
    "cargo": {
      "capacity": 30,
      "units": 999,
      "inventory": [
        {
          "symbol": "QUARTZ_SAND",
          "name": "Quartz Sand",
          "description": "High-purity quartz sand used in the production of glass and ceramics.",
          "units": 499
        }
      ]
    }
  }
}
```

Once the terms of the contract have been met, you can fulfill the contract and receive the final payment using the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/contracts/$contractId/fulfill
```

```json
{
  "data": {
    "contract": {
      "id": "clc0u1d210005p4ys90philn5",
      "factionSymbol": "COMMERCE_REPUBLIC",
      "type": "PROCUREMENT",
      "terms": {
        "deadline": "2022-12-23T18:15:46.639Z",
        "payment": {
          "onAccepted": 58800,
          "onFulfilled": 235200
        },
        "deliver": [
          {
            "tradeSymbol": "QUARTZ_SAND",
            "destinationSymbol": "X1-OE-PM",
            "unitsRequired": 14000,
            "unitsFulfilled": 999999
          }
        ]
      },
      "accepted": true,
      "fulfilled": true,
      "expiration": "2022-12-26T18:14:46.103Z"
    },
    "agent": {
      "accountId": "clc0u1cvk0000p4ysdxjcuy2e",
      "symbol": "595559",
      "headquarters": "X1-OE-PM",
      "credits": 1000208559
    }
  }
}
```

### Markets

You will likely extract some minerals and ores that you don't need for your current contract. You can sell these minerals and ores at the market.

Find a waypoint with a market and check if it has the minerals and ores you want to sell listed as imports. Imports typically have a higher price than exports or goods generally available at the market.

```bash
https -A bearer -a $apiToken GET $apiUrl/systems/$systemSymbol/waypoints/$waypointSymbol/market
```

```json
{
  "data": {
    "symbol": "X1-ZZ-7-EE",
    "imports": [
      {
        "symbol": "HYDROCARBON"
      },
      {
        "symbol": "AMMONIA_ICE"
      },
      {
        "symbol": "LIQUID_NITROGEN"
      }
    ],
    "exports": [
      {
        "symbol": "POLYNUCLEOTIDES"
      }
    ],
    "transactions": []
  }
}
```

#### Sell and Purchase

When your ship is at a designated market waypoint, you can sell goods using the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/sell \
  symbol=$tradeSymbol \
  units=999
```

```json
{
  "data": {
    "agent": {
      "accountId": "clc0u1cvr0000p4yvjcbbdtkr",
      "symbol": "ED3EFE",
      "headquarters": "X1-OE-PM",
      "credits": 999914595
    },
    "cargo": {
      "capacity": 30,
      "units": 30,
      "inventory": [
        {
          "symbol": "SILICON_CRYSTALS",
          "name": "Silicon Crystals",
          "description": "High-quality silicon crystals used in the production of advanced electronic components and solar panels.",
          "units": 9
        }
      ]
    },
    "transaction": {
      "shipSymbol": "ED3EFE-1",
      "tradeSymbol": "SILICON_CRYSTALS",
      "type": "SELL",
      "units": 1,
      "pricePerUnit": 36,
      "totalPrice": 36,
      "timestamp": "2022-12-23T18:14:46.413Z"
    }
  }
}
```

If you want to purchase any goods from the market, you can use the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/purchase \
  symbol=$tradeSymbol \
  units=10
```

### Purchase Ships

You can expedite your mining exports by purchasing a new ship. Find a waypoint with a shipyard and view available ships with the following endpoint.

```bash
https -A bearer -a $apiToken $apiUrl/systems/$systemSymbol/waypoints/$waypointSymbol/shipyard
```

```json
{
  "data": {
    "symbol": "X1-OE-PM",
    "shipTypes": [
      {
        "type": "SHIP_MINING_DRONE"
      }
    ]
  }
}
```

Find a shipyard with a `SHIP_MINING_DRONE` available and purchase it with the following endpoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships \
  shipType=SHIP_MINING_DRONE \
  waypointSymbol=$waypointSymbol
```

```json
{
  "data": {
    "agent": {
      "accountId": "clc0u1cok0000p4ytz33fltey",
      "symbol": "8C7441",
      "headquarters": "X1-OE-PM",
      "credits": 999401919
    },
    "ship": {
      "symbol": "8C7441-7",
      "nav": {
        "systemSymbol": "X1-OE",
        "waypointSymbol": "X1-OE-PM",
        "route": {
          "departure": {
            "symbol": "X1-OE-PM",
            "type": "PLANET",
            "systemSymbol": "X1-OE",
            "x": -6,
            "y": -7
          },
          "destination": {
            "symbol": "X1-OE-PM",
            "type": "PLANET",
            "systemSymbol": "X1-OE",
            "x": -6,
            "y": -7
          },
          "arrival": "2022-12-23T18:14:46.289Z"
        },
        "status": "DOCKED",
        "flightMode": "CRUISE"
      },
      "crew": {
        "current": 0,
        "capacity": 0,
        "required": 0,
        "rotation": "STRICT",
        "morale": 100,
        "wages": 0
      },
      "fuel": {
        "current": 100,
        "capacity": 100,
        "consumed": {
          "amount": 0,
          "timestamp": "2022-12-23T18:14:46.289Z"
        }
      },
      "frame": {
        "symbol": "FRAME_DRONE",
        "name": "Frame Drone",
        "description": "A small, unmanned spacecraft used for various tasks, such as surveillance, transportation, or combat.",
        "moduleSlots": 3,
        "mountingPoints": 2,
        "fuelCapacity": 100,
        "condition": 100,
        "requirements": {
          "power": 1,
          "crew": -3
        }
      },
      "reactor": {
        "symbol": "REACTOR_CHEMICAL_I",
        "name": "Chemical Reactor I",
        "description": "A basic chemical power reactor, used to generate electricity from chemical reactions.",
        "condition": 100,
        "powerOutput": 14,
        "requirements": {
          "crew": 3
        }
      },
      "engine": {
        "symbol": "ENGINE_IMPULSE_DRIVE_I",
        "name": "Impulse Drive I",
        "description": "A basic low-energy propulsion system that generates thrust for interplanetary travel.",
        "condition": 100,
        "speed": 2,
        "requirements": {
          "power": 1,
          "crew": 0
        }
      },
      "modules": [
        {
          "symbol": "MODULE_CARGO_HOLD_I",
          "name": "Cargo Hold",
          "description": "A module that increases a ship's cargo capacity.",
          "capacity": 30,
          "requirements": {
            "crew": 0,
            "power": 1,
            "slots": 1
          }
        },
        {
          "symbol": "MODULE_MINERAL_PROCESSOR_I",
          "name": "Mineral Processor",
          "description": "A module that processes raw ore and minerals into refined materials.",
          "requirements": {
            "crew": 0,
            "power": 1,
            "slots": 2
          }
        }
      ],
      "mounts": [
        {
          "symbol": "MOUNT_MINING_LASER_I",
          "name": "Mining Laser I",
          "description": "A basic mining laser that can be used to extract valuable minerals from asteroids and other space objects.",
          "strength": 10,
          "requirements": {
            "crew": 0,
            "power": 1
          }
        }
      ],
      "registration": {
        "name": "8C7441-7",
        "factionSymbol": "COMMERCE_REPUBLIC",
        "role": "EXCAVATOR"
      },
      "cargo": {
        "capacity": 30,
        "units": 0,
        "inventory": []
      }
    }
  }
}
```

Drones are quite a bit slower than other ships, but they require zero crew to operate which means they are easier to automate and maintain.

### Exploration

If you want to continue to explore the universe, you can use the warp and jump drives on your command ship to travel to new systems. Warp drives are slower and consume regular fuel, where as jump drives will instantly transport you to a new system but require a unit of antimatter to operate.

#### Scan nearby systems

If you want to explore nearby systems, you can use your sensor array to scan for new systems. You can see how far away the system is from your current location by looking at the `distance` property. The `type` property will tell you what type of star the system has.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/scan/systems
```

```json
{
  "data": {
    "cooldown": {
      "shipSymbol": "DC4ECA-1",
      "totalSeconds": 69,
      "remainingSeconds": 69,
      "expiration": "2022-12-23T22:17:09.240Z"
    },
    "systems": [
      {
        "symbol": "X1-EV",
        "sectorSymbol": "X1",
        "type": "ORANGE_STAR",
        "x": 1,
        "y": 1,
        "distance": 1
      },
      {
        "symbol": "X1-ZZ",
        "sectorSymbol": "X1",
        "type": "BLUE_STAR",
        "x": 2,
        "y": 2,
        "distance": 3
      }
    ]
  }
}
```

#### Jump to a new system

Choose a system to jump to and use the `jump` command to jump to that system. You will need to have a jump drive installed on your ship and you will need to have a unit of antimatter in your cargo hold.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/jump systemSymbol=$systemSymbol
```

```json
{
  "data": {
    "nav": {
      "systemSymbol": "X1-EV",
      "waypointSymbol": "X1-EV-A004",
      "route": {
        "departure": {
          "symbol": "X1-OE-PM",
          "type": "PLANET",
          "systemSymbol": "X1-OE",
          "x": -6,
          "y": -7
        },
        "destination": {
          "symbol": "X1-EV-A004",
          "type": "PLANET",
          "systemSymbol": "X1-EV",
          "x": 3,
          "y": 10
        },
        "arrival": "2022-12-23T18:14:49.168Z"
      },
      "status": "IN_ORBIT",
      "flightMode": "CRUISE"
    },
    "cooldown": {
      "shipSymbol": "04A691-1",
      "totalSeconds": 99,
      "remainingSeconds": 99,
      "expiration": "2022-12-23T18:16:29.164Z"
    }
  }
}
```

If you don't have antimatter but you have a warp drive installed, you can also use the `warp` command to travel to a new system. This will take longer and consume fuel, but you won't need to have antimatter in your cargo hold.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/warp waypointSymbol=$waypointSymbol
```

#### Chart a waypoint

If you discover an uncharted waypoint, you can use the `chart` command to chart it. This will allow other agents to view more details about the waypoint.

```bash
https -A bearer -a $apiToken POST $apiUrl/my/ships/$shipSymbol/chart
```

```json
{
  "data": {
    "chart": {
      "waypointSymbol": "X1-ZZ-Z9185A",
      "submittedBy": "E0783A-1",
      "submittedOn": "2022-12-23T18:14:46.699Z"
    },
    "waypoint": {
      "systemSymbol": "X1-ZZ",
      "symbol": "X1-ZZ-Z9185A",
      "type": "GAS_GIANT",
      "x": 1005,
      "y": 2569,
      "orbitals": [],
      "traits": [],
      "chart": {
        "waypointSymbol": "X1-ZZ-Z9185A",
        "submittedBy": "E0783A-1",
        "submittedOn": "2022-12-23T18:14:46.699Z"
      },
      "faction": {
        "symbol": "ZANZIBAR_TRIKES"
      }
    }
  }
}
```
