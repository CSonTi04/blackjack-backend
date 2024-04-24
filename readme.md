

## Design document
### Simple Blackjack State Machine
This state machine outlines the core gameplay loop for a Blackjack game with one player and a dealer.

#### States:

<ul>
    <li>Start: The initial state where bets are placed.</li>
    <li>Deal: Cards are dealt to the player and dealer.</li>
    <li>PlayerTurn: Player decides to hit (take another card) or stand (stay with current hand).</li>
    <li>DealerTurn: Dealer draws cards according to pre-defined rules.</li>
    <li>Evaluate: Winner is determined based on hand values.</li>
    <li>End: Game ends, payouts are made (if applicable), and option to restart is presented.
</ul>

#### Transitions:
<ul>
    <li>Start &rarr; Deal: Player places a bet, triggering the deal of cards.</li>
    <li>Deal &rarr; PlayerTurn: Player receives cards and decides to hit or stand.</li>
    <li>PlayerTurn (Hit): Player takes another card, loop back to PlayerTurn.</li>
    <li>PlayerTurn (Stand): Player finishes turn, transition to DealerTurn.</li>
    <li>DealerTurn: Dealer draws cards based on rules. If bust (over 21), transition to Evaluate. Otherwise, loop back to DealerTurn.</li>
    <li>DealerTurn (Done): Dealer finishes turn, transition to Evaluate.</li>
    <li>Evaluate: Winner is determined. Payouts are made (if applicable).</li>
    <li>Evaluate &rarr; End: Game ends, option to restart.</li>
    <li>End &rarr; Start: Player chooses to restart, returning to the beginning with a new bet.</li>
</ul>

#### Additional Notes:

The state machine can be extended to handle multiple players by adding additional "PlayerTurn" states.
Dealer rules (when to hit or stand) can be implemented within the "DealerTurn" state.
The "Evaluate" state can handle different win/loss scenarios like blackjack, bust, or exceeding dealer's hand value.


### Blackjack REST API Design
This document outlines a REST API design for a Blackjack game with a single player and dealer, inspired by the state machine concept.

#### Resources:

<ul>
    <li>Game: Represents a single Blackjack game instance.</li>
    <li>Player: Represents the player participating in the game.</li>
</ul>

#### Endpoints:

## Resources

* **Game:** Represents a single Blackjack game instance.
* **Player:** Represents the player participating in the game.

## Endpoints

**1. Game:**

* **POST /games:** Creates a new Blackjack game instance. No body required in the request.
    * Response (Success):
        * Status: 201 Created
        * Body: JSON object containing game details like game ID, current state ("Start"), and minimal player information (e.g., ID).

* **GET /games/{gameId}:** Retrieves details about an existing game.
    * Path Parameter: `{gameId}` - Unique identifier for the game.
    * Response:
        * Status: 200 OK - Returns a JSON object containing game details (state, player info, dealer cards if revealed, etc.).
        * Status: 404 Not Found - If the game with `{gameId}` doesn't exist.

**2. Player:**

* **PUT /games/{gameId}/player/bet?amount={betAmount}:** Player places a bet on the current game.
    * Path Parameter: `{gameId}` - Unique identifier for the game.
    * Query Parameter: `{betAmount}` - Amount of the bet (integer).
    * Response:
        * Status: 200 OK - If the bet is placed successfully. The response body should update the game state to "Deal" and include initial cards dealt to both player and dealer (some may be hidden).
        * Status: 400 Bad Request - If the bet amount is invalid or the game is not in the "Start" state.

**3. Player Actions:**

* **POST /games/{gameId}/player/hit:** Player requests another card.
    * Path Parameter: `{gameId}` - Unique identifier for the game.
    * Response:
        * Status: 200 OK - If the card is dealt successfully. The response body should update the game state and player hand details.
        * Status: 400 Bad Request - If the game is not in the "PlayerTurn" state.

* **POST /games/{gameId}/player/stand:** Player decides to stand with their current hand.
    * Path Parameter: `{gameId}` - Unique identifier for the game.
    * Response:
        * Status: 200 OK - If the player stands successfully. The response body should update the game state to "DealerTurn" and potentially reveal the dealer's hidden card.
        * Status: 400 Bad Request - If the game is not in the "PlayerTurn" state.

### Additional Considerations

* Implement error handling for various scenarios (e.g., invalid game ID, insufficient funds for bet).
* Include authentication mechanisms if user accounts are involved.
* Consider adding endpoints for game history or player statistics (optional).
* Responses should follow a consistent format (e.g., JSON) containing relevant game data.

This design provides a basic structure for a Blackjack game API. You can expand on it to include additional functionalities like insurance bets, splitting hands, or multiple players.
