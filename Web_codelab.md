summary: USF GDSC Spring 2024 Python Snake Workshop
id: docs

# USF GDSC Spring 2024 Flutter Workshop

## Introduction

We're gonna make Snake game in Python!

<img src="docs\img\intro.gif" width="270" height="600"> 
(Change image, add one for finished game)

## Setup screen

add screen attribute and complete setup in game controller class


main.py
```python
class GameController:
    def __init__(self) -> None:

        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0) #remove glitch when snake move

        self.screen.exitonclick()  #Have to be b4 exit on click
        self.screen.mainloop() # keep the screen even after running 
```

## Make Snake body

snake.py:
    - add visual setup and position attribute to Segment class init method
    - add head attribute to Snake class init method
    - add segments attribute to snake class init method (1 head +  2 segments)
    - add previous tail coordinate attribute to snake class init method 

```python
from turtle import Turtle 

# a unit of the snake
class Segment:
    def __init__(self, initial_position) -> None:
        self.position = initial_position
        self.turtle = Turtle(shape='square')
        self.turtle.color('white')
        self.turtle.penup()
        self.turtle.goto(initial_position)

class Head(Segment):
    def __init__(self, initial_position) -> None:
        super().__init__(initial_position)

class Snake:
    def __init__(self) -> None:
        self.head = Head((0,0))
        self.segments = [self.head, Segment((-20, 0)), Segment((-40, 0))]
        self.previous_tail_coord = (-40, 0)
```

main.py:
    - create instance of snake inside Gamecontroller class init methood

```python
class GameController:
    def __init__(self) -> None:

        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0) #remove glitch when snake move
        
        self.snake = Snake()

```

## Moving Snake forward

snake.py:
    - Complete get_position and change_position Segment methods
    - Complete move forward Head method
    - Complete move forward Snake method


```python
class Segment:
  def __init__(self, initial_position) -> None:
        self.position = initial_position
        self.turtle = Turtle(shape='square')
        self.turtle.color('white')
        self.turtle.penup()
        self.turtle.goto(initial_position)

  # return position 
    def get_position(self):
        return self.position

    # move segment to coordinate
    def change_position(self, new_position):
        self.turtle.goto(new_position)
        self.position = new_position

class Head(Segment):
  def __init__(self, initial_position) -> None:
        super().__init__(initial_position)

  def move_forward(self):
        self.turtle.forward(20)
        self.position = self.turtle.pos()
    
class Snake:
  def __init__(self) -> None:
        self.head = Head((0,0))
        self.segments = [self.head, Segment((-20, 0)), Segment((-40, 0))]
        self.previous_tail_coord = (-40, 0)

   def move_forward(self): 
        self.previous_tail_coord = self.segments[-1].get_position()
        for i in range(len(self.segments) - 1, 0, -1): 
            self.segments[i].change_position(self.segments[i - 1].get_position()) 
        self.head.move_forward()
```

main.py:
    - Add attribute to hold game state
    - Add to game loop method that moves snake forward while game not Over
    - Add tracer(0) to screen and include sleep, update in game loop method

```python
class GameController:
    def __init__(self) -> None:
        self.game_ended = False #Game state
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0) #remove glitch when snake move
        self.snake = Snake()

        self.game_loop()
        self.screen.exitonclick()  #Have to be b4 exit on click
        self.screen.mainloop()
      
     def game_loop(self):
        while not self.game_ended:
            self.snake.move_forward()
            sleep(0.1)

```

## Directing the Snake

snake.py:
    - Add heading attribute to segment class
    - Complete turn left, right, up down methods in Head
    - Complete turn left and turn right methods in Snake


```python
class Segment:
    def __init__(self, initial_position) -> None:
        self.position = initial_position
        self.turtle = Turtle()
        self.turtle.shape("square")
        self.turtle.penup()
        self.turtle.color("white")
        self.turtle.goto(initial_position)
        self.direction = 'right' #default Snake's heading

class Head(Segment):
    def __init__(self, initial_position) -> None:
        super().__init__(initial_position)
        
    def move_forward(self):
        self.turtle.forward(20)
        self.position = self.turtle.pos()
        
    def turn_left(self):
        if self.direction != 'right':
            self.turtle.setheading(180)
            self.direction = 'left'
    
    def turn_right(self):
        if self.direction != 'left':
            self.turtle.setheading(0)
            self.direction = 'right'
        
    def turn_up(self):
        if self.direction != 'down':
            self.turtle.setheading(90)
            self.direction = 'up'
        
    def turn_down(self):
        if self.direction != 'up':
            self.turtle.setheading(270)
            self.direction = 'down'

class Snake:
    def __init__(self) -> None:
        self.head = Head((0, 0))
        self.segments = [self.head, Segment((-20, 0)), Segment((-40, 0))]
        self.previous_tail_coord = (-40, 0)
    
    def move_foward(self):    
        self.previous_tail_coord = self.segments[-1].get_position()
        for i in range (len(self.segments) - 1, 0, -1):
            self.segments[i].change_position(self.segments[i - 1].get_position())
        self.head.move_forward()
        
    def turn_left(self):
        self.head.turn_left()
        
    def turn_right(self):
        self.head.turn_right()
        
    def turn_up(self):
        self.head.turn_up()
        
    def turn_down(self):
        self.head.turn_down()
```

main.py:
    - Add screen listener to screen attribute in Game Controller
    - add onkeypress that turns snake left on 'Left', 'Right', 'Up', 'Down' key press


```python
class GameController:
    def __init__(self) -> None:
        self.game_ended = False
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0)
        self.snake = Snake()
        
        #add listeners
        self.screen.listen()
        self.screen.onkeypress(self.snake.turn_left, "Left")
        self.screen.onkeypress(self.snake.turn_right, "Right")
        self.screen.onkeypress(self.snake.turn_up, "Up")
        self.screen.onkeypress(self.snake.turn_down, "Down")

        self.screen.exitonclick()  #Have to be b4 exit on click
        self.screen.mainloop()

```

## Create Food

food.py:
    - Add visual attributes in Food init method 
    - Complete move around method


```python
from turtle import Turtle
from random import randint

class Food (Turtle):
    def __init__(self, shape: str = "circle", undobuffersize: int = 1000, visible: bool = True) -> None:
        super().__init__(shape, undobuffersize, visible)
        self.color('gold')
        self.shapesize(0.5, 0.5)
        self.penup()
        self.speed('fastest')
        
    def move_around(self):
        self.goto(randint(-280, 280), randint(-280, 280))
```

main.py:
    - Create an instance of food class inside the Gamecontroller class init method

```python
class GameController:
    def __init__(self) -> None:
        self.game_ended = False
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0)
        self.snake = Snake()

        self.food = Food() # Create an instance of Food
  
        self.screen.listen()
        self.screen.onkeypress(self.snake.turn_left, "Left")
        self.screen.onkeypress(self.snake.turn_right, "Right")
        self.screen.onkeypress(self.snake.turn_up, "Up")
        self.screen.onkeypress(self.snake.turn_down, "Down")
        self.game_loop()

        self.screen.exitonclick()  
        self.screen.mainloop() 
```


## Detect Snake food collissions and eat food

snake.py:
    - Complete get distance method in Head
    - Complete detect collission food method in Snake (recommend use distance <= 15)
    - complete eat food method that increases size of snake body
    - call eat food method inside detect collission food method only if conditional statement is True

```python
class Head(Segment):
    def __init__(self, initial_position) -> None:
        super().__init__(initial_position)
        
    def move_forward(self):
        self.turtle.forward(20)
        self.position = self.turtle.pos()
        
    def turn_left(self):
        if self.direction != 'right':
            self.turtle.setheading(180)
            self.direction = 'left'
    
    def turn_right(self):
        if self.direction != 'left':
            self.turtle.setheading(0)
            self.direction = 'right'
        
    def turn_up(self):
        if self.direction != 'down':
            self.turtle.setheading(90)
            self.direction = 'up'
        
    def turn_down(self):
        if self.direction != 'up':
            self.turtle.setheading(270)
            self.direction = 'down'
    
    def get_distance(self, coord):
        return self.turtle.distance(coord)

class Snake:
    def __init__(self) -> None:
        self.head = Head((0,0)) 
        self.segments = [self.head, Segment((-20, 0)), Segment((-40, 0))] #segments stored in an array with 1 head and 2 segments
        self.previous_tail_coord = (-40, 0) # last tail coordination
        
    def move_forward(self):
        self.previous_tail_coord = self.segments[-1].get_position()
        for i in range (len(self.segments) - 1, 0, -1):
            self.segments[i].change_position(self.segments[i - 1].get_position()) #segments follow its front segments
        
        self.head.move_forward()
        
        
    def turn_left(self):
        self.head.turn_left()
        
    def turn_right(self):
        self.head.turn_right()
        
    def turn_up(self):
        self.head.turn_up()
        
    def turn_down(self):
        self.head.turn_down()
    
  #Eat food
    def eat_food(self):
        self.segments.append(Segment(initial_position=self.previous_tail_coord))

    #Check for collision with food
    def detect_collision_food(self, food) -> bool:
      if self.head.get_distance(food) <= 15:
          self.eat_food()
          return True
      return False
```

main.py:
    - Add conditional statement to handle collision with food, if fullfilled move food move_around
    - Add handle collission with food to the end of the game loop method 

```python
from time import sleep
from turtle import Screen
from snake import Snake
from food import Food

class GameController:
    def __init__(self) -> None:
        self.game_ended = False
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0) 
        
        self.snake = Snake()
        self.food = Food()
        
        self.screen.listen()
        self.screen.onkeypress(self.snake.turn_left, "Left")
        self.screen.onkeypress(self.snake.turn_right, "Right")
        self.screen.onkeypress(self.snake.turn_up, "Up")
        self.screen.onkeypress(self.snake.turn_down, "Down")
        self.game_loop()
        self.screen.exitonclick() 
        self.screen.mainloop() 
        
    def detect_collision_food(self):
        if self.snake.detect_collision_food(self.food):
            self.food.move_around()

    def game_loop(self):
        while not self.game_ended:
            self.snake.move_forward()
            sleep(0.1)
            self.screen.update()
            self.detect_collision_food()

game = GameController()
```

## Check for collisions with wall

snake.py: 
    - Complete check collission with wall method in Snake class
    -  Complete detect collission with self method in Snake class

```python
class Snake:
    def __init__(self) -> None:
        self.head = Head((0,0)) 
        self.segments = [self.head, Segment((-20, 0)), Segment((-40, 0))] 
        self.previous_tail_coord = (-40, 0)
        
    def move_forward(self):
        self.previous_tail_coord = self.segments[-1].get_position()
        for i in range (len(self.segments) - 1, 0, -1):
            self.segments[i].change_position(self.segments[i - 1].get_position())
        self.head.move_forward()
        
    def turn_left(self):
        self.head.turn_left()
        
    def turn_right(self):
        self.head.turn_right()
        
    def turn_up(self):
        self.head.turn_up()
        
    def turn_down(self):
        self.head.turn_down()
    
    def eat_food(self):
        self.segments.append(Segment(initial_position=self.previous_tail_coord))
        
    def detect_collision_food(self, food) -> bool:
        if self.head.get_distance(food) <= 15:
            self.eat_food()
            return True
        return False
    
    def detect_collision_wall(self) -> bool:
        headpos = self.head.get_position()
        return headpos[0] > 290 or headpos[0] < -290 or headpos[1] > 290 or headpos[1] < -290 #check if x-y coord inside screen
    
    def detect_collision_self(self) -> bool:
      for i in range (1, len(self.segments)): # check if head collide with any segments
          if self.head.get_distance(self.segments[i].get_position()) <= 10:
              return True
      return False
```

main.py :
    - add conditional statement to game over method for collission with wall if so change game state attribute
    - call game over method at the end of the game loop 
    - add detect collission with self to the conditionoal statement in game over method 

```python
from time import sleep
from turtle import Screen
from snake import Snake
from food import Food

class GameController:
    def __init__(self) -> None:
        self.game_ended = False
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0)
        
        self.snake = Snake()
        self.food = Food()
        
        self.screen.listen()
        self.screen.onkeypress(self.snake.turn_left, "Left")
        self.screen.onkeypress(self.snake.turn_right, "Right")
        self.screen.onkeypress(self.snake.turn_up, "Up")
        self.screen.onkeypress(self.snake.turn_down, "Down")
        self.game_loop()
        self.screen.exitonclick()
        self.screen.mainloop() 
        
    def detect_collision_food(self):
        if self.snake.detect_collision_food(self.food):
            self.food.move_around()
    
    def game_over_condition(self):
        if self.snake.detect_collision_self() or self.snake.detect_collision_wall():
            self.game_ended = True
    
    def game_loop(self):
        while not self.game_ended:
            self.snake.move_forward()
            sleep(0.1)
            self.screen.update()
            self.detect_collision_food()
            self.game_over_condition()

game = GameController()
```

# Create Scoreboard
scoreboard.py;
    - add basic visual setup and score attribute to Scoreboard class
    - complete write_score method (which should clear itself before writing score)
    - call write score method in init method 

```python
from turtle import Turtle 

class Scoreboard(Turtle):

    def __init__(self, shape: str = "classic", undobuffersize: int = 1000, visible: bool = True) -> None:
        super().__init__(shape, undobuffersize, visible)

        self.score = 0 
        self.color('white') 
        self.penup() 
        self.hideturtle() 
        self.goto(0,265) 
        self.write_score() 

    # method to write score
    def write_score(self):
        self.clear()
        self.write(f'Score: {self.score}', align='center', font=('Verdana', 25, 'normal')) 

    # increase the score by one and write the new score
    def increment_score(self):
        self.score += 1
        self.write_score()


```

main.py:
    - cerate an instance of scoreboaord inside the Gamecontroller class init method

```python
from time import sleep
from turtle import Screen
from scoreboard import Scoreboard
from snake import Snake
from food import Food

class GameController:
    def __init__(self) -> None:
        self.game_ended = False
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0)

        self.scoreboard = Scoreboard()
        self.snake = Snake()
        self.food = Food()
        
        self.screen.listen()
        self.screen.onkeypress(self.snake.turn_left, "Left")
        self.screen.onkeypress(self.snake.turn_right, "Right")
        self.screen.onkeypress(self.snake.turn_up, "Up")
        self.screen.onkeypress(self.snake.turn_down, "Down")
        self.game_loop()
        self.screen.exitonclick()
        self.screen.mainloop() 
        
    def detect_collision_food(self):
        if self.snake.detect_collision_food(self.food):
            self.food.move_around()
            self.scoreboard.increment_score() #increase score when eat food
    
    def game_over_condition(self):
        if self.snake.detect_collision_self() or self.snake.detect_collision_wall():
            self.game_ended = True
            
    
    def game_loop(self):
        while not self.game_ended:
            self.snake.move_forward()
            sleep(0.1)
            self.screen.update()
            self.detect_collision_food()
            self.game_over_condition()

game = GameController()
```

# Game Over screen

scoreboard.py:
    - Complete write_game_over method 

```python
from turtle import Turtle 

class Scoreboard(Turtle):

    def __init__(self, shape: str = "classic", undobuffersize: int = 1000, visible: bool = True) -> None:
        super().__init__(shape, undobuffersize, visible)

        self.score = 0 
        self.color('white') 
        self.penup() 
        self.hideturtle() 
        self.goto(0,265) 
        self.write_score() 

    # method to write score
    def write_score(self):
        self.clear()
        self.write(f'Score: {self.score}', align='center', font=('Verdana', 25, 'normal')) 

    # increase the score by one and write the new score
    def increment_score(self):
        self.score += 1
        self.write_score()

    # game over screen
    def write_game_over(self):
        self.goto(0,0)
        self.color("red")
        self.write("Game Over", align="center", font=("Verdana", 69, "normal"))
        self.color("white")
        self.goto(0,-30)
        self.write("Press Space to Continue", align="Center", font=("Verdana", 18, "normal"))
        self.goto(0,265)
 

```

main.py;
    - if conditional statement in game over is true call the scoreboard's write game over method 

```python
from turtle import Screen
from scoreboard import Scoreboard
from food import Food
from snake import Snake
from time import sleep

class GameController:
    
    def __init__(self) -> None:

        self.game_ended = False

        # Setup Screen
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0)
        

        # Initialize instance of game objects
        self.scoreboard = Scoreboard()
        self.snake = Snake()
        self.food = Food()
        
        # add screen listeners
        self.screen.listen()
        self.screen.onkeypress(self.snake.turn_left, 'Left')
        self.screen.onkeypress(self.snake.turn_right, 'Right')

        # start main loop
        self.game_loop()

        self.screen.exitonclick()

    # check whether snake head has touched food, if so eat it and move food
    def detect_collision_food(self):
        if self.snake.detect_collission_food(self.food):
            self.food.move_around()
            self.scoreboard.increment_score()

    # check for game over conditions and display game over screen
    def game_over(self):
        if self.snake.detect_collission_wall() or self.snake.detect_collission_self():
            self.game_ended = True
            self.scoreboard.write_game_over() 

 
    # main game looop, make snake go forward, update screen and check for collisioons with good and game over
    def game_loop(self):
        while not self.game_ended:
            self.snake.move_forward()
            sleep(0.1)
            self.screen.update()
            self.detect_collision_food()
            self.game_over()

GameController()
```

# Reset functionality
snake.py:
    - Complete the reset method in the snake class

```python
from turtle import Turtle 

# a unit of the snake
class Segment:
    def __init__(self, initial_position) -> None:
        self.position = initial_position
        self.turtle = Turtle(shape='square')
        self.turtle.color('white')
        self.turtle.penup()
        self.turtle.goto(initial_position)

    # return position 
    def get_position(self):
        return self.position

    # move segment to coordinate
    def change_position(self, new_position):
        self.turtle.goto(new_position)
        self.position = new_position

# unit of snake that represents head
class Head(Segment):
    def __init__(self, initial_position) -> None:
        super().__init__(initial_position)

    # advance
    def move_forward(self):
        self.turtle.forward(20)
        self.position = self.turtle.pos()

    # change directioon left
    def turn_left(self):
        self.turtle.left(90)
    
    # change direction right
    def turn_right(self):
        self.turtle.right(90)

    # check how far an object is
    def get_distance(self, coord):
        return self.turtle.distance(coord)

    # check if crashed with wall
    def collision_wall(self):
        return self.position[0] > 290 or self.position[0] < -290 or self.position[1] > 290 or self.position[1] < -290

class Snake:
    def __init__(self) -> None:
        self.head = Head((0,0))
        self.segments = [self.head, Segment((-20, 0)), Segment((-40, 0))]
        self.previous_tail_coord = (-40, 0)

    # move entire snake forward
    def move_forward(self): 
        self.previous_tail_coord = self.segments[-1].get_position()
        for i in range(len(self.segments) - 1, 0, -1): 
            self.segments[i].change_position(self.segments[i - 1].get_position()) 
        self.head.move_forward()

    # change directiions left
    def turn_left(self):
        self.head.turn_left()

    # change directions right 
    def turn_right(self):
        self.head.turn_right()

    # make snake bodoy larger when a food is eaten
    def eat_food(self):
        self.segments.append(Segment(self.previous_tail_coord))

    # check whether snake head touched food
    def detect_collission_food(self, food):
        if self.head.get_distance(food) <= 15:
            self.eat_food()
            return True
        return False
    
    # check whether snake head touched the body
    def detect_collission_self(self):
        for x in range(1, len(self.segments)):
            if self.head.get_distance(self.segments[x].get_position()) <= 10:
                return True
        return False

    # check whether snake touched screem edges
    def detect_collission_wall(self):
        return self.head.collision_wall()
    
    # make snake revert to initial size and position
    def reset(self): 
        for seg in self.segments: 
            seg.turtle.goto(100000,100000) 
        self.segments.clear() 
        self.head = Head((0,0)) 
        self.segments = [self.head, Segment((-20,0)), Segment((-40,0))]
        self.previous_tail_coord = (-40,0) 
```

scoreboard.py:
    - Complete reset method in scoreboard class

```python
from turtle import Turtle 

class Scoreboard(Turtle):

    def __init__(self, shape: str = "classic", undobuffersize: int = 1000, visible: bool = True) -> None:
        super().__init__(shape, undobuffersize, visible)

        self.score = 0 
        self.color('white') 
        self.penup() 
        self.hideturtle() 
        self.goto(0,265) 
        self.write_score() 

    # method to write score
    def write_score(self):
        self.clear()
        self.write(f'Score: {self.score}', align='center', font=('Verdana', 25, 'normal')) 

    # increase the score by one and write the new score
    def increment_score(self):
        self.score += 1
        self.write_score()

    # game over screen
    def write_game_over(self):
        self.goto(0,0)
        self.color("red")
        self.write("Game Over", align="center", font=("Verdana", 69, "normal"))
        self.color("white")
        self.goto(0,-30)
        self.write("Press Space to Continue", align="Center", font=("Verdana", 18, "normal"))
        self.goto(0,265)
 
    # revert to initial scoreboard 
    def reset(self):
        self.score = 0 
        self.write_score() 
```

main.py:
    - Complete reset method in Game Controller class
    - Add a keypress listener to game over mehtod (if game has ended) that calls reset method when activated
```python
from turtle import Screen
from scoreboard import Scoreboard
from food import Food
from snake import Snake
from time import sleep

class GameController:
    
    def __init__(self) -> None:

        self.game_ended = False

        # Setup Screen
        self.screen = Screen()
        self.screen.setup(width=600, height=600)
        self.screen.bgcolor('black')
        self.screen.tracer(0)
        

        # Initialize instance of game objects
        self.scoreboard = Scoreboard()
        self.snake = Snake()
        self.food = Food()
        
        # add screen listeners
        self.screen.listen()
        self.screen.onkeypress(self.snake.turn_left, 'Left')
        self.screen.onkeypress(self.snake.turn_right, 'Right')

        # start main loop
        self.game_loop()

        self.screen.exitonclick()

    # check whether snake head has touched food, if so eat it and move food
    def detect_collision_food(self):
        if self.snake.detect_collission_food(self.food):
            self.food.move_around()
            self.scoreboard.increment_score()

    # check for game over conditions and display game over screen
    def game_over(self):
        if self.snake.detect_collission_wall() or self.snake.detect_collission_self():
            self.game_ended = True
            self.screen.onkey(fun=self.reset_game, key="space") 
            self.scoreboard.write_game_over() 

    # reset the game
    def reset_game(self): 
            self.scoreboard.reset() 
            self.snake.reset() 
            # self.screen.onkey(fun=None, key="space") 
            self.game_ended = False 
            self.game_loop()

    # main game looop, make snake go forward, update screen and check for collisioons with good and game over
    def game_loop(self):
        while not self.game_ended:
            self.snake.move_forward()
            sleep(0.1)
            self.screen.update()
            self.detect_collision_food()
            self.game_over()

GameController()

```

Good job!!!.
