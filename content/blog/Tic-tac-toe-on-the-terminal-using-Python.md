---
title: "Tic-tac-toe on the terminal using Python"
date: 2020-11-05T19:58:11Z
draft: true
tags: 
- python
- programming
---

20 something years ago I was tasked with writing a Tic-Tac-Toe game for my A-level computing coursework at college. The allocated programming language was [Visual Basic](https://en.wikipedia.org/wiki/Visual_Basic).

For reasons that mainly involved [Counter-Strike](https://en.wikipedia.org/wiki/Counter-Strike_(video_game)) I didn't finish the tic-tac-toe code and it's bugged me ever since.

So here's an attempt to finish what I started all those years ago, albeit in a much more elegant language - Python.

For some reason I've taken to calling this game Tic-Tac-Toe, although I grew up calling it Noughts and Crosses. So take your pick.

```python
import random

board = [
    [0, 0, 0],
    [0, 0, 0],
    [0, 0, 0]
    ]

human_turn = False

def draw_board():
    count = 0
    print('   0  1  2')
    for row in board:
        print(count, row)
        count += 1
    print('')
    play_move()


def play_move():
    global human_turn
    if not human_turn:
        movex = random.randrange(3)
        movey = random.randrange(3)
        check_move(1, movex, movey)
    else:
        try: 
            movex = int(input('Enter a row number: '))
            movey = int(input('Enter a column number: '))
        except:
            print('Incorrect input. Try again...')
            play_move()
        else: 
            human_turn = False
            update_board(2, movex, movey)
            draw_board()


def check_move(player, movex, movey):
    global human_turn
    if board[movex][movey] == 0:
        update_board(player, movex, movey)
        if player == 1:
            human_turn = True
            play_move()
        else:
            human_turn = False
            play_move()
    else:
        play_move()


def update_board(player, movex, movey):
    board[movex][movey] = player
    return

def check_winner():
    pass


draw_board()

```
