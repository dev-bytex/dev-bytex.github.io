+++
title = "Quick Sort"
date = "2026-08-27"
+++

# Quick Sort

A few months ago, I was replicating the Linux `ls` command in Go. Part of the task was implementing `ls -t`, which sorts directory contents by creation time, newest first. I was restricted from using Go's built-in sort function, so I had to write my own. That's where I came across Quick Sort, and it's been a useful weapon ever since. In this article, I'll hand it over to you (use responsibly).

In my previous post, I wrote about Merge Sort. Quick Sort covers similar ground but trades Merge Sort's guaranteed O(n log n) and extra memory usage for something that runs faster in practice and sorts in place. Different tools for different situations, and that's why knowing both matters.

Quick Sort is one of the most used sorting algorithms because of how fast it is and how easy it is to understand. It uses the divide and conquer method: you divide the items into smaller sections until you hit the base case, then work your way back up. It's faster than algorithms like Selection Sort. The way it works is that for a given list of items, you pick a pivot, just one item you use as a reference point. The idea is simple: any item smaller than the pivot belongs to its left, and any item larger belongs to its right. Once you place the pivot in that position, it's done and will never move again. You then do the same thing recursively on each side until every item has been placed. That's the whole trick: each step permanently places one item, and the rest falls into order around it.

## How It Works

The key idea is the pivot. You pick one item from the list and use it as a reference point, then rearrange everything so that smaller items are on its left and larger items are on its right. You don't care about the exact order on either side yet, you just care about which side things end up on. Then you place the pivot in its final position and do the same thing recursively on each side. Unlike Bubble Sort or Selection Sort, you're not comparing every item against every other item repeatedly; each pass through the list is doing real, permanent work. That's what makes it fast.

The only thing you need before any of that is a base case, so the recursion knows when to stop. A list with 0 or 1 items is already sorted, so that's your exit condition.

## Analogy

That might still sound abstract, so let's walk through a real example step by step.

Let's say we have an array of numbers like `[4, 1, 3, 5, 2]` and we need to sort it using Quick Sort.

**Steps:**

**I.** Set a base case so we don't hit infinite recursion. We know that a list with 0 or 1 items is already sorted, so the base case is when the number of items is less than 2.

**II.** Pick a pivot as a reference item. There are different ways to do this. You can pick from anywhere in the list, as long as you put it back in its correct position afterward. Here we'll pick the middle item, which is `3`.

**III.** To make things easier, move the pivot to the end of the list so we can place it back precisely after partitioning. The list becomes `[4, 1, 5, 2, 3]`.

**IV.** Now move all numbers smaller than the pivot to the left side. We use a cursor `i`, starting at 0, to track how many smaller numbers we've placed:

- Is 4 less than 3? No. Do nothing.
- Is 1 less than 3? Yes. Swap it with the item at position `i` (which is 4), then increment `i` by 1. List becomes `[1, 4, 5, 2, 3]`.
- Is 5 less than 3? No. Do nothing.
- Is 2 less than 3? Yes. Swap it with the item at position `i` (which is 4), then increment `i` by 1. List becomes `[1, 2, 5, 4, 3]`.
- Is 3 less than 3? No, it's equal. Do nothing.
- End of list. We have `[1, 2, 5, 4, 3]`.

**V.** Move the pivot back to its correct position, which is right after the last small number, at position `i`. Since 5 is there, we swap it with the pivot. The list becomes `[1, 2, 3, 4, 5]`. But wait, we have a sorted list already, does the algorithm stop here? No. We won't always get a sorted list after this step, and the computer doesn't know when the list is sorted; it just follows our instructions.

**VI.** Recursively sort the left side of the pivot (`[1, 2]`) and the right side (`[4, 5]`), repeating from step I until we hit the base case.

**VII.** After all that, we have a perfectly sorted list.

## Another visual example

<img src="https://i0.wp.com/techiedelight.com/wp-content/uploads/Quicksort.png?w=640&ssl=1" alt="another example" style="background-color: #f8f9fa; padding: 1rem; border-radius: 3px; width: 100%; max-width: 600px; display: block; margin: 0 auto;"/>
<p style="text-align: center;">Source: https://www.techiedelight.com/es/quicksort</p>

## Implementation in Zig
``` zig, linenos
const std = @import("std");
const Io = std.Io;

pub fn quickSort(
    comptime T: type,
    arr: []T,
    lessThan: fn (T, T) bool,
) !void {
    // base case
    if (arr.len < 2) {
        return;
    }

    // choosing a pivot, this place we will choose the middle
    const mid = arr.len / 2;
    // swap the pivot with the last item in the list
    const arr_len = arr.len - 1;
    std.mem.swap(T, &arr[mid], &arr[arr_len]);
    const pivot = arr[arr_len];

    // everything that is less than the pivot should be at the left side of the pivot
    // and everything greater should be at the right
    var i: usize = 0; // this acts like a cursor keeping tracking of the amount of
    // left hand side items
    for (arr, 0..) |value, j| {
        if (lessThan(value, pivot)) {
            std.mem.swap(T, &arr[i], &arr[j]);
            i += 1;
        }
    }

    // return the pivot to its position
    std.mem.swap(T, &arr[i], &arr[arr_len]);

    // recursively sort the left side
    try quickSort(T, arr[0..i], lessThan);

    // recursively sort the right side
    try quickSort(T, arr[i + 1 ..], lessThan);
}
```
## How Fast Is It?

The answer depends on the size of the list and the pivot chosen. If you read the Merge Sort post, you already know what O(n log n) means. Quick Sort shares that average case. But unlike Merge Sort, it can go as bad as O(n²). That worst case doesn't happen often, but we can't guarantee which pivot is perfect for a given list, especially if we have a billion items to sort and can't inspect them all first. That's why picking the middle item as the pivot is a safe default: you're less likely to hit the worst case consistently.

## Implementing `ls -t` Using Quick Sort

Now you have the tool. The challenge: implement `ls -t` yourself using Quick Sort. Start with just the `-t` flag, get it working, then go further if you want. I'll write a full guide on implementing it in Zig at some point, but try it first before reading anyone else's solution.

Until next time, thanks for reading.
