---
date: 2023-07-25
layout: post
title: Python Turtle Graphics Tutorial- Building a Fun Snake Game!
subtitle: "Time to recreate the Nokia 3310 Memories!"
description: >-
  Learn to Code and Master the Classic Snake Game with Python and Turtle Graphics
image: >-
  https://res.cloudinary.com/dy3po9tjd/image/upload/v1690267366/951eba151a6b10e438ac2b52ffcf1ad2_whyuyt.gif
# https://res.cloudinary.com/dm7h7e8xj/image/upload/v1559821647/theme6_qeeojf.jpg
optimized_image: >-
  https://res.cloudinary.com/dy3po9tjd/image/upload/v1690267366/951eba151a6b10e438ac2b52ffcf1ad2_whyuyt.gif
# https://res.cloudinary.com/dm7h7e8xj/image/upload/c_scale,w_380/v1559821647/theme6_qeeojf.jpg
category: code
tags:
  - tutorial
  - python
author: sufiyan
paginate: false
---





# Tutorial: Creating a Snake Game with Python's Turtle Graphics

In this tutorial, we will walk through the process of creating a simple Snake Game using Python's Turtle Graphics module. The game will involve controlling a snake to eat food and grow longer while avoiding collisions with the walls and its own tail.

## Prerequisites

To follow along with this tutorial, you will need to have Python installed on your computer. You can download the latest version of Python from the official website (https://www.python.org/downloads/).

We will also be using the built-in Turtle Graphics module in Python, which comes pre-installed with most Python distributions.

## Getting Started

First, let's set up the project structure. Create a new folder for the Snake Game and create the following files inside it:

1. `main.py`: The main game file containing the game loop and initialization.
2. `snake.py`: A module containing the Snake class to handle the snake's behavior.
3. `food.py`: A module containing the Food class to handle the food's behavior.
4. `scoreboard.py`: A module containing the Scoreboard class to manage the game score.

## Step 1: Setting up the Main Game File (`main.py`)

```python
from turtle import Screen
from snake import Snake
from food import Food
from scoreboard import Scoreboard
import time

screen = Screen()
screen.setup(width=600, height=600)
screen.bgcolor("black")
screen.title("My Snake Game")
screen.tracer(0)

snake = Snake()
food = Food()
scoreboard = Scoreboard()

screen.listen()
screen.onkey(snake.up, "Up")
screen.onkey(snake.down, "Down")
screen.onkey(snake.left, "Left")
screen.onkey(snake.right, "Right")

game_is_on = True
while game_is_on:
    screen.update()
    time.sleep(0.1)
    snake.move()

    # Detect collision with food.
    if snake.head.distance(food) < 15:
        food.refresh()
        snake.extend()
        scoreboard.increase_score()

    # Detect collision with wall.
    if snake.head.xcor() > 280 or snake.head.xcor() < -280 or snake.head.ycor() > 280 or snake.head.ycor() < -280:
        scoreboard.reset_game()
        snake.reset_snake()

    # Detect collision with tail.
    for segment in snake.segments:
        if segment == snake.head:
            pass
        elif snake.head.distance(segment) < 10:
            scoreboard.reset_game()
            snake.reset_snake()

screen.exitonclick()
```

In the main game file, we first import the necessary modules (`Screen`, `Snake`, `Food`, `Scoreboard`, and `time`). Then, we set up the game screen with the desired width, height, and background color.

We initialize the game components: the snake, the food, and the scoreboard. We also set up the event listeners for arrow keys to control the snake's movement.

Inside the game loop (`while game_is_on`), we update the screen, move the snake, and check for collisions with food, walls, and the snake's own tail. Depending on the collision, we call appropriate functions to handle these scenarios.

## Step 2: Creating the `Snake` Class (`snake.py`)

```python
from turtle import Turtle

STARTING_POSITIONS = [(0, 0), (-20, 0), (-40, 0)]
MOVE_DISTANCE = 20
UP = 90
DOWN = 270
LEFT = 180
RIGHT = 0

class Snake:
    def __init__(self):
        self.segments = []
        self.create_snake()
        self.head = self.segments[0]

    def create_snake(self):
        for position in STARTING_POSITIONS:
            self.add_segment(position)

    def add_segment(self, position):
        new_segment = Turtle("square")
        new_segment.color("white")
        new_segment.penup()
        new_segment.goto(position)
        self.segments.append(new_segment)

    def reset_snake(self):
        for seg in self.segments:
            seg.goto(1000, 1000)
        self.segments.clear()
        self.create_snake()
        self.head = self.segments[0]

    def extend(self):
        self.add_segment(self.segments[-1].position())

    def move(self):
        for seg_num in range(len(self.segments) - 1, 0, -1):
            new_x = self.segments[seg_num - 1].xcor()
            new_y = self.segments[seg_num - 1].ycor()
            self.segments[seg_num].goto(new_x, new_y)
        self.head.forward(MOVE_DISTANCE)

    def up(self):
        if self.head.heading() != DOWN:
            self.head.setheading(UP)

    def down(self):
        if self.head.heading() != UP:
            self.head.setheading(DOWN)

    def left(self):
        if self.head.heading() != RIGHT:
            self.head.setheading(LEFT)

    def right(self):
        if self.head.heading() != LEFT:
            self.head.setheading(RIGHT)
```

In the `snake.py` file, we define the `Snake` class responsible for managing the snake's behavior. The class contains methods to create the initial snake, add new segments, extend the snake, move the snake, and handle its direction.

## Step 3: Creating the `Food` Class (`food.py`)

```python
from turtle import Turtle
import random

class Food(Turtle):
    def __init__(self):
        super().__init__()
        self.shape("circle")
        self.penup()
        self.shapesize(stretch_len=0.5, stretch_wid=0.5)
        self.color("blue")
        self.speed("fastest")
        self.refresh()

    def refresh(self):
        random_x = random.randint(-280, 280)
        random_y = random.randint(-280, 280)
        self.goto(random_x, random_y)
```

The `Food` class in `food.py` is responsible for managing the food's behavior. When initialized, the food is represented by a blue circle. The `refresh()` method generates a random position for the food on the screen.

## Step 4: Creating the `Scoreboard` Class (`scoreboard.py`)

```python
from turtle import Turtle

ALIGNMENT = "center"
FONT = ("Courier", 24, "normal")
file = open("data.txt", mode="r")
read_high_score = file.read()
file.close()

class Scoreboard(Turtle):
    def __init__(self):
        super().__init__()
        self.score = 0
        self.high_score = int(read_high_score)
        self.color("white")
        self.penup()
        self.goto(0, 270)
        self.hideturtle()
        self.update_scoreboard()

    def update_scoreboard(self):
        self.clear()
        self.write(f"Score: {self.score} Highscore: {self.high_score}", align=ALIGNMENT, font=FONT)

    def reset_game(self):
        if self.score > self.high_score:
            self.high_score = self.score
            with open("data.txt", mode="w") as new_file:
                new_file.write(f"{self.high_score}")

        self.score = 0
        self.update_scoreboard()

    def increase_score(self):
        self.score += 1
        self

.clear()
        self.update_scoreboard()
```

In the `scoreboard.py` file, we define the `Scoreboard` class to manage the game's score. The class handles updating the score, displaying it on the screen, and managing the high score using data stored in a text file (`data.txt`).

## Conclusion

## ![placeholder](https://res.cloudinary.com/dy3po9tjd/image/upload/v1690267366/snake_game_b7ew0c.gif "Completed game gif")


Congratulations! You have successfully created a simple Snake Game using Python's Turtle Graphics module. You now have a basic understanding of how to handle the game loop, control the snake, manage the food, and keep track of the player's score.

Feel free to enhance the game further by adding features such as sound effects, increasing the game's difficulty as the snake grows longer, or implementing additional power-ups.

Now it's time to play your game and have fun! Happy coding! 🎮🐍
