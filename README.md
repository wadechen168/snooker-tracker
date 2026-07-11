Snooker Tracker
A single-file HTML snooker score tracker with real-time needs and alt needs calculation — telling you exactly how many points a player needs at any moment to put their opponent in a "snooker needed" position.
Built by a snooker fan with zero coding knowledge, in collaboration with Claude (Anthropic) and DeepSeek AI over 6 months of iteration, edge case hunting, and logic refinement.

The Core Problem
Standard snooker scorers just add and subtract points. This app solves a harder question:

At any given moment, how many points does Player A need to put Player B in "snooker needed" position?

This is not a simple calculation. It requires knowing both the current scores AND the number of reds remaining simultaneously — you cannot derive one from the other.

The 2-Cycle Algorithm
The needs calculation works by simulating the game forward:

Outer loop: test potting 1 to N reds (N = reds remaining)
Inner loop: test color value 0 (miss) to 7 (black) after each red
Test: Player A score + accumulated points >= Player B score + Player B max remaining
Player B's maximum shrinks as the outer loop advances — each red potted by A removes one red cycle from B's ceiling
tie = win by design — the first state that satisfies the test is the answer

Example — Score 42:30, 7 reds remaining
Test red 1 + miss:  42+1 >= 30+75?  NO
Test red 1 + black: 42+8 >= 30+75?  NO
...
Test red 4 + black: 42+32 >= 30+51? NO
Test red 5 + red:   42+33 >= 30+43? YES — needs = 33
Player A needs 33 points. The 2-cycle found it by tracking score and reds together.

Why tie = win?
A tie is one exact mathematical answer. A win could be thousands of different scoring paths. Anchoring to tie gives the algorithm a clean, unambiguous target and dramatically reduces edge cases and bugs.

The Alt Needs Calculation
The 2-cycle finds the needs assuming Player A pots a color after each red. But does Player A really need that color? Sometimes the red alone is enough.
Alt needs uses no simulation — pure math:

The 2-cycle tells us Player A uses N reds, so Player B gets the remaining reds
Player B's max = their current score + (remaining reds × 8) + 27
Alt = Player B's max − Player A's current score

Example — Score 42:30, 7 reds remaining

2-cycle finds Player A needs 5 reds, leaving 2 for Player B
Player B's max = 30 + (2×8) + 27 = 73
Alt = 73 − 42 = 31t

Player A only needs 31 points — cheaper than the 33 the 2-cycle found, because the 10th red itself crosses the line without needing a color after it.
The alt is always equal to or lower than the needs figure, and is displayed alongside it when it differs.

Color Stage
The same logic applies when all reds are potted. Player B's maximum becomes their current score plus the remaining colors (e.g. from brown onwards = 4+5+6+7). The 2-cycle and alt calculations adapt automatically.

UI and Controls

Player photo: tap the box at the top to open your photo album — the filename becomes the player name
Score and reds boxes: dual input/output — editable mid-frame if needed
Single tap ball = pot
Long press ball = miss
Super long press red = remove a red from the table (for off-table situations)
Multiple reds can be potted in one shot (legal in snooker)
Long press undo = redo
Long press swap = trailing player concedes the frame
Long press color (reds = 0) = remove that color and all colors below it from the table


Built With

Logic design: Wade — snooker domain knowledge, 2-cycle concept, alt needs formula, tie=win design decision, 6 months of edge case and bug reporting
HTML, CSS, touch event handling, button logic: Claude (Anthropic)
Core logic translation into working code: DeepSeek AI
Tested on: Android tablet, Edge browser


Why This Is Different
Every other snooker scorer on GitHub adds and subtracts points. None implement a needs or alt needs calculation. The 2-cycle simulation and the alt math are original work derived from first principles of snooker scoring logic.
