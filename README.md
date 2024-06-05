This project is an interactive Snake game implemented using Python, OpenCV, and Pygame. The game uses computer vision techniques to track the movement of the player’s hand, allowing them to control the snake by moving their hand in front of a webcam. The goal is to eat the apples that randomly appear on the screen while avoiding the boundaries.

Features:
Real-time Hand Tracking: Utilizes OpenCV to detect and track the player's hand movements.
Dynamic Gameplay: The snake grows in length as it consumes apples.
Boundary Detection: The game ends if the snake touches the boundaries.
Sound Effects: Includes sound effects for eating apples, winning the game, and game over events using Pygame.
Score Display: Real-time display of the player's score.
Random Apple Placement: Apples appear at random positions within the game boundaries.
How to Play:
Run the Game: Launch the game by running the main Python script.
Control the Snake: Use your hand to control the snake's movement in front of the webcam.
Eat Apples: Guide the snake to the apples to increase your score.
Avoid Boundaries: Ensure the snake does not touch the boundaries to keep playing.
Requirements:
Python 3.x
OpenCV
Pygame
Numpy
Imutils
