# DSA-java-project
Snake Game Using Linked List, Queue, HashMap, A* Pathfinding, GUI, and File Handling
===================================================================

Programming Language:
Java with Swing GUI

How to Run:
1. Open terminal or command prompt.
2. Go to the src folder:
   cd SnakeGameDSA/src
3. Compile all Java files:
   javac *.java
4. Run the project:
   java Main

Files Created Automatically:
- scores.txt
- history.txt

Data Structures Used:

1. Custom Linked List
Class: SnakeLinkedList.java
- Stores snake body manually.
- Every SnakeNode stores x and y coordinates.
- New head is added during movement.
- Tail is removed unless food is eaten.

2. Custom Queue
Class: DirectionQueue.java
- Stores quick direction inputs.
- Processes direction inputs one by one.
- Prevents invalid reverse movement inside GamePanel.

3. HashMap
Class: ScoreManager.java
- Stores player names and high scores.
- Allows searching score by player name.
- Saves and loads high scores from scores.txt.

4. Advanced A* Pathfinding
Class: AStarPathFinder.java
- Treats every board tile as a graph node.
- Finds the shortest safe path from snake head to food.
- Avoids snake body and wall collisions.
- Uses Manhattan distance as the heuristic.
- Uses a custom Min-Heap instead of Java PriorityQueue.
- Press H during the game to toggle the path hint.

5. File Handling
Classes:
- ScoreManager.java
- PlayerHistoryManager.java

Files:
- scores.txt stores player high scores.
- history.txt stores player name, score, date/time, and result.

Controls:
- Arrow Keys: Move snake
- P: Pause / Resume
- R: Restart
- H: Show / hide A* shortest-path hint

Difficulty:
- Easy = slow speed
- Medium = normal speed
- Hard = fast speed

Project Screens:
- Welcome/Main Menu
- Player Name and Difficulty Screen
- Game Screen
- Game Over Screen
- High Scores Screen
- Player History Screen
- Instructions Screen

Important Notes:
- Java built-in LinkedList is not used for snake body.
- Java built-in Queue is not used for direction handling.
- Java built-in HashMap is used for score records.
- The project uses only Java Swing, no game engine.

GUI Upgrade Added:
- Modern gradient background across all screens.
- Rounded glass-style cards for menu, scores, history, instructions, and game over screen.
- Custom modern buttons with hover effects.
- Improved game board with rounded border, subtle checker/grid texture, and shadow.
- Upgraded HUD cards for player name, score, and difficulty.
- Glowing food design.
- Better snake body rendering with gradient segments and snake-head eyes.
- Cleaner pause overlay and bottom control legend.

Updated Files:
- UITheme.java
- MainMenu.java
- GamePanel.java
- GameOverPanel.java
- Main.java

Windows One-Click Run
---------------------
Double-click Run_Snake_Game.bat to compile and launch the game on Windows.
Requirement: Java JDK 8 or newer must be installed and added to PATH.

Latest GUI Update:
- Removed the Advanced DSA Project heading from the main menu.
- Removed the DSA feature cards from the main menu.
- Removed the long DSA feature list under the title.
- Added an animated snake preview on the main menu for a cleaner and more attractive look.

MULTIPLAYER UPDATE
------------------
A Two Player Mode has been added.

Controls:
- Player 1: Arrow Keys
- Player 2: W, A, S, D
- P: Pause / Resume
- R: Restart

Rules:
- Both players try to eat the same food.
- Each snake has a separate score.
- Avoid walls, your own body, and the other player's snake. In multiplayer, the snake that hits the wall first loses, and the snake that hits itself loses.
- If both players crash at the same time, the game ends in a draw.

DSA Note:
- Each player's snake body is still managed using the custom SnakeLinkedList structure.


Multiplayer Winner Rule Update:
- If both snakes collide/crash at the same time, the player with the higher score wins.
- If both players have the same score, the game shows a draw.


Latest Multiplayer Rule Update:
- Multiplayer wall rule updated: if a snake hits the wall first, that player loses. For non-wall collisions, higher score wins; equal score is a draw.
- The player with the higher score wins, even if that player crashes first.
- If both scores are equal, the game result is a draw.


Latest Multiplayer Self-Collision Rule Update:
- If a snake hits its own body, that player loses automatically.
- If both snakes hit themselves in the same move, the higher score wins; equal score is a draw.
- Wall-hit loss still has priority over self-collision.

Latest Key Control Check:
- Player 1 controls verified: Arrow Keys.
- Player 2 controls verified: W, A, S, D.
- Pause: P.
- Restart: R.
- Multiplayer panel now requests keyboard focus automatically when opened, so controls respond correctly after starting/restarting the game.
- Final collision rule: wall hit = that player loses; self hit = that player loses; if both crash together, higher score wins, equal score is a draw.

Latest Head-to-Body Collision Rule Update:
- If Player 1 snake head hits Player 2 snake body, Player 1 loses.
- If Player 2 snake head hits Player 1 snake body, Player 2 loses.
- If both snake heads hit each other directly, the higher score wins.
- If both scores are equal during a head-to-head hit, the game shows a draw.
