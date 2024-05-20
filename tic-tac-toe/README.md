Tic-Tac-Toe is a game that is played on a 3x3 grid. Players take turns placing their mark, either an "X" or an "O", on an empty cell. The player who places three of their marks in a horizontal, vertical, or diagonal row wins the game. If the grid is full and no player has won, the game is a draw.

Children age 6 and up can generally play tic-tac-toe at some level of competence, while in early teens they play optimally and it is considered a solved game.

Minimax, Q-learning, and Reinforcement learning can be used to create an AI that plays tic-tac-toe.  The AI can be trained to play optimally or to play at a specific level of competence.

LLMs have advanced significantly in performance in many tasks, but they still struggle with tasks that require reasoning.  Tic-tac-toe is a simple game that can be used to test the capabilities of LLMs in a game-playing scenario.

In this project, we explore how LLMs respond to tic-tac-toe with a variety of prompt engineering techniques and varying amount of given information.

LLM Sub-optimal play
* Even GPT-4 Turbo and Claude-3 Opus cannot play tic-tac-toe optimally and most of the time make glaring mistakes.
* Strong prompting is required for those models to even avoid making invalid moves, but such prompting still leads to invalid moves for Haiku etc.

Board representation may affect how LLM handles the game:
* 2D grid with spaces between X or O and | to avoid mixing tokens, empty spaces given by numbers to choose from
```mathematica
X | O | 3
---------
O | O | X
---------
X | X | 9
---------
```
with or without spaces
Or with no extra characters:
```mathematica
X O 3
O O X
X X 9
```
Or with textual labels:
```mathematica
1 | 2 | HUMAN
---------------
AI | HUMAN | 6
---------------
7 | AI | 9
```
Or chess-like 2D board:
```mathematica
  a   b   c
1 O   -   O
2 O   X   -
3 X   -   X
```
* Chess-like sequence generation of moves (e.g. `X2b, O1a, X3c, O2a, X3a, O1c, X3b, O2c`)
* List of board positions with X, O, and empty spaces
* Map of board positions to X, O, and numbers
* Verbalized map, saying for each line if filled or unfilled
* GPT-4 Vision, providing visual board and asking it to play next move (either as X or O) e.g. from https://papergames.io/en/r/TH1GFs3sH.  Also separately, telling it to play optimally and it will win $200 dollars if it beats me.  Still fails miserably.

Prompt engineering likely affects game play:
* Rules or not (no effect)
* Valid moves or not (helps to avoid invalid moves, but still weak LLMs may make them)
* Reminders or not (helps to avoid invalid moves for smart models)

Agents via iteration or chaining may improve performance:
* 0-Shot or 1+ shot
* Reflection/Self-critique (still makes mistakes)
* BFS (still makes mistakes)

Odd things observed:
* GPT4 Turbo 1106 via API going into a loop of self-correction even in 0-shot, doing about 10 attempts to fix, all failing (system prompt is just: "You are an expert tic-tac-toe player."):
```json
[
      [
        "I will start and play X and play 5.  You play O.  Remember to think step-by-step.",
        "Thinking step-by-step, I will consider the best move to counter your X in position 5. To prevent you from creating a line, I should place my O in one of the corners. I'll choose position 1.\n\nHere's the updated board:\n\n```board\nO | 2 | 3\n4 | X | 6\n7 | 8 | 9\n```\n\nYour turn."
      ],
      [
        "6",
        "Thinking step-by-step, I see that you've placed an X in position 6. To block any potential winning moves and to create an opportunity for myself, I should place an O in one of the strategic positions. Position 3 is a good choice to block your potential line from 5 to 6 and to create a chance for me to win on the next moves.\n\nHere's the updated board:\n\n```board\nO | 2 | O\n4 | X | X\n7 | 8 | 9\n```\n\nYour turn."
      ],
      [
        "4",
        "Thinking step-by-step, you've placed an X in position 4. To block your potential winning line from 4 to 6, I need to place an O in position 2 or 8. Position 2 is the better choice because it also gives me a potential winning line across the top row.\n\nHere's the updated board:\n\n```board\nO | O | O\nX | X | X\n7 | 8 | 9\n```\n\nHowever, it seems there has been a mistake. Both of us cannot have a winning line simultaneously. Let's correct this. Since it's my turn, and you've just placed an X in position 4, I will place an O in position 2 to block your line and to create a potential winning line for myself.\n\nHere's the corrected board:\n\n```board\nO | O | O\nX | X | X\n7 | 8 | 9\n```\n\nOnce again, there seems to be a mistake as we have two winning lines. Let's correct this one more time. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nIt seems I've made the same mistake again. Let's try this one more time. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI apologize for the confusion. It appears I have made the same mistake repeatedly. Let's correct the board properly this time. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI apologize once again for the repeated error. It seems I am stuck in a loop making the same mistake. Let's try to correct the board one more time. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI apologize for the continued errors. It seems I am unable to provide the correct board. Let's try this again. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI am sorry for the repeated mistakes. It seems I am unable to break out of this loop. Let's try to correct the board one last time. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI apologize for the continued errors. It seems I am unable to provide the correct board. Let's try this again. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI am sorry for the repeated mistakes. It seems I am unable to break out of this loop. Let's try to correct the board one last time. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI apologize for the continued errors. It seems I am unable to provide the correct board. Let's try this again. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI am sorry for the repeated mistakes. It seems I am unable to break out of this loop. Let's try to correct the board one last time. After you placed an X in position 4, I will place an O in position 2 to block your line.\n\nHere's the corrected board:\n\n```board\nO | O | 3\nX | X | X\n7 | 8 | 9\n```\n\nI apologize for the continued errors. It"
      ]
]
```
Papers:
* Benchmarking Large Language Model (LLM) Performance for Game Playing via Tic-Tac-Toe https://www.mdpi.com/2079-9292/13/8/1532 (https://www.mdpi.com/2079-9292/13/8/1532/pdf?version=1713429285)
* Can Large Language Models Serve as Rational Players in Game Theory? A Systematic Analysis https://arxiv.org/abs/2312.05488
* Large Language Models as Gaming Agents: https://openreview.net/forum?id=iS3fQooCaa
* A Survey on Large Language Model-Based Game Agents: https://arxiv.org/abs/2404.02039 https://github.com/git-disl/awesome-LLM-game-agent-papers
* MindAgent: https://arxiv.org/abs/2309.09971 https://mindagent.github.io/ https://github.com/mindagent/mindagent

Code:
* Benchmarking Large Language Model (LLM) Performance for Game Playing via Tic-Tac-Toe https://github.com/research-outcome/LLM-TicTacToe-Benchmark/
* OpenAI Code: https://medium.com/@jelle.vankerkvoorde/enhancing-python-games-with-large-language-model-agents-a-noughts-and-crosses-case-study-31e6654b9f45
* LMS: https://stackoverflow.com/questions/8016641/tic-tac-toe-using-machine-learning-with-lms-rule (https://github.com/DynastyElvis/Tic-Tac-Toe-LMS-Algorithm)
* https://github.com/sebastiengilbert73/tutorial_learnbyplay
* https://github.com/jordddan/GameEval?tab=readme-ov-file
* https://github.com/Microsoft/SmartPlay

Data:
* https://www.kaggle.com/datasets/newtonbaba12345/the-battle-between-llms-dataset

Discussions:
* https://manifold.markets/notune/will-any-large-language-model-be-ab
* https://twitter.com/polynoamial/status/1755717284650176591
* https://news.ycombinator.com/item?id=38129203
* https://news.ycombinator.com/item?id=35516391
* https://towardsdatascience.com/training-an-agent-to-master-tic-tac-toe-through-self-play-72038c3f33f7
* https://medium.com/@paul.k.pallaghy/i-taught-gpt-3-to-play-tic-tac-toe-almost-354de1c52cd8
* https://www.pudn.com/Download/item/id/1711782574668971.html
* https://stackoverflow.com/questions/53437620/recursively-generate-every-tic-tac-toe-game-in-python
* https://1a3orn.com/sub/essays-tic-tac-toe.html
* https://www.reddit.com/r/ArtificialInteligence/comments/1cjqb4f/llms_cant_play_tictactoe_why_explained/
* https://chatgpt.com/share/266e3ba6-e550-4905-aeb8-d18a90bed38e?oai-dm=1
* https://www.skool.com/data-alchemy/playing-tic-tac-toe-with-your-llm
* https://community.deeplearning.ai/t/scrabble-and-tic-tac-toe/595195
