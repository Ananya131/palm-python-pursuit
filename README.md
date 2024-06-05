import cv2
import imutils
from collections import deque
import numpy as np
import pygame
import time
# Initialize game parameters
score = 0
max_score = 10
list_capacity = 0
max_lc = 20
l = []
flag = 0
apple_x = None
apple_y = None
center = None
prev_c = None #initialize prev_c
# Initialize pygame mixer for sound
pygame.mixer.init()

# Load sound files
eat_sound = pygame.mixer.Sound("C:/Users/LENOVO THINKPAD/Downloads/mixkit-achievement-completed-2068.wav")
win_sound = pygame.mixer.Sound("C:/Users/LENOVO THINKPAD/Downloads/mixkit-animated-small-group-applause-523.wav")
game_over_sound = pygame.mixer.Sound("C:/Users/LENOVO THINKPAD/Downloads/mixkit-sad-game-over-trombone-471.wav")

# distance function
def dist(pt1, pt2):
    return np.sqrt((pt1[0] - pt2[0]) ** 2 + (pt1[1] - pt2[1]) ** 2)


cap = cv2.VideoCapture(0)

# Snake game in Python
while 1:

    ret, frame = cap.read()
    frame = cv2.flip(frame, 1)
    cv2.line(frame, (50, 50), (50, 450), (255, 255, 255), 5)
    cv2.line(frame, (50, 450), (550, 450), (255, 255, 255), 5)
    cv2.line(frame, (550, 450), (550, 50), (255, 255, 255), 5)
    cv2.line(frame, (550, 50), (50, 50), (255, 255, 255), 5)

    img = imutils.resize(frame.copy(), width = 600)
    img = cv2.GaussianBlur(img, (11, 11), 0)
    img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

    if apple_x is None or apple_y is None:
        # assigning random coefficients for apple coordinates
        apple_x = np.random.randint(60, frame.shape[0] - 60)
        apple_y = np.random.randint(60, 450)

    cv2.circle(frame, (apple_x, apple_y), 10, (0, 0, 255), -1)
    # Check if the snake's head touches the boundary
    if center:
        if center[0] <= 50 or center[0] >= 550 or center[1] <= 50 or center[1] >= 450:
            flag = -1  # Indicates game over
            game_over_sound.play()  # Play game over sound


    # change this range acc to your need
    greenLower = (29, 86, 18)
    greenUpper = (93, 255, 255)

    # masking out the green color
    mask = cv2.inRange(img, greenLower, greenUpper)
    mask = cv2.erode(mask, None, iterations=2)
    mask = cv2.dilate(mask, None, iterations=2)

    # find contours
    cnts = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    cnts = imutils.grab_contours(cnts)

    if len(cnts) > 0:
        ball_cont = max(cnts, key=cv2.contourArea)
        (x, y), radius = cv2.minEnclosingCircle(ball_cont)  # find the minimum enclosing circle about the found contour


        M = cv2.moments(ball_cont)
        center = (int(M['m10'] / M['m00']), int(M['m01'] / M['m00']))

        if radius > 10:
            cv2.circle(frame, center, 10, (0, 0, 255), 3)

            if len(l) > list_capacity:
                l = l[1:]

            if prev_c and (dist(prev_c, center) > 3.5):
                l.append(center)

            apple = (apple_x, apple_y)
            if dist(apple, center) < 20:
                score += 1
                eat_sound.play()  # Play eating sound
                if score == max_score:
                    flag = 1
                    win_sound.play()  # Play winning sound
                list_capacity += 1
                apple_x = None
                apple_y = None

    for i in range(1, len(l)):
        if l[i - 1] is None or l[i] is None:
            continue
        r, g, b = np.random.randint(0, 255, 3)

        cv2.line(frame, l[i], l[i - 1], (int(r), int(g), int(b)), thickness=int(len(l) / max_lc + 2) + 2)

    cv2.putText(frame, 'Score :' + str(score), (450, 100), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 0, 203), 2)
    if flag == 1:
        cv2.putText(frame, 'YOU WIN !!', (100, 250), cv2.FONT_HERSHEY_SIMPLEX, 3, (255, 255, 0), 3)
        break #Exit the loop when the player wins
    if flag == -1:
        cv2.putText(frame, 'GAME OVER !!', (100, 250), cv2.FONT_HERSHEY_SIMPLEX, 3, (0, 0, 255), 3)
        break  # Exit the loop when the game is over

    cv2.imshow('live feed', frame)
    cv2.imshow('mask', mask)
    prev_c = center

    if cv2.waitKey(1) == 27:
        break

# Display final score and exit message
while True:
    frame = np.zeros((500, 600, 3), dtype='uint8')
    if flag == 1:
        message = 'YOU WIN !!'
        color = (255, 255, 0)
    else:
        message = 'GAME OVER !!'
        color = (0, 0, 255)

    cv2.putText(frame, message, (100, 200), cv2.FONT_HERSHEY_SIMPLEX, 3, color, 3)
    cv2.putText(frame, 'Final Score: ' + str(score), (150, 300), cv2.FONT_HERSHEY_SIMPLEX, 1.5, (255, 255, 255), 2)
    cv2.putText(frame, 'Press any key to exit', (150, 400), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 255), 2)

    cv2.imshow('Final Score', frame)

    if cv2.waitKey(1) != -1:
        break

cv2.destroyAllWindows()
cap.release()
