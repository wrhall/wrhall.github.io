---
layout: post
title:  "Merge Sort"
date:   2013-09-06 00:00:00
categories: ruby blog
---

Earlier this year, a friend and I decided to implement some sorting algorithms in ruby.
Here I'll briefly explain my merge sort code.

## Finding subproblems
Merge sort is an algorithm used to sort an array of objects recursively.
The idea of mergesort (and more generally, the idea of divide and conquer algorithms) is to solve a problem by breaking it into smaller problems.
The trick is that the smaller problem should be in the same form as the initial problem.
For sorting, this means one must divide the initial array into two smaller arrays that need to be sorted.

## The 'base case'
Now, eventually you will get an array that cannot be broken down anymore (for example, because it is size 1).
But an array of size 1 is already sorted, so the next step is just merging (yes, hence the name) the sorted arrays and 'popping' up a step.

# An example
To implement a divide & conquer program (or just to think about a D & C algorithm), one approach is to focus on a single step.

For example, we could consider how to use these general ideas on an array of size 2.
{% highlight ruby %}
a = [2, 1]
first_half = a[0]
second_half = a[1]
a_sorted = merge(first_half, second_half)
{% endhighlight %}

It seems as though we have thrown all of our magic in the merge step. In fact, the merge step is the step that must correctly order the `first_half` and `second_half`.

So what does the merge step look like?
For just two elements we can compare which one is smaller and put it first.

{% highlight ruby %}
def merge(first_half, second_half)
  sorted_array = []
  if first_half < second_half
    sorted_array << first_half
    sorted_array << second_half
  else
    sorted_array << second_half
    sorted_array << first_half
  end
  sorted_array
end
{% endhighlight %}

Of course, this example only addresses the test case of an array with two elements. What happens if we have more than two elements?

# A general merge sort
The divide step needs to address arrays that have 2 or more elements.
A fairly reasonable idea here is to divide the array in half.
{% highlight ruby %}
def merge_sort(a)
  return a if a.length <= 1
 
  first_half = a.first(a.length / 2)
  second_half = a.last(a.length - a.length / 2)
{% endhighlight %}

Next, we need to sort the new, smaller arrays.
This is the recursive step.

{% highlight ruby %}
  sorted_first_half = merge_sort(first_half)
  sorted_second_half = merge_sort(second_half)
{% endhighlight %}

Finally, we need to merge our two sorted arrays. This will use a soon-to-be-defined merge_step method.

{% highlight ruby %}
  merge_step(sorted_first_half, sorted_second_half)
end
{% endhighlight %}

We have yet to define a general merge_step, but the idea is similar to before. We are given two sorted lists, and we must merge them so that the result is sorted.

In our example below, we create a new array `merged = []`.
We then repeatedly compare the next unused value from our two sorted lists (`l` and `r` in this case).
Once either `l` or `r` is empty, we can just concatenate the rest of the values to the end of our `merged` array.

{% highlight ruby %}
def merge_step(l, r)
  merged = []
  while l.any? and r.any?
    if l.first < r.first
      merged << l.shift
    else
      merged << r.shift
    end
  end
  merged + l + r
end
{% endhighlight %}

A quick ruby note: `Array#shift` is a method that pulls the first element off of an array, returning the value of that element (to just look at the first element, use `Array#first`). [More details in the documentation](http://www.ruby-doc.org/core-2.0.0/Array.html#method-i-shift)

As well, `Array#<<` is equivalent to an `append` method in other languages: it adds the element to the right of the `<<` to the back end of the array. [You can read the docs here](http://www.ruby-doc.org/core-2.0.0/Array.html#method-i-3C-3C)

Finally, the complete code can be found here: https://gist.github.com/wrhall/6467755
