---
layout: post
title: Java Collections Framework
description: Nuances of Java Collection Framework
headline: "Nuances of Java Collection Framework"
categories:
  - Softwaredevelopment

tags: 
  - Java
  - Tricky Questions
  - Core Java
  - Collections
  - MultiThreading 
comments: true
mathjax: null
featured: true 
published: true
---

I am not going to explain what are java collections and why or when it should be used. Do a favour google it. I will mention only subtle difference and tricky concepts here.

### HashMap vs HashTable 

1. HashTable exists from Java 1.2 when collections framework was introduced Authors of Java collections API wanted to improve hashtable so they introduced HashMap. HashMap is different from HashTable in following ways 
1. HashMap is not synchronized where Hashtable is. Hashmap is not synchronized because for multi-threaded environment enhanced version was made available ConcurrentHashMap.
2. HashMap allows one null key and multiple null values where hashtable doesnt allow null keys.
3. HashMap allows one null key as it can be used for default scenarios.

Note : Though HashMap allows null key concurrent hashmap doesn't. Found this one on [StackOverFlow](http://stackoverflow.com/questions/698638/why-does-concurrenthashmap-prevent-null-keys-and-values)

>
The main reason that nulls aren't allowed in ConcurrentMaps (ConcurrentHashMaps, ConcurrentSkipListMaps) is that ambiguities that may be just barely tolerable in non-concurrent maps can't be accommodated. The main one is that if map.get(key) returns null, you can't detect whether the key explicitly maps to null vs the key isn't mapped. In a non-concurrent map, you can check this via  map.contains(key), but in a concurrent one, the map might have changed between calls.



### Ordering In Collections 
List Always maintains insertion order whereas Set & Map depends on its Implementations. 
HashSet - is based on HashMap so it doesnt perserve insertion order 
TreeSet - Maintains natural ordering based on the comparator provided.
LinkedHashSet- Maintains insertion order 

Same applies for Map as well.

Here is the Cheet sheet for [Java Collections API](http://pedrocardoso.eu/scjp-java-collections-cheat-sheet)




