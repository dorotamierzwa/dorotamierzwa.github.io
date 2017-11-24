---
layout: post
title: Take your Python calculator to the next level
tags: python
---

At the very beginning of learning of a programming language you are very thorough. When there is a task, you solve it, and make your code look exactly as in the given answer, sometimes even changing your names of variables to "correct" ones. And then you hear "There are many ways to solve this problem". And you go, *wait what?*

Today I am going through few different ways of building a calculator in Python. I think it shows nicely how differently you can approach a task and push to be more creative even for simple tasks.

## The basic calculator

This calculator was shown on one of first Python classes I attended. It introduces the idea of simple functions, input and loops. 

**Input:**
```
# define function for each operation
def addition(a, b):
    return a + b

def substraction(a, b):
    return a - b

def multiplication(a, b):
    return a * b

def division(a, b):
    return a / b

print("""
Choose your operation:
1. Addition
2. Substraction
3. Multiplication
4. Division

Type Q to quit.
""")

# the loop will ask the user for input for new operation after providing result for the previous one

run = True

while run:
    choice = input("Choose number of desired operation:")

    if choice == "Q":
        break

    else:

        x = int(input("First number:"))
        y = int(input("Second number:"))

        if choice == '1':
            print("Your result: ", addition(x, y))

        elif choice == '2':
            print("Your result: ", substraction(x, y))

        elif choice == '3':
            print("Your result: ", multiplication(x, y))

        elif choice == '4':
            print("Your result: ", division(x, y))

        else:
            print("Invalid input")
```



**Output**
```
Choose your operation:
1. Addition
2. Substraction
3. Multiplication
4. Division

Type Q to quit.

Choose number of desired operation:2
First number:5 
Second number:3
Your result:  2
Choose number of desired operation:
```

As this code is introduced early, you can see its limitations easily. You have limited operations to choose from, also it allows to perform calculations on only two numbers at a time.

## The advanced calculator

An actual inspiration for this post was code shared by Nick Germaine on [Github](https://github.com/nickgermaine/MagicCalculator/blob/master/main.py). I stumbled upon it on a Udemy Python course. Nick wrote a code that was meant to work as a real calculator, where you can perform calculations on results of previous equations.

**Input:**
```
import re

print("A fancy calculator")
print("Type 'quit' to exit\n")

previous = 0
run = True

def perform_math():
    global run
    global previous

    equation = ""

    # defining equation as input from user depending on the previous result

    if previous == 0:
        equation = input("Enter equation:")
    else:
        equation = input(str(previous))

    # closing on quit or cleaning equation from any other characters than numbers

    if equation == "quit":
        print("Thanks")
        run = False
    else:
        equation = re.sub('a-zA-z:,.()" "', "", equation)

        if previous == 0:
            previous = eval(equation)
        else:
            previous = eval(str(previous) + equation)

while run:
    perform_math()

```


The code looks much more complicated than the first one, but it doesn't require advanced skills to understand it. Similarily as the basic calculator it's based on a function, user input and loops.

**Output:**
```
A fancy calculator
Type 'quit' to exit

Enter equation:2+2 # user typed 2+2
40 # result is 4, user typed 0
40 # result
```


I made few alterations to Nick's code. In the orginal form, each time a result was produced (**4**) and user chose a number without specifying an operation (**0**), is simply added two numbers as a string (resulting in **40**):

Normally when using a traditional calculator, not specifying an operation simply means you start a new calculation. So I added few lines marked with a comment. The calculator works now as follows:

**Input**
```
import re

print("Fancy Calculator")
print("Type 'quit' to exit\n")

first_equation = True
run = True

def perform_math():
    global run
    global first_equation
    global previous

    # added code - if this is not first equation and user inputs a number (no operands),
    # it is treated as a new equation

    if not first_equation:
        equation = input(str(previous))

        if equation[0] not in ['+', '-', '/', '%', '*', '**']:
            first_equation = True

    else:
        equation = input("Enter first number: ")

    if equation == "quit":
        print("Ok bye")
        run = False

    else:
        equation = re.sub('[a-zA-z:,.()" "]', "", equation) # making sure there are only numbers

        if first_equation:
            previous = equation
            first_equation = False
        else:
            previous = eval(str(previous) + equation)

while run:
    perform_math()
```

**Output:**
```
Fancy Calculator
Type 'quit' to exit

Enter first number:2
2+2 # user typed +2
40 # result is 4, user typed 0
0 # result
```

This still doesn't work perfectly, as you can't enter a whole equation in the first line (with an operand). You can only specify number you will use as first in you equation. But for now I think this gives a slightly better feel as a normal calculator.

There are two tricky things in this piece of code that I learned with that particular task on Udemy. 

* re.sub() that comes from *re* library. It allows to clean the *equation* input from any lower and upper case letters, spaces and punctation marks by replacing them with "" - nothing, no characters.
* eval() function. In the documentation you can find that with eval() "argument is parsed and evaluated as a Python expression". This sounds very complicated, if the code above doesn't tell you what it does, take a look at the next calculator.

## Explaining eval() with a calculator

**Input:**
```
a = int(input("Type first number:"))
b = int(input("Type second number:"))
Operation = input("Choose operation: +, -, /, * , ** ")

print(eval("{}{}{}".format(a, Operation, b)))
```

This code can perform calculation, that was passed to a string. 

**Output:**
```
Type first number:2
Type second number:2
Choose operation: +, -, /, * , **
4
```

In this case Python is able to read it and evaluate this expression as it would normally do, if you wrote that in command line. Without the eval() function it would not be possible to perform calculation, the result is just a string:

**Output:**
```
Type first number:2
Type second number:2
Choose operation: +, -, /, * , **
2+2
```

This is just to show in a simpler way how eval() works. However beware of using it without constraints. Eval() can even harm your system ([Google more on this](https://stackoverflow.com/questions/1832940/why-is-using-eval-a-bad-practice)), so make sure you understand how and when to use it in the code you're writing.

