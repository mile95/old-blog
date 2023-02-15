---
layout: post
title:  "5 Takeaways from Curses Programming with Python"
date:   2022-04-26 10:10:15 +0700
tags: [python]
---

Curses is a terminal control library for Unix systems that makes it possible to write Text User Interface (TUI) applications. You rarely see them in today’s world of graphical user interfaces, but I find them quite cool hence I decided to create one myself.

Here in Sweden, there’s a popular football betting pool game called Stryktipset where the player is supposed to predict the outcome of 13 games. I wrote a TUI application using python and curses that corrected the players’ system while the games were live. 

## Takeaway 1 - The Curses module is part of pythons standard lib

The curses module is included in the Python version for Unix. All you need to get started is to write: `import curses`. Pythons curses module is a basic wrapper over the C functions provided by curses, and it is intuitive to use.

## Takeaway 2 - Use the provided wrapper

During the development of my TUI application, my terminal window became a mess after the application crashed. My terminal input was not displayed correctly and the text ended up in weird places. I had to reset the terminal after each crash.

These complications can be avoided by using the `curses.wrapper` function. This wrapper function makes sure that the application exit in a safe way, i.e performs a good clean-up before shutting down. 

The wrapper can be used like this

```python
import curses

def main(stdscr: Curses._CursesWindow) -> None:
	# Actual code related to the curses application

if __name__ == "__main__":
	# Stuff that needs to be handled before the curses application starts.
  # For instance, argparse.
	curses.wrapper(main)
```

## Takeaway 3 - User input is difficult

It is difficult to handle the user input correctly. There are so many cases that you have to cover. Here are some cases that you have to hand yourselves.

- By default, the user can overwrite existing text. The user can freely move around the curser using the arrows and replace text.
- If you expect user input, you need to handle weird characters like `\n` and `\r` .
- Always keep the cursor at an appropriate place. Where should it be moved to after the user has given their input?

## Takeaway 4 - Rows and Columns

You specify the `y` coordinate before the `x` coordinate, for instance, the `addsr` function is used like this: 

```python
stdscr.addstr(y_index, x_index, "Hello World")
```

## Takeaway 5 - Use `napms` instead of `time.sleep`

Most often when writing a TUI application you will have a `while True` loop that updates the application every x time unit. It is preferable to use the `curses.napms` instead of `time.sleep` since `time.sleep` gets interrupted by `SIGWINCH` events which could cause your application to exit if not handled correctly. 

## Extra resources and inspiration

- Docs: [https://docs.python.org/3/howto/curses.html](https://docs.python.org/3/howto/curses.html)
- My application: [https://github.com/mile95/cli-stryket](https://github.com/mile95/cli-stryket)
- AnthonyWriteCode has some really good videos for curses programming in python
    - DVD Screensaver: [https://www.youtube.com/watch?v=mVwAehkeBkI](https://www.youtube.com/watch?v=mVwAehkeBkI)
    - Wordle clone: [https://www.youtube.com/watch?v=dViRI1iovoc](https://www.youtube.com/watch?v=dViRI1iovoc)
    - Colors: [https://www.youtube.com/watch?v=M8gC65VgApM](https://www.youtube.com/watch?v=M8gC65VgApM)