---
layout: post
title: Python - Lists, dictionaries & for-loops problems
tags: python
---


List and dictionaries with for loops is where Python gets tricky for beginners for the first time. It certainly did for me. 
There were exercises that I did wrong every time I attempted them and it didn’t even occur to me that I am overseeing the same things repetitively. Only after writing them down and describing the solutions I used, I was able to match the problem with a solution AND understand it. Before I just moved quickly between exercises without giving the solution too much thought, which of the 20 trials was successful.

Each time the problems appeared when working with some text data in a file where I needed to iterate through each line. I worked out the examples based on a simple People.txt file:

*Anne is 23 years old and has 7 dogs.<br>
John is 34 years old and has 5 dogs.<br>
Ted is 22 years old and has 0 dogs.<br>
Lisa is 27 years old and has 2 dogs.<br>
Ron is 17 years old and has 1 dog.<br>
George is 45 years old and has 1 dog.<br>
Andrew is 24 years old and has 1 dog.<br>
Alex is 24 years old and has 2 dogs.<br>
Jane is 35 years old and has 3 dogs.<br>
Greta is 31 years old and has 0 dogs.*

## Problem #1 Create a dictionary with name of a person and their age from a text file.

Repeatedly I started as follows:

**Input:**

```
file = open("People.txt", "r")
age_dict = {}

for person in file:
    psplit = person.split(' ‘)
    age_dict['name'] = psplit[0] 
    age_dict['age'] = psplit[2] 

print(age_dict)
```

**Output:**

```
{'name': 'Greta', 'age': ’31'}
```

It’s so wrong, I kept on overseeing the most basic characteristics of a dictionary - **keys have to be unique**. With split() function I created a list of words - *psplit*, which means *file* is now a list of lists. This code loops through each person and prints only the data for the last list *psplit*. 
This mistake probably comes from the fact, that when I was learning about loops for the first time I didn’t pay attention whether (and when) print function is in or outside the loop. If it was in the loop, we would get 10 dictionaries, one for each person. However in that case we can’t assign the result to any variable.


Another solution could be creating a list of dictionaries, each of them having the same keys: name and age. But before I manage to do that I fall into another trap...

## Problem #2 Create a list of dictionaries with name and age of a person from text file.

**Input:**
```
file = open("People.txt", "r")

age_dict = {}
people = []

for person in file:
    psplit = person.split(' ')
    age_dict['name'] = psplit[0]
    age_dict['age'] = psplit[2]
    people.append(age_dict)

print(people)
```
**Output:**
```
[{'name': 'Greta', 'age': '31'}, {'name': 'Greta', 'age': '31'}, {'name': 'Greta', 'age': '31'}, …]
```

This code also doesn’t bring expected result. I get the right number of dictionaries but again, with data from the last list created. That mistake can be also put down to the same issue - what and when goes in or outside the loop. Here I create the dictionary first and then start the loop. With each iteration, *people* list with information about first *person* becomes overwritten by information about second *person* etc. The key here is to create a new dictionary for each person - with every loop. At the end of the loop *age_dict* is added to people list. Now it’s ok to create a completely new dictionary, add data about other person, append to the people list and start the loop again. Finally the desired result:

**Input:**
```
file = open("People.txt", "r")

people = []

for person in file:
    age_dict = {}
    psplit = person.split(' ')
    age_dict['name'] = psplit[0]
    age_dict['age'] = psplit[2]
    people.append(age_dict)

print(people)
```
**Output:**

```
[{'name': 'Anne', 'age': '23'}, {'name': 'John', 'age': '34'}, {'name': 'Ted', 'age': '22'}, {'name': 'Lisa', 'age': '27'}, {'name': 'Ron', 'age': ’17’},…]
```


## Problem #3 Create a dictionary with data from text file when names are keys and ages their values.

There are times though, when creating a dictionary outside of for loop would make sense and still would bring results for each line/list of a file we work with. In this case it was possible to create a key of a person's name and assign a value with number of dogs.

**Input:**
```
file = open("People.txt", "r")

dictionary = {}

for person in file:
    name = person[0: person.find(" is”)]
    dogs = person[person.find("has ") + 4: person.rfind(“ dog")]
    dictionary[name] = dogs

print(dictionary)
```
**Output:**

```
{'Anne': '7 ', 'John': '5 ', 'Ted': '0 ', 'Lisa': '2 ', 'Ron': ‘1, … }
```

## Problem #4 Create strings with data from a dictionary.

An inverse problem is to extract data from *dictionary* created in Problem #3 and write full sentences. This turns out to be simple with just two lines of code and .items() method, that produces list of tuples with two values.

**Input:**
```
for key, value in dictionary.items():
    print("{} lives with {} dogs.".format(key, value))
```

**Output:**
```
Anne lives with 7 dogs.
John lives with 5 dogs.
Ted lives with 0 dogs.
…
```

## Problem #5 Create a dictionary where values are calculated from data in a text file.

Similarly as in Problem #3, doing some calculations on dictionary values requires a dictionary definition before the loop. Here I calculate total and average number of dogs per person:

**Input:**
```
file = open("People.txt", "r")

number_of_dogs = {}
dogs_list = []

for person in file:
    dogs = int(person[person.find("has ") + 4: person.rfind(" dog")])
    dogs_list.append(dogs)

number_of_dogs['total number of dogs'] = sum(dogs_list)
number_of_dogs['average number of dogs'] = sum(dogs_list)/len(dogs_list)

print(number_of_dogs)
```
**Output:**
```
{'total number of dogs': 22, 'average number of dogs': 2.2}
```

## Problem #6 Create a dictionary where values from text file are grouped by keys.

Here I group age of people by the number of dogs. How old are people owning 1 dog? The challenge is to first create a list under the key “dogs” so that multiple ages can be appended.

**Input:**
```
file = open("People.txt", "r")

average_age = {}


for person in file:
    age = int(person[person.find("is ") + 3: person.rfind(" years")])
    dogs = int(person[person.find("has ") + 4: person.rfind(" dog")])

    if average_age.get(dogs) is None:
        average_age[dogs] = []

    average_age[dogs].append(age)    

print(average_age)
```
**Output:**

```
{7: [23], 5: [34], 0: [22, 31], 2: [27, 24], 1: [17, 45, 24], 3: [35]}
```


## Lessons learned:
* Dictionary keys are always unique.
* If you want one list/dictionary as a result - create it outside the loop.
* To produce multiple, different lists/dictionaries that can be appended to one main list, create them in the loop.
* Appending multiple elements from a file to a list or assigning multiple keys/values to a dictionary always happens in the loop.
