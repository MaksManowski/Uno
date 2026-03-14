# Analysing Optimal Play in UNO: Human Strategies using Computational Modelling

This project simulates UNO games to analyze and compare various human-inspired strategies for optimal play. It was developed for the presentation "Analysing Optimal Play in UNO: Human Strategies using Computational Modelling" at the IMA TMT 2026 conference.

For more details on the conference, visit: [https://sites.google.com/view/ima-tmt-2026/programme](https://sites.google.com/view/ima-tmt-2026/programme)

## Overview

The `project.ipynb` Jupyter notebook implements a computational model of the UNO card game using Python with NumPy and for analysis: Pandas, Matplotlib, and Seaborn. It defines the game rules (based on the 2022 Mattel ruleset), simulates multiplayer games, and evaluates strategies through extensive trials.

### Key Components

- **Card Representation**: Cards are encoded numerically (e.g., colors as multiples of 15, values 0-14 for numbers, skips, reverses, etc.).
- **UnoGame Class**: Manages game state, deck shuffling, player turns, and special card effects (e.g., skips, reverses, draw cards).
- **Player Strategies**: Implements 9 strategies:
  - `low_value_first`: Plays lowest value cards first.
  - `high_value_first`: Plays highest value cards first.
  - `color_streak`: Focuses on playing cards of the same color.
  - `planned_finish`: Plans endgame moves to match numbers.
  - `random_play`: Random valid card selection.
  - `dynamic_best`: Scores cards based on dominance, duplicates, and opponent threats.
  - `card_counting`: Tracks played colors to choose rare ones.
  - `opponent_block`: Uses attack cards when opponents are close to winning.
  - `risk_aware`: Avoids frequently played values.
- **Simulations**:
  - Strategy vs. starting position: Analyzes win probabilities based on who goes first.
  - Strategy vs. strategy: Head-to-head comparisons with random starts.
  - Strategy vs. number of opponents: Tests performance in games with 2 to 7 players.
- **Visualizations**: Heatmaps and tables showing win rates, probabilities, and normalized strengths.

### Running the Notebook

1. Ensure Python 3.x is installed with required libraries: `pip install numpy pandas matplotlib seaborn`.
2. Open `project.ipynb` in Jupyter Notebook or JupyterLab.
3. Run cells sequentially to simulate games and generate plots.
4. Adjust `trials` variable for more/less simulation runs (default: 1000).

### Results Insights

- Strategies like `color_streak` often perform well due to being offensive and defensive at the same time.
- Starting position and opponent count significantly impact win rates.
- Visualizations highlight optimal strategies in various scenarios.

This model provides insights into strategic decision-making in UNO, bridging human intuition with computational analysis.

### Adding New Strategies

To extend the model with custom strategies, you can add them to `project.ipynb`. There are two main approaches: basic strategies using the `@strategy` decorator and complex strategies by overriding the `Player` class.

#### Basic Strategies with @strategy Decorator

Use the `@strategy("strategy_name")` decorator to define a simple strategy function. The function must accept the following parameters:
- `player`: The Player instance (for setting `pick_color` if needed).
- `playable_cards`: Array of valid cards the player can play.
- `player_cards`: Array of cards in the player's hand.
- `pile`: Array of cards on the discard pile (last card is the current one).
- `played`: Array of all cards played so far.
- `draw`: Number of cards the next player must draw (0 if none).
- `players`: List of card counts for all players (starting from current player in play order).

The function should return the chosen card from `playable_cards`. For wild cards, set `player.pick_color` to the desired color (0-3 for blue/green/red/yellow).

Example:
```python
@strategy("my_strategy")
def my_choice(player, playable_cards, player_cards, pile, played, draw, players):
    # Simple logic: play the first playable card
    card = playable_cards[0]
    if card // 15 == 4:  # Wild card
        player.pick_color = 0  # Choose blue
    return card
```

Using the decorator will automatically add the strategy to the `strategies` list.

#### Complex Strategies by Overriding Player Class

For strategies needing state (e.g., memory across turns), subclass `Player` and override methods:
- `simulate_start()`: Reset state at game start.
- `choice(playable_cards, player_cards, pile, played, draw, players)`: Return the chosen card.
- `choice_color(playable_cards, player_cards, pile, played, draw, players)`: Return the color (0-3) for wild cards.

Example:
```python
class MyComplexPlayer(Player):
    def __init__(self):
        super().__init__("my_complex_strategy")
        self.memory = []  # Track something
    
    def simulate_start(self):
        self.memory = []
    
    def choice(self, playable_cards, player_cards, pile, played, draw, players):
        # Custom logic using self.memory
        self.memory.append(len(playable_cards))
        return playable_cards[0]
    
    def choice_color(self, playable_cards, player_cards, pile, played, draw, players):
        return 1  # Green

# Add to strategies list
strategies.append(MyComplexPlayer())
```

After adding, re-run simulations to evaluate the new strategy against others.
