---
layout: post
title: "Bloom Filter"
date: 2014-10-09 10:18:34
categories: study-notes data-structures
---

## Overview

A bloom filter is a data structure which allows one to determine whether an
element is NOT in a set of elements in a space-efficient way. While it yields
no false negatives, it does produce false positives, and as a result, it may
not definitively answer whether an element is in a set.

## Pros

* Very memory efficient so can be used on large data sets
* Nonexistence can be checked very quickly
* Can be tuned to optimize probability of false positives

## Cons

* Cannot determine existence of elements, only nonexistence
* Optimization depends on hashing algorithm(s) used
* Elements cannot be removed

## Applications

A common application of a bloom filter is to determine if an element is
present before doing any disk I/O. For instance, when searching for an element
in a large dataset in a database, one would first check am in-memory bloom filter.
If the element is not in the bloom filter, then there is no need to perform costly
disk operations to fetch a nonexistent element. If the bloom filter returns that
the element may exist, however, then it would be appropriate to check the disk
(even though this may still result in a miss).

Facebook used a bloom filter in their implementation of lookahead search as described
in the following video: [https://www.facebook.com/video/video.php?v=432864835468](https://www.facebook.com/video/video.php?v=432864835468)

## Implementation

A bloom filter is implemented as an array of bits. Initially, all bits are set to
false. When an element is added, multiple hashes are computed, each mapping to a
bit in the array. For each hash, the corresponding bit is set in the array. To
check if an element is present, you test each bit corresponding to the element’s
hashes. If any of these bits are false, then the element is not in the set, since
adding it would’ve set each of the corresponding bits. However, if all of the
corresponding bits are set, that does not guarantee that the element is in the sit,
since each of these bits could have been set separately by other elements’ hashes.

Here's a basic implementation I wrote in Ruby. The complete gist can be seen [here](https://gist.github.com/okalex/14688ab3a49d6ead1c67).

<script src="https://gist.github.com/okalex/14688ab3a49d6ead1c67.js?file=bloom_filter.rb"></script>
