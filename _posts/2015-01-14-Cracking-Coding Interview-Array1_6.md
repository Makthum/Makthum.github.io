---
layout: post
published: true
title: Program to Rotate NxN matrix by 90 degrees in place.
mathjax: false
featured: true
comments: true
headline: Blogging using Github pages + Jekyll
categories: 
  - webdevelopment
  - SoftwareDevelopment
tags: 
  -Cracking Coding Interview
  -Matrix Rotation
  -Java
---
Given an image represented by an NxN matrix, where each pixel in the image is 4 bytes, write a method to rotate the image by 90 degrees Can you do this in place?

This question is an interesting one as it requires you too do the rotation in place without using additional array. I was able to crack the logic behind it though i had to take some help before I could completely put the answer on paper.

Question
========

What does the question wants you to solve ? Suppose you are given an array like 

  0 0 1 &nbsp;&nbsp;&nbsp;90 degree clockwise &nbsp;&nbsp;&nbsp;&nbsp;1 0 0 <br>
  0 1 0 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;------------------>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0 1 0 <br>
  1 0 0 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0 0 1 <br>

  catch here is you need to do them in place.

 if you compare orginial matric and the roated matrix you can find out the row 1 and 3 have be reversed to get the result matrix. In this case it looks good. I wanted try same logic with all unique values 

  00 01 02 &nbsp;&nbsp;&nbsp;90 degree clockwise &nbsp;&nbsp;&nbsp;&nbsp;20 10 00 <br>
  10 11 12 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;------------------>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 21 11 01 <br>
  20 21 22 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 22 12 02 <br>

 

  so looking at this example it is clear that it not just as simple as reversing row 1 and 3. But I also noticed there is some kind of swapping happening around the edge elements

  **00** 01 **02** &nbsp;&nbsp;&nbsp;90 degree clockwise &nbsp;&nbsp;&nbsp;&nbsp;**20** 10 **00** <br>
  10 11 12 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;------------------>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 21 11 01 <br>
  **20** 21 **22** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **22** 12 **02** <br>

 
you can now see its one clockwise rotation of edge elements.

  00 **01** 02 &nbsp;&nbsp;&nbsp;90 degree clockwise &nbsp;&nbsp;&nbsp;&nbsp;20 **10** 00 <br>
  **10** 11 **12** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;------------------>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **21** 11 **01** <br>
  20 **21** 22 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 22 **12** 02 <br>


 When you look at the second set of values rotated you can find that all values are located at certain offset from the last element or first element of that row. Using the intuition we will try to understand the logic behind this code snippet.

 {% highlight java %}
 
 public static void rotate(int[][] matrix, int n) { 
  for (int layer = 0; layer < n / 2; ++layer) {
    int first = layer; intlast=n-1-layer;
    for(int i = first; i < last; ++i) {
      int offset = i - first;
      int top = matrix[first][i]; // save top
      // left -> top
      matrix[first][i] = matrix[last-offset][first];
      // bottom -> left
      matrix[last-offset][first] = matrix[last][last - offset];
      // right -> bottom
      matrix[last][last - offset] = matrix[i][last];
      // top -> right
      matrix[i][last] = top; // right <- saved top 
      }
    }
}

{% endhighlight %}

source : Cracking Coding Interview Solutions

This solution is expanded to any matrix of size N. Applying the same logic we saw in our examples. In this code First Loop iterates to half of N and each time first element to start the iteration with is incremented. I am not gonna explain the debugging this code with example as i leave it you. I hope this helps.