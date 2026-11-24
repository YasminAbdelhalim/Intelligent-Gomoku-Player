# Intelligent Gomoku Player

An AI opponent for **Gomoku (Five in a Row)**, built in Python using the **Minimax algorithm**, **Alpha-Beta pruning**, and **heuristic evaluation functions**.

> **Course:** AI310 / CS361 Artificial Intelligence, Fall Semester 2026-2027
> **Institution:** Capital University (formerly Helwan University), Faculty of Computing & Artificial Intelligence
> **Project type:** Group project (6 members)

---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Game Rules](#game-rules)
4. [How the AI Works](#how-the-ai-works)
5. [Project Structure](#project-structure)
6. [Installation](#installation)
7. [Usage](#usage)
8. [Difficulty Levels](#difficulty-levels)
9. [Experiments and Results](#experiments-and-results)
10. [Module Interfaces](#module-interfaces)
11. [Testing](#testing)
12. [Team and Responsibilities](#team-and-responsibilities)
13. [Development Platform](#development-platform)
14. [References](#references)
15. [Academic Integrity](#academic-integrity)

---

## Overview

Gomoku is a two-player strategy board game played on a grid (traditionally 15x15). Players alternate placing stones on empty intersections, with Black moving first. The first player to form an unbroken line of five stones horizontally, vertically, or diagonally wins.

This project implements a complete Gomoku game in which a human plays against an intelligent AI. The AI looks ahead through possible move sequences, prunes unpromising branches, and evaluates board positions with a custom heuristic function, while staying fast enough to respond in real time.

## Features

- **Minimax search** with configurable depth
- **Alpha-Beta pruning** for faster decision making
- **Heuristic evaluation** covering threats, defensive moves, and strong positions
- **Move ordering and candidate-move filtering** to reduce the search space
- **Iterative deepening with time limits** for real-time responses
- **Adaptive difficulty levels** (Easy, Medium, Hard)
- **User-selectable board size (n)**
- **Graphical user interface** for playing against the AI
- **Rule variants:** free-style (six or more also wins) or exact-five (overlines do not count)
- **Experiment scripts** to benchmark and compare algorithms

## Game Rules

| Rule | Description |
|------|-------------|
| Board | Default 15x15 grid; size `n` is user-selectable |
| Players | Two players; Black moves first |
| Turns | Players alternate placing one stone on an empty intersection |
| Win | First player to make an unbroken row of five stones (horizontal, vertical, or diagonal) |
| Draw | The board is full and nobody has won |
| Overline (optional) | In exact-five mode, a line of six or more stones does not count as a win |

## How the AI Works

### 1. Minimax
Minimax searches the game tree to a fixed depth. The AI (maximiser) picks the move that gives the highest score, assuming the opponent (minimiser) always replies with the move that is worst for the AI.

### 2. Alpha-Beta Pruning
Alpha-Beta keeps track of the best score already guaranteed for each player (`alpha` and `beta`) and stops exploring a branch as soon as it cannot affect the final decision. It returns the same move as plain Minimax while visiting far fewer nodes.

### 3. Heuristic Evaluation
Because the game cannot be searched to the end, positions at the depth limit are scored by a heuristic that rewards patterns such as:

- Fives (win) and open fours (forced win)
- Open and closed threes and twos
- Blocking the opponent's fours and open threes
- Central and connected positions

Attack and defense weights are tuned separately, and the pattern scores are the main lever for changing playing strength.

### 4. Optimisations
- **Candidate moves:** only empty cells near existing stones are considered
- **Move ordering:** promising moves are searched first so pruning cuts more branches
- **Iterative deepening:** search depth increases until the time limit is reached
- **Time limit per move:** guarantees a response in real time

## Project Structure

```
gomoku-ai/
├── README.md
├── requirements.txt
├── main.py                  # Entry point
├── gomoku/
│   ├── __init__.py
│   ├── board.py             # Board representation, move validation, win/draw detection
│   ├── minimax.py           # Minimax search
│   ├── alphabeta.py         # Alpha-Beta pruning, move ordering, iterative deepening
│   ├── heuristics.py        # Evaluation function and pattern scoring
│   ├── ai_player.py         # AI entry point: get_best_move(board, player, level)
│   ├── difficulty.py        # Difficulty level configuration
│   └── ui.py                # Graphical interface
├── experiments/
│   ├── benchmark.py         # Minimax vs Alpha-Beta comparison
│   ├── self_play.py         # AI vs AI win-rate experiments
│   └── results/             # Generated tables and charts
├── tests/
│   ├── test_board.py
│   ├── test_search.py
│   └── test_heuristics.py
├── docs/
│   ├── diagrams/            # UML and flow diagrams
│   └── report/              # Final report and slides
└── assets/                  # Screenshots and images
```

> Adjust file names to match your actual repository.

## Installation

### Requirements
- Python 3.10 or newer
- Packages listed in `requirements.txt` (for example `pygame` or `matplotlib`, depending on your final choices)

### Setup

```bash
# Clone the repository
git clone <repository-url>
cd gomoku-ai

# (Optional) create a virtual environment
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

## Usage

### Play the game

```bash
python main.py
```

In the start menu you can:
1. Choose the **board size** (n)
2. Choose the **difficulty level**
3. Choose whether you play **Black (first)** or **White**
4. Click an empty intersection to place your stone

The game ends when a player makes five in a row or the board is full.

### Run experiments

```bash
# Compare Minimax and Alpha-Beta (nodes visited, time per move)
python experiments/benchmark.py

# AI vs AI win rates across difficulty levels
python experiments/self_play.py
```

Results are saved to `experiments/results/`.

## Difficulty Levels

The AI adapts its strength by changing search depth and heuristic quality.

| Level | Search depth | Heuristic | Intended player |
|-------|--------------|-----------|-----------------|
| Easy | Shallow (for example 1-2) | Simplified, with some randomness | Beginners |
| Medium | Moderate (for example 3) | Full patterns | Casual players |
| Hard | Deep, with iterative deepening | Full patterns, tuned weights | Experienced players |

> Fill in the final values after tuning.

## Experiments and Results

The following experiments are planned for the report:

1. **Minimax vs Alpha-Beta:** nodes visited and time per move at the same depth
2. **Effect of move ordering:** pruning efficiency with and without ordering
3. **Difficulty levels:** win rates from AI vs AI matches between levels
4. **Scalability:** response time for different board sizes (for example 9, 13, 15, 19)
5. **Heuristic tuning:** effect of attack/defense weights on win rate

| Experiment | Result |
|------------|--------|
| Minimax vs Alpha-Beta nodes | _to be added_ |
| Average time per move (Hard) | _to be added_ |
| Win rate Hard vs Medium | _to be added_ |

**Dataset:** Gomoku does not need a training dataset. Evaluation data is produced by self-play matches between AI configurations. _(Confirm with the teaching assistant if public game records, such as Gomocup archives, are also required.)_

## Module Interfaces

Agreed interfaces so modules can be developed in parallel:

```python
# Board: 2D list, 0 = empty, 1 = black, 2 = white

# Engine (board.py)
is_valid_move(board, row, col) -> bool
make_move(board, row, col, player) -> None
check_winner(board) -> int          # 0 = none, 1 = black, 2 = white
get_empty_cells(board) -> list[tuple[int, int]]

# AI (ai_player.py)
get_best_move(board, player, level) -> tuple[int, int]
```

## Testing

```bash
python -m unittest discover tests
```

Each module has its own unit tests covering normal cases and edge cases, such as wins at the board edge, overlines, a full board, and different board sizes.

## Team and Responsibilities

| # | Member | Role | Responsibilities |
|---|--------|------|------------------|
| 1 | Member 1 | Game Engine | Board representation, move validation, win and draw detection, overline rule, unit tests |
| 2 | Member 2 | Minimax and Candidate Moves | Core Minimax, depth-limited search, candidate-move generation, AI player class, test cases |
| 3 | Member 3 | Alpha-Beta and Optimisation | Alpha-Beta pruning, move ordering, time limits, iterative deepening |
| 4 | Member 4 | Heuristics and Difficulty Levels | Evaluation function, pattern scoring, attack and defense weights, Easy/Medium/Hard levels |
| 5 | Member 5 | UI and Integration | Board display, move input, size and difficulty menus, game-over screen, integrating all modules |
| 6 | Member 6 | Experiments and Documentation | Test plan, benchmarks and charts, UML and flow diagrams, slides, final report assembly |

Every member also wrote the report section for their own module and summarised one research paper for the literature review.

## Development Platform

- **Language:** Python 3.10+
- **GUI:** _Tkinter / Pygame (update to your choice)_
- **Version control:** Git and GitHub
- **Testing:** `unittest`
- **Charts:** Matplotlib
- **Editor/IDE:** _VS Code / PyCharm (update)_




## Academic Integrity

This project is original group work. All sources, libraries, and tools used, including any AI assistance, are acknowledged, and every team member can explain and defend the submitted work.
