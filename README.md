
#  Quantum Rock, Paper, Scissors

**Qiskit Fall Fest Nairobi 2026 · Challenge 07** · by Ian Kamau

A Rock, Paper, Scissors game where the computer's choice comes from measuring qubits instead of a classical random number generator. Built with Qiskit 2.5 on qBraid.

👉 **[Open the notebook](quantum_rps.ipynb)**: all the code, results and explanations

🎮 **[Play the game](https://ian-qhamau.github.io/qff26-quantum-rock-paper-scissors/)**: beat Fez, a quantum robot whose moves are my real ibm_fez measurements

🎬 **video:**


https://github.com/user-attachments/assets/ab310997-2902-44aa-b7d1-8d4a92c87d8e



## The problem
Two qubits give 4 results (`00`, `01`, `10`, `11`), but the game only has 3 choices: Rock = `00`, Paper = `01`, Scissors = `10`. What do you do with `11`?

## The approaches I compared (10,000 shots each)

| Approach | Rock | Paper | Scissors | Wasted runs | Verdict |
|---|---|---|---|---|---|
| 1. Treat `11` as Rock | 48.9% | 25.2% | 25.9% | 0 | ❌ Unfair: always play Paper to win |
| 2. Throw away `11` and measure again | 33.1% | 33.3% | 33.6% | ≈ 25% | ✅ Fair, but wasteful |
| 3. `Ry` + controlled-H circuit | 33.1% | 33.9% | 33.0% | 0 | ✅ Fair by design |
| 3 on a noisy simulated IBM Torino | 33.4% | 32.3% | 33.5% | 0.7% (`11`) | ✅ Noise brings `11` back, so the game keeps a safety net |
| 3 on real hardware (ibm_fez) | 34.3% | 32.9% | 32.2% | 0.6% (`11`) | ✅ Real 156-qubit IBM quantum computer: matches the simulator |

![How fair is each approach?](comparison.png)

*The chart shows the first four rows; the real-hardware run is in section 7b of the notebook.*

## How approach 3 works
1. **`Ry(θ)` on qubit 0**, with θ = 2·arccos(√(2/3)) ≈ 70.5°: qubit 0 becomes `0` with probability 2/3 and `1` with probability 1/3.
2. **Controlled-H on qubit 1, only when qubit 0 is `0`.**

So `01` (Paper) gets 1/3, and the other 2/3 splits evenly into `00` (Rock) and `10` (Scissors). In an ideal circuit, `11` is impossible.

## Answers to the deeper questions
1. **How can you make the three choices equally likely?** Throw away `11` and measure again (Approach 2), or build a state with exactly three equal outcomes using `Ry` + controlled-H (Approach 3).
2. **Why is generating three equally probable states more difficult than two?** An H gate splits a qubit 50/50, so n qubits with H gates give 2ⁿ equally likely outcomes: 2, 4, 8, 16… Two outcomes are easy (one qubit and one H gate make a fair coin), but 3 is not a power of 2, so no number of H gates gives exactly three. Two qubits give four, leaving `11` left over. To get three, you either throw results away (wasteful) or design an uneven split: `Ry` gives 2/3 vs 1/3, and a controlled-H divides the 2/3 in half. That takes more gates, and on real hardware more gates means more noise.
3. **What happens if you simply map `11` to one of the choices?** That choice gets two of the four results, so it comes up about 50% of the time (Rock 48.9% in my test).
4. **Could this introduce bias?** Yes. The game becomes predictable: always playing Paper wins about half the games.

## The game
- One fresh quantum measurement (`shots=1`) per round
- Multiple rounds with a live score, win rate, choice statistics and round-by-round history
- Safety net: if noise ever produces `11`, the computer measures again

## Files in this repo
| File | What it is |
|---|---|
| `quantum_rps.ipynb` | The main notebook: all code, results and explanations |
| `ibm_setup.ipynb` | One-time setup to connect to IBM Quantum for the real-hardware run |
| `comparison.png` | Chart comparing the approaches |
| `video demo.mp4` | Short video walkthrough of the project |
| `docs/index.html` | The playable game (hosted with GitHub Pages) |
| `docs/quantum_results.json` | The real measurement results the game replays |
| `export_game_data.ipynb` | Exports those results from my ibm_fez job and Qiskit |

## Run it yourself
Open `quantum_rps.ipynb` in qBraid Lab (Qiskit environment), or any Jupyter setup with `qiskit`, `qiskit-aer`, `qiskit-ibm-runtime` and `matplotlib`. Run the cells from top to bottom; the game cells ask for your move in a text box. The real-hardware cells (section 7b) also need a free IBM Quantum account: run `ibm_setup.ipynb` once first. It asks for your API key in a hidden box and saves it privately, so the key never appears in either notebook.

## What I learned
- Small samples look uneven. Judging fairness takes thousands of shots.
- I ran it on **ibm_fez**, a real 156-qubit IBM quantum computer: `11` appeared 0.6% of the time, close to the simulator's 0.7%.
- Designing the quantum state directly, instead of fixing results afterwards, gave a fair game with nothing wasted.
- **What surprised me most:** I didn't expect a computer to give a result that should be impossible. Noise made `11` appear even though my circuit can't produce it.

## AI disclosure
I used Claude (Anthropic) as a tutor and coding partner: to learn the concepts, get code suggestions with explanations, and word the write-up. I ran, tested and analysed everything myself in qBraid, and all results come from my own runs. I also used Claude Code with the brag plugin to make the promo video, and Claude to help design and build the game page. Full details are in the notebook's last section.
