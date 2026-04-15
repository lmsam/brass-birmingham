---
name: brass-rules
description: Provides comprehensive rules, structural mechanics, network adjacency requirements, scoring algorithms, and limitations for Brass: Birmingham. Use this skill when interpreting rules, enforcing game constants, checking turn logic, evaluating build conditions, writing AI engines, or updating game mechanics.
---

# Brass: Birmingham — Game Mechanics & Rules Workspace Skill

Use this document to enforce constraints, architect gameplay features, and implement AI logic. This represents the absolute ground-truth logic for the Brass Birmingham engine implementation.

## 1. Game Structure & Turn Order
- **Length**: The game is split into two halves: the **Canal Era (1770-1830)** and the **Rail Era (1830-1870)**.
- **Actions per Turn**:
  - The *very first round* of the Canal Era allows only **1 action** per player.
  - All subsequent rounds in both eras give **2 actions** per player.
  - Doing an action always requires discarding **1 card** from the player's hand.
- **Turn Order**: Turn order is determined dynamically at the end of each round based on **total money spent** during that round. The player who spent the *least* money goes *first*. Ties are broken by maintaining the relative order from the previous turn.

## 2. Resource Logistics

Resource transportation is the core puzzle of Brass: Birmingham. The rules vary wildly between the three main resources.

### Coal ⛏️
- Must be traced by an **unbroken chain of connected links** (canals/rails) to a valid source.
- Valid sources: An unflipped Coal Mine tile on the board, OR the Merchant (if the location is connected to a Merchant via links with empty space on the market).
- Every rail link costs 1 Coal. If taken from a player's tile, it's free. If taken from the Merchant, the cost is the current market price.

### Iron 🔩
- Iron is "teleported". It **does not require a network connection**.
- To consume Iron (e.g. for building a Cotton Mill or Developing), take it from **any** unflipped Iron Works anywhere on the board.
- If no board Iron exists, purchase it from the Iron Market (paying the market price) from anywhere on the board.

### Beer 🍺
- Beer is strictly required for the **Sell** action and for building **Double Rail** links.
- Sourcing rules:
  1. **Own Beer**: You can use beer from *your own* Breweries **without a network connection**.
  2. **Opponent Beer**: You can use an opponent's beer *only if* it is connected via the **network** to your target location.
  3. **Merchant Beer**: Each merchant provides 1 free barrel of beer, consumed during a Sell action *only if* your selling industry is connected to that merchant via links.

## 3. The Network Edge
A location is considered **"in your network"** if:
1. You have an industry tile built in that location.
2. It is immediately adjacent to one of your network link tiles (canal/rail).
*Note: Your first action of the game can be placed anywhere, ignoring network constraints.*

## 4. The Six Actions
Every action requires discarding exactly 1 card.

1. **Build**:
   - Location cards allow placing an industry in the specified city (ignoring your network).
   - Industry cards allow placing *that specific industry* anywhere **within your established network**.
   - **Cost**: Pay listed money, plus required Coal/Iron.
   - **Canal Era Limit**: A player can only have **1 tile** (total) in any given city.
   - **Rail Era Limit**: A player can place multiple tiles in the same city if there are empty slots.
2. **Network**:
   - **Canal Era**: Pay £3 to place 1 canal link between two cities.
   - **Rail Era**: Pay £5 + 1 Coal to place 1 rail link, OR Pay £15 + 1 Coal + 1 Beer to place 2 railways in a single action.
   - Links must always connect to an end of your existing network.
3. **Develop**:
   - Discard 1 to 2 industry tiles of the lowest level from your player mat.
   - Costs **1 Iron per tile** removed.
   - Removes obsolete tiles so higher-level tiles can be built.
   - Note: Pottery Levels I and III have a "lightbulb" icon; they *cannot* be developed and must be built.
4. **Sell**:
   - Sell eligible flipped industries (Cotton, Manufacturer, Pottery).
   - Must be connected to a merchant who buys that good type.
   - Consumes the required number of Beer. If successful, tile flips and provides Income/VP. Merchant connection bonuses are awarded immediately.
5. **Loan**:
   - Take £30 from the bank.
   - Instantly drop your income marker by 3 distinct color bands (levels) on the income track.
   - Cannot be taken if income is below -10.
6. **Scout**:
   - Discard 3 cards from your hand.
   - Take 1 Wild Location and 1 Wild Industry action card.
   - You cannot do this if you currently hold any Wild cards.

## 5. Overbuilding Rules
- **Own Tiles**: You can replace your own tile with a higher-level tile of the *exact same type*.
- **Opponent Tiles**: Normally, you cannot overbuild an opponent's tile.
  - *Exception*: You can overbuild an opponent's Coal Mine or Iron Works if and only if that specific resource is mathematically completely depleted (0 cubes on board, 0 in market). You must still build a higher level tile of the same type.

## 6. Era Transitions & Scoring
An Era ends when all players have empty hands and the draw deck is empty.

### Scoring Structure:
1. **Link VP**: Every Canal/Rail link scores points equal to the number of VP medals printed on the **flipped** industries connected to both sides of that link.
2. **Industry VP**: Every flipped industry on the board (base VP printed on tile).

### Canal Era Cleanup:
- Score VP.
- Remove *all* Canal Links from the board.
- Remove *all* Level 1 Industry Tiles from the board (even if unflipped). Level II+ tiles stay on the board and can be scored *again* in the Rail Era.

### End of Game:
- Rail era scoring is identical to Canal era scoring.
- Add current Income Level value directly to VP.
- Add remaining cash ratio (e.g. +1 VP for every £10) depending on strict rule interpretation if breaking ties. Player with the highest VP wins. Tiebreaker is highest income tier, then most money remaining.

---
**Usage**: When modifying `gameLogic.js`, `gameState.js`, or `uiManager.js`, query these rules to determine if a check (like `canAffordTwo` or `hasCardForBuild`) aligns with the raw board game mechanics described above.
