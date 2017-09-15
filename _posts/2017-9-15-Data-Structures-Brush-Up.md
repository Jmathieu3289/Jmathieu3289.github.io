---
layout: post
title: Data Structures Brush Up
---

Every now and then it's good to go back and make sure you refresh your knowledge on common data structures, algorithms and design patterns. Over the next few posts I will go over these topics, adding some basic information about each and a basic implementation in Javascript. In this first post I'll be starting with data structures.

### Array

The array is a basic data structure, where elements are typically stored starting at a 0 index. They can be declared many ways in various programming languages, but in javascript arrays are actually a high-level list-like objects with support for behaving like more complicated data structures such as stacks or queues.

```javascript
var a = [1,3,5,7,9];
var b = a[1];
console.log(b);
```
output:
```
3
```

Arrays have O(1) access time and O(n) search and insertion, making them a good choice when fast access to a known index is needed over fast insertion or search.

### Stack 

A stack is a data structure where elements are inserted and removed in a last-in-first-out manner (LIFO). Some common uses of stacks are depth-first search (DFS) and undo or reverse operations. Functions also act in a stack-like manner, with calls made to inner functions returning to their calling functions after execution.

```javascript
//A basic browser "history"
var history = [];

function visit(url) {
    a.push(url);
    console.log("Visiting: " + history.peek());
}

function back() {
    if(!history.isEmpty()) {
        history.pop();
        console.log("Returning to: " + history.peek());
    }else{
        console.log("You have reached the end of your history!");
    }
}

//Let's visit some websites!
visit("https://www.google.com");
visit("https://www.stackoverflow.com");
visit("https://www.reddit.com");

//Now lets go all the way back!
back();
back();
back();
```
output:
```
Visiting: https://www.google.com
Visiting: https://www.stackoverflow.com
Visiting: https://www.reddit.com
Returning to: https://www.stackoverflow.com
Returning to: https://www.google.com
You have reached the end of your history!
```

Stacks have O(n) access time and O(1) insertion and deletion which make them good for insertion and deletion, but for a more generalized data structure with the same time and space complexity you could use a Linked List.

### Queue

A queue is a data structure where elements are inserted and removed in a first-in-first-out manner (FIFO). Some common uses of queues are breadth-first search (BFS) as well as processing where the first element in should be the first element processed (which can be further refined by a PriorityQueue).

```javascript
//Breadth-first search
/*Let's assume we have the following tree to search, where each node has a name property:
 *           a
 *          / \
 *         b   c 
 *        /\   /\
 *       d  e f  g
 */

 var queue = [];
 queue.enqueue(a);

 console.log("Walking tree...");
 while(!queue.isEmpty()) {
    var n = queue.dequeue();
    console.log("Searching: " + n.name);
    for(var i=0; i<n.children.length; i++) {
        queue.enqueue(n.children[i]);
    }
 }
 console.log('Finished!');
```
output:
```
Walking tree...
Searching a
Searching b
Searching c
Searching d
Searching e
Searching f
Searching g
Finished!
```

Queues have O(n) access time and O(1) insertion and deletion like stacks.

### Hash Table

A Hash Table is a data structure where each element is stored as a key/value pair, where looking up a given key will return the value. Hash tables are very good when speed is a requirement or when keys need to be something other than integer values. This is because hash tables make use of a hash function, which maps an object (of some type) to an integer. The downsides are they often come with much higher memory overhead, and hash functions can be prone to hash collisions, where the output of the hash function is the same for two different inputs. In Javascript every object is a simple hashmap that accepts strings as keys.

```javascript
var sounds = [];
sounds['cat'] = "Meow";
sounds['dog'] = "Woof!";
console.log("The dog goes " + a['dog']);
```
output:
```
The dog goes Woof!
```

Hash tables have O(1) average case search, insertion and deletion, making them a great choice when memory is not a concern. However, be aware of the downsides and understand that, although rare, [hash collisions happen]("https://shattered.io/"), even with very complex hash functions. 


### In Conclusion

That's it for now, but I'll continue to update this list with other useful data structures like Linked Lists, Binary Search Trees and Graphs!

Thanks for reading!
