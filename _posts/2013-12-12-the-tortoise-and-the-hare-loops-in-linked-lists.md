---
layout: post
title: "The Tortoise and The Hare: Loops in Linked Lists"
description: "Description of Flyod's Algorithm for detecting loops in linked lists, also known as the tortoise and the hare algorithm."
category: "Technical"
tags: ["C", "Java", Algorithms", "Interviews", "Jobs"]
---
{% include JB/setup %}

Okay, so today I had a phone interview for a job and I feel like it went fairly
well. I am notoriously bad over the phone so I was a bit nervous, but I think I
answered well for all the JavaScript questions. At the end I was given a
question which would apply to programming in C. This was not surprising because
I listed experience with C, C++, and other languages I have used a fair amount
in the past.

I probably didn't do so well on this last question, but I learned about an
algorithm I didn't know about before so there's my silver lining. One of my 
problems was that I was having trouble understanding the question over the phone
at first, but eventually I understood it as:

> How would you write a function that takes as an argument a linked list? The
> function should return true if the linked list has a loop, false otherwise.

After I understood this I thought about it. So basically, it's a function that
takes a linked list and tells you whether or not it's circular? Not quite. My 
first attempt was a description of something like this:

```cpp
/** Return true if a loop is found */
int detectLoop(Node * list) {
    Node * current = list;
    while (current = current->next) {
        if (current == listNode)
            return 1;
    }
    return 0;
}
```

The problem with this one as my interviewer pointed out, is that it only detects
circular linked lists, or lists that link back to the first node. I realized he
was right and tried again. After a minute of fumbling around with the problem I
came up with an inefficient solution (I believe it's `O(n^2)`) using a nested 
loop:

```cpp
int detectLoop(Node * list) {
    if (list->next == NULL) return 0;
    Node * current = list->next;
    int i = 0;
    do {
        Node * scan = list;
        int j = 0;
        do {
            if (scan = current) return 1;
            j++;
        } while (j < i && scan = scan->next);
        i++;
    } while (current = current->next);
    return 0;
}
```

I had no access to a computer and was trying to come up with something on my
own. I had never written a function to do this before. I think my answer was a
bit complicated to describe over the phone and I think the interviewer probably
had trouble understanding me completely. The interviewer told me we were out
of time after I gave this answer. With a single google search of 
`'loops linked lists'` I was able to figure out exactly the answer I should have
given. I am a huge fan of StackOverflow and always find good information there
that sometimes have been vetted by hundreds of programmers. Such was the case
with this question that pointed me to 
[Floyd's Cycle-Finding Algorithm][wiki-floyds-algorithm], also known as the
tortoise and hare algorithm. The algorithm is named for Robert W. Floyd, a
very accomplished computer sceintist and professor at Stanford, who invented it
in late 60s.

The algorithm is absolutely brilliant and I had to write about it.
The general idea is to have two pointers which you advance through the list
at different rates. It can be shown that if there is a loop, then eventually
both pointers will point to the same node. Not only will they eventually point
to the same node, they will do so in O(n) steps. Here is my C code for the 
algorithm:

```cpp
int detectLoop(Node * list) {
    Node * tortoise = list;
    Node * hare = list;

    do {
        if (!(hare = hare->next) || !(hare = hare->next)) {
            return 0;
        }
        tortoise = tortoise->next;
    } while (tortoise != hare);
    
    return 1;
}
```

The way I think about this algorithm is this. If there are no loops in `list`
then in less then the `hare` pointer will hit `NULL` before the `tortoise`
pointer. If there is a loop in `list` then we can think of the list in terms of
the length of the loop. Lets say there are *N* nodes in the loop. When the
tortoise makes *N* steps, the hare will have made exactly *2N* steps. Once they
are both in the loop they will meet after either one or two passes.

[so-question]: http://stackoverflow.com/questions/2663115/how-to-detect-a-loop-in-a-linked-list
[wiki-floyds-algorithm]: http://en.wikipedia.org/wiki/Cycle_detection#Tortoise_and_hare
