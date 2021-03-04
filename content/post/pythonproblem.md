---
title: "How I approach a problem - use WWCode ATX Python night problem as example"
date: 2020-01-31T00:00:00-05:00
draft: true
---

I am probably the only person who claim to be good at whiteboarding interviews. Not saying my way is the only way, but here's a demo of how I approach a problem.

This question is from Women Who Code Austin Python meetup on 1/15/2020:
  
>Write a function that accepts a string and returns a dictionary of words as the keys, and the number of times each word appeared as a value. Make sure that the mapping works with words reardless of case, and handle punctuation.

The first step is to clarify what we are trying to do. This is one of the hardest part! 

The original problem is a very long sentence and hard for me to wrap my head around. So I break this into the following:

1. We are writing a function. The input of the function is a string.
1. The function returns a dictionary. Dictionaries are key-value pairs. And... 
1. The keys are words.
1. The values is the number of times each word appeared.

This is still pretty abstract. So I would try to work on an example. 
As we know, the input is a string. So let's say, our input is "What would a woodchuck chuck if a woodchuck could chuck wood". 

What would the output be? Reading the problem again, the output is a dictionary. So I would write down: `{}` to start. 

Now I can fill in the keys. It is not very clear what words, but since the only clue so far is the input, I can assume it's the words in the input sentance.
I also know that the keys in a dictionary are all unique. So I can assume my output to be like: 
```
{
  'What': ?,
  'would': ?,
  'a': ?,
  'woodchuck': ?,
  'chuck': ?,
  'if': ?,
  'could': ?,
  'wood': ?
}
```
And the values are the number of times each word appeared. So the output should be:

```
{
  'What': 1,
  'would': 1,
  'a': 2,
  'woodchuck': 2,
  'chuck': 2,
  'if': 1,
  'could': 1,
  'wood': 1
}
``` 

Now I know my goal, I can start to figure out how I can turn the tougue twister into a dictionary. 
Note that I am still thinking in terms of actual example, not code. 
Since I just finished deriving this example, I subconsciously know how solve this. 
This step is about writing down my thought process into plane English.
My mental model is:
1. First look at the first word in the sentence. A word is all the letters between 2 spaces.
1. Check if I've seen this word.
1. If not, I'll add this to the key. And record that this word appears once.
1. If I've seen it, I'll find the previous record, and mark that the word appears one more time.
1. Look at the second word in the sentence. Do all the things I did above. 
1. After all the words are looked at, return the word count. 

Once I have this, I can start translating English into code! 
First step would define the function signature. I usually write down my input and output at this time too:
```python
def count_words(input_string):
    output_dict = {}
    return output_dict
```

"Working through all the words" indicates that I need a loop. 
The loop loops through a list of words. 
The first step would be creating a list of words.

```python
def count_words(input_string):
    input_list = input_string.split(' ')
    output_dict = {}
    return output_dict
```
And now comes the loop, and fill in the steps I described. 

```python
def count_words(input_string):
    output_dict = {}
    word_list = input_string.split(' ')
    for word in word_list:
        if word in output_dict:
            output_dict[word] += 1
        else:
            output_dict[word] = 1
    return output_dict
```
{'How': 1,
 'much': 1,
 'wood': 2,
 'would': 1,
 'a': 2,
 'woodchuck': 2,
 'chuck': 2,
 'if': 1,
 'could': 1}