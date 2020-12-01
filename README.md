# G_assignment_01
# I am going to create a snake game using pygame
# My previous plan was to make music, but I decided that it was a little difficult to make at my level, so I made a game.

# Load the library through import pygame & sys to shut down the window to terminate the import
# Since it is a game, also use the time element, and random to make the game dynamically.
import pygame
import sys
import time
import random

# Announce the start.
from pygame.locals import *

# size of the 'window'
WINDOW_WIDTH = 800
WINDOW_HEIGHT = 600
# size of the 'grid'
GRID_SIZE = 20
GRID_WIDTH = WINDOW_WIDTH /GRID_SIZE
GRID_HEIGHT = WINDOW_HEIGHT /GRID_SIZE

# Specified color names and actual color values (R,G,B)
WHITE = (255, 255, 255)
GREEN = (0, 50, 0)
ORANGE = (250, 150, 0)

# Specifies the direction value when viewed in the coordinates of x and y
UP = (0, -1)
DOWN = (0, 1)
LEFT = (-1, 0)
RIGHT = (1, 0)

FPS = 10

# Select a snake (Python) as the object of the game,'create' it, and set a color
class Python(object):
    def __init__(self):
        self.create()
        self.color = GREEN

# It sets the length, position, and direction of 'self' defined above
    def create(self):
        self.length = 2
# To place the position in the center of the screen, the value is defined like this
        self.positions = [((WINDOW_WIDTH /2), (WINDOW_HEIGHT / 2))]
# Direction is determined by a random value
        self.direction = random.choice([UP, DOWN, LEFT, RIGHT])

# In the snake's motion, it has to go up but not down, that is, the movement should not be manipulated in the opposite direction
    def control(self, xy):
        if (xy[0] * -1, xy[1] * -1) == self.direction:
            return
# In other situations (else), the coordinate value can be moved as it is    
        else:
            self.direction = xy

# 'cur' represents the current position, the 0th of 'positions' represents the snake's head
    def move(self):
        cur = self.positions[0]
        x, y = self.direction
# draw snake's head 
# In order to prevent the snake from disappearing when it passes over the grid window (to make it come out again from the opposite side), it is divided by the window size in the moving unit
        new = (((cur[0] + (x * GRID_SIZE)) % WINDOW_WIDTH), (cur[1] + (y * GRID_SIZE)) % WINDOW_HEIGHT)
# Snakes shouldn't contain moving parts in their position coordinates, so when the snake eats itself, it's reborn
        if new in self.positions[2:]:
            self.create()
# If the number of 'positions' is calculated as 'length', and the value is larger than the actual length, the last one in 'positions' is'pop'ed
        else:
            self.positions.insert(0, new)
            if len(self.positions) > self.length:
                self.positions.pop()
# If a snake ate its prey, the length of the snake feeds
    def eat(self):
        self.length += 1
# Draw the snake to be on the screen
    def draw(self, surface):
        for p in self.positions:
            draw_object(surface, self.color, p)

# Define feed. But here it is not'positions', but'position'
class Feed(object):
    def __init__(self):
        self.position = (0, 0)
        self.color = ORANGE
        self.create()
# Use 'random' because the feed needs to keep changing on the game screen
    def create(self):
        self.position = (random.randint(0, GRID_WIDTH -1) * GRID_SIZE, random.randint(0, GRID_HEIGHT -1) * GRID_SIZE)

    def draw(self, surface):
        draw_object(surface, self.color, self.position)

# The grid is in a square shape, add the grid size using the coordinate values of x and y, 0 and 1 in 'Rect'
def draw_object(surface, color, pos):
    r = pygame.Rect((pos[0], pos[1]), (GRID_SIZE, GRID_SIZE))
    pygame.draw.rect(surface, color, r)

# This is made for the case of eating feed
def check_eat(python, feed):
    if python.positions[0] == feed.position:
        python.eat()
        feed.create()  
            
if __name__ == '__main__':
    python = Python()
    feed = Feed()
    
# Write a command that initializes pygame to use the library

    pygame.init()
# I need a window to run pygame. Name it as 'window'
# In 'set mode','mode' defines the total size of 'window' because it requires the size of the actual game
    window = pygame.display.set_mode((WINDOW_WIDTH, WINDOW_HEIGHT), 0, 32)
# name the game as 'Python Game'
    pygame.display.set_caption('Python Game')
# Put the size of 'window' defined above into the 'surface' & convert
    surface = pygame.Surface(window.get_size())
    surface = surface.convert()
# color the surface (background) as white color and I name the color as 'WHITE'
    surface.fill(WHITE)
# Because it is a game, it uses time. Then set the degree of repetition of that key as 1 to 40
    clock = pygame.time.Clock()
    pygame.key.set_repeat(1, 40)
# Screen is composed through window bit operation
    window.blit(surface, (0, 0))


    while True:
# Write an event operation statement
        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                sys.exit()
# When the keyboard is pressed
            elif event.type == KEYDOWN:
                if event.key == K_UP:
                    python.control(UP)
                elif event.key == K_DOWN:
                    python.control(DOWN)
                elif event.key == K_LEFT:
                    python.control(LEFT)
                elif event.key == K_RIGHT:
                    python.control(RIGHT)
# Write the generated/created classes        
        surface.fill(WHITE)
        python.move()
        check_eat(python, feed)
# When the snake eats its prey, the speed is set to increase
        speed = (FPS + python.length) / 2
        python.draw(surface)
        feed.draw(surface)
        window.blit(surface, (0, 0))
# 'Flip' & 'update' to 'display' of pygame
        pygame.display.flip()
        pygame.display.update()
# Since the computer moves in 'tick' units, the speed is reflected in the 'tick' of the actual 'clock'
        clock.tick(speed)
        