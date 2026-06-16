
# Sudoku CSP Solver

An AI-powered Sudoku project built for **AI2002 — Artificial Intelligence** at **FAST-NUCES Chiniot-Faisalabad**.

Includes:
- A **Python CLI solver** using Constraint Satisfaction Problem (CSP) techniques
- A **playable web UI** where you can solve puzzles yourself — or let the AI solve them for you

---

## Gameplay

https://github.com/user-attachments/assets/f3d50f09-a848-4e67-906e-60493aad039f

---

## Play It Online

Open [sudoku_index.html](https://github.com/user-attachments/files/28994250/sudoku_index.html) in any browser — no installation needed.

**Features:**
- 4 difficulty levels: Easy, Medium, Hard, Very Hard
- Type numbers into cells yourself to play
- **Check** your answers for mistakes
- **Hint** button to reveal one cell
- **AI Solve** — watch the CSP solver fill the board with animation
- Live timer
- Arrow key + keyboard number input support

---

## AI Techniques Used

| Technique | Description |
|-----------|-------------|
| **AC-3** | Arc Consistency Algorithm 3 — reduces domains before search |
| **Backtracking Search** | Recursive search with pruning |
| **MRV Heuristic** | Minimum Remaining Values — picks the most constrained cell first |
| **LCV Heuristic** | Least Constraining Value — orders values to minimize conflicts |
| **Forward Checking** | Eliminates invalid values from peers after each assignment |

---

## Python CLI Solver

### Requirements
```bash
pip install -r requirements.txt
```
*(Only standard library used — no packages needed)*

### Run
```bash
python sudoku_csp.py
```

Solves all 4 puzzles (easy → very hard) and prints stats:
```
Backtrack calls   : 1
Backtrack failures: 0
Comment: Very few backtracks — AC-3 + MRV solved it almost directly.
```

### Puzzle Format
Each `.txt` file is a 9×9 grid where `0` = empty cell:
```
004030050
609400000
005100489
...
```

---

## License

MIT License
