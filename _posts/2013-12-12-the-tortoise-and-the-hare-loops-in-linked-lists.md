---
layout: post
title: "The Tortoise and The Hare: Loops in Linked Lists"
displayTitle: "The Tortoise and The Hare"
tagline: "Loops in Linked Lists"
description: "Description of Flyod's Algorithm for detecting loops in linked lists, also known as the tortoise and the hare algorithm."
category: "Technical"
tags: ["C", "Java", "Algorithms", "Interviews", "Jobs", "Linked Lists"]
highlights: true
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

> How would you write a function that takes a linked list as an argument and
> should return true if the linked list contains a loop, and return false 
> otherwise.

After I understood this I thought about it. So basically, it's a function that
takes a linked list and tells you whether or not it's circular? Not quite. My 
first attempt was a description of something like this. Looking back I was
obviously nervous and rushing it.

```
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
came up with an inefficient solution (which I believe is quadratic) using a
nested loop:

```
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
with [this question][so-question] that pointed me to 
[Floyd's Cycle-Finding Algorithm][wiki-floyds-algorithm], also known as the
tortoise and hare algorithm. The algorithm is named for Robert W. Floyd, a
very accomplished computer sceintist and professor at Stanford, who invented it
in late 60s.

The algorithm is absolutely brilliant and I had to write about it.
The general idea is to have two pointers which you advance through the list
at different rates. It can be shown that if there is a loop, then eventually
both pointers will point to the same node. Not only will they eventually point
to the same node, they will do so in O(n) steps. Here is my C implementation:

```
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

This is by no means a formal proof, but here is how I think about the above
algorithm:

 - If the list does not contain a loop, we will eventually hit `NULL` and return
   false. If incremented first, the hare pointer will always be `NULL` before
   the tortoise pointer.
 - If the list does contain a loop, then both pointers will end up in the loop
   eventually. It is *not* possible for the pointers to meet before they enter
   the loop.
   - Assume the list *does* contain a loop of length *L*. At this point, I think 
     the simplest way to think about the algorithm at this point is to consider
     only the loop. Take the starting location of the loop to be node `0`.
   - When the tortoise pointer enters the loop at node `0`, the hare pointer 
     will have already entered the loop and may be located at any node in the 
     loop (ranging from node `0` to node `L-1`).
   - When the tortoise pointer enters the loop at node `0` we will consider the
     unknown location of the hare pointer to be an unknown number of steps `p`
     *behind* the tortoise pointer in the loop and will eventually catch up to 
     it. 
   - Once entering the loop at node `0`, after `t` iterations the tortoise
     pointer will be located at node `t mod L` and the hare pointer will be
     located at the node `(2t - p) mod L`.

Now if we create an equation using the above values, we can easily see that the
hare pointer will catch up and meet the tortoise pointer after `t = p`
iterations (after `tortoise` enters the loop). 

     2t - p = t   -->   2t = t + p  -->  t = p

The total number of steps required is the number of steps (or number of nodes)
prior to entering the loop plus the number of steps after entering the loop `p`.
Although we do not know the value of `p`, we do have an upper bound (which is
the important thing) and that is the length of the loop *L*. 

If we want an upper bound on the overall number of iterations/steps for the
algorithm, we can consider the number of nodes in the list as *N*, the number
of nodes in the loop as *L*, and the number of nodes not contained in the loop
as *K*. We then have an upper bound of `L + K = N` steps. Therefore, we do
indeed have a linear-time algorithm for detecting loops in linked lists.

[so-question]: http://stackoverflow.com/questions/2663115/how-to-detect-a-loop-in-a-linked-list
[wiki-floyds-algorithm]: http://en.wikipedia.org/wiki/Cycle_detection#Tortoise_and_hare
