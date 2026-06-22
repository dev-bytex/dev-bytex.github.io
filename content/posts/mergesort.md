+++
title = "Merge Sort"
date = "2026-06-23"
+++

# Sorting Algorithm: Merge Sort

You are in the middle of building a platform for a school; you have built the UI, the database, and the result manager. But wait — there's a problem. What if you want to know which student did best in a particular class, when the average class has around 100 students? You can't just do that manually by hand. Apart from being stressful, you'd have to rearrange it every session for every class — let's say about 30 classes across the whole school. That would be really hectic.

So, what's the solution? Sorting algorithms. That's it. But we can't just pick any sorting algorithm and call it a day — we have to consider conditions like speed, efficiency, memory usage, and so on. That's why computer scientists and software engineers have developed various algorithms over the years. Before we dive deeper, let's define what it means to sort.

Sorting is the process of arranging `comparable` items in ascending or descending order. Notice the word "comparable" — that's deliberate, because we can't arrange items that aren't. If there's no way to compare two items, there's no way to sort them. We can sort a list of numbers because numbers are comparable — one is either bigger or smaller than another. We can sort by time for the same reason — one moment comes before or after another. This matters beyond plain numbers too: comparability isn't about the *type* of data, it's about whether you can define an ordering on it — which is exactly what will later let us sort students by score instead of just sorting numbers.

Several sorting algorithms have been developed over the years for different purposes — some are extremely slow and inefficient, others are fast and efficient. Examples include bubble sort, insertion sort, quick sort, merge sort, selection sort, and more. We'll cover most of them in upcoming articles, all implemented in Zig. We'll start with one of the most widely used, merge sort, because it's fast, and a good place to begin.

## Merge Sort

As the name implies, merge sort works by merging items. But we can't merge items that are already together. That means we first need to recursively break the list into halves until we can't break it down any further, and only then merge them back. Merging isn't just blindly joining items together — that would leave them unsorted, no different from the input. Merging works by comparing items to see which is smaller, and pushing it into its correct place in the resulting array.

### The Breakdown

That definition might sound complex, but it's genuinely simple once broken into pieces. Merge sort does two things:

- Recursively divide the items into halves until the list can no longer be divided (i.e., when its length is 1). If the list has an odd number of items, one side ends up with one more item than the other — this is fine, since merging only requires that both halves are individually sorted, not that they're the same size.
- Merge the divided items back together. While merging, items are compared based on whatever property you want them ordered by — time, length, size, or anything else that's comparable.

Once those two ideas are clear, the steps below should be easy to follow.

### Steps

1. Create a function — call it `mergeSort` — that takes a list of items as an argument. Its job is to recursively split the list.
2. Check if the list has 0 or 1 items. If so, it's already sorted — return it immediately. This also prevents the recursion from running forever.
3. Find the midpoint of the list. If the length is odd, round down (e.g., 5 / 2 = 2, not 2.5).
4. Split the list into left and right halves using that midpoint. The left half contains everything from the start up to the midpoint; the right half contains everything from the midpoint to the end.
5. Call `mergeSort` recursively on the left half, then on the right half.
6. Create another function — call it `merge` — that takes the left and right (now-sorted) lists and combines them.
7. Inside `merge`, create three variables, `i`, `j`, and `k`, all starting at 0. `i` tracks the current position in the left list, `j` tracks the current position in the right list, and `k` tracks the current position in the result list.
8. Compare the item at position `i` in the left list with the item at position `j` in the right list. If the left item is smaller, place it at position `k` in the result and increment `i` and `k`. Otherwise, place the right item at position `k` and increment `j` and `k`.
9. Once one list is exhausted, copy any remaining items from the other list directly into the result.
10. Back in `mergeSort`, call `merge` on the sorted left and right halves to combine them.
11. The result is the fully sorted list.

``` zig, linenos
const std = @import("std");

// Step 6: Prepare the function merging function
fn merge(
    comptime T: type,
    arr: []T,
    left: []T,
    right: []T,
) !void {
    // Step 7: Initialising i, j, and k
    var i: usize = 0;
    var j: usize = 0;
    var k: usize = 0;

    while (i < left.len and j < right.len) {
        // Step 8a: Checking if ith item of the left array is greater the jth
        // item of right array
        if (left[i] < right[j]) {
            // Step 8b: Inserting the item from the left array in the kth 
            // position in the holder array
            arr[k] = left[i];
            // Step 8c: Increasing i by 1
            i += 1;
        } else {
            // Step 8d: If the above condition is false, the jth position of the right 
            // array is smaller the it will be inserted in the kth position of the 
            // holder array
            arr[k] = right[j];

            // Step 8e: Increasing j by 1
            j += 1;
        }

        // Step 8f: Increasing k by 1
        k += 1;
    }

    // Step 9: Sweep the leftovers.
    while (i < left.len) : (i += 1) {
        arr[k] = left[i];
        k += 1;
    }

    // Step 9b: Still Sweeping the leftovers.
    while (j < right.len) : (j += 1) {
        arr[k] = right[j];
        k += 1;
    }
}

// Step 1: Creating the function
pub fn mergeSort(
    // comptime is used here so that we can tell the compiler what the type of items 
    // we are sorting which gives us the ability to sorting any list of any type 
    // as far as it is comparable.
    comptime T: type,
    arr: []T,
    allocator: std.mem.Allocator,
) !void {
    // Step 2: The Base Case(The stopper)
    if (arr.len <= 1) return;

    // Step 3: Find the middle
    const mid = arr.len / 2;

    // Step 4a: Split the list into two halves, the left
    const left = try allocator.dupe(T, arr[0..mid]);
    defer allocator.free(left);

    // Step 4b: The right
    const right = try allocator.dupe(T, arr[mid..]);
    defer allocator.free(right);

    // Step 5a: Left side calling
    try mergeSort(T, left, allocator);

    // Step 5a: Right side calling
    try mergeSort(T, right, allocator);

    // Step 10: Calling the merge function
    try merge(T, arr, left, right);
}
```

### How Fast Is It?

Speed is measured using something called Big O notation — a way of describing how an algorithm's running time grows as the number of items it's processing grows. You'll see notations like O(n) or O(n²); I'll write a dedicated article on Big O soon, so stay tuned. For merge sort, the complexity is **O(n log n)**, meaning the time grows in proportion to n multiplied by the logarithm of n — which scales far better than something like O(n²) as your data grows.

Here's the intuition. Picture the recursion as a tree: the root is the full list of n items, the next level splits it into two halves of n/2, the next into four quarters of n/4, and so on, down to lists of size 1.

- **The "log n" part** comes from how many levels that tree has. Each level halves the list, and the number of times you can halve n before reaching 1 is exactly log₂(n). So there are log n levels.
- **The "n" part** comes from the work done at each level. At any single level, no matter how many pieces the list has been split into, merging them back together still touches every one of the n original items exactly once. So each level costs n work.

Multiply the two together — log n levels, each costing n work — and you get O(n log n).

<img src="https://www.programiz.com/sites/tutorial2program/files/merge-sort-example_0.png" alt="My Diagram" style="background-color: #f8f9fa; padding: 1rem; border-radius: 8px; width: 50%; max-width: 400px; display: block; margin: 0 auto;"/>
<p style="text-align: center; padding-bottom: 4px">Source: Programiz pro</p>


<img src="https://cdn.programiz.pro/programiz-static/programiz-blog/dsa-blog-time-complexity-merge-sort.png" alt="My Diagram" style="background-color: #f8f9fa; padding: 1rem; border-radius: 8px; width: 50%; max-width: 400px; display: block; margin: 0 auto;"/>
<p style="text-align: center;">Source: Programiz pro</p>



## Back to Our Initial Problem

Now that we have the tool to solve our sorting problem, how do we apply it?

``` zig, linenos
// root.zig
const std = @import("std");

fn merge(
    comptime T: type,
    arr: []T,
    left: []T,
    right: []T,
    lessThan: fn (T, T) bool,
) !void {
    var i: usize = 0;
    var j: usize = 0;
    var k: usize = 0;

    while (i < left.len and j < right.len) {
        if (lessThan(left[i], right[j])) {
            arr[k] = left[i];
            i += 1;
        } else {
            arr[k] = right[j];
            j += 1;
        }
        k += 1;
    }

    while (i < left.len) : (i += 1) {
        arr[k] = left[i];
        k += 1;
    }

    while (j < right.len) : (j += 1) {
        arr[k] = right[j];
        k += 1;
    }
}

pub fn mergeSort(
    comptime T: type,
    arr: []T,
    allocator: std.mem.Allocator,
    lessThan: fn (T, T) bool,
) !void {
    if (arr.len <= 1) return;

    const mid = arr.len / 2;

    const left = try allocator.dupe(T, arr[0..mid]);
    defer allocator.free(left);

    const right = try allocator.dupe(T, arr[mid..]);
    defer allocator.free(right);

    try mergeSort(T, left, allocator, lessThan);
    try mergeSort(T, right, allocator, lessThan);
    try merge(T, arr, left, right, lessThan);
}
```
Now, let's test it!
``` zig, linenos
// main.zig
const std = @import("std");
const root = @import("root.zig");

fn studentLessThan(a: Student, b: Student) bool {
    return a.score < b.score;
}

const Student = struct {
    name: []const u8,
    score: u16,
};

// This is used to reverse the order of the list
fn reverseList(comptime T: type, arr: []T) !void {
    var j: usize = arr.len - 1;
    var i: usize = 0;

    while (i < j) {
        const temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;

        i += 1;
        j -= 1;
    }
}


pub fn main() !void {
    var gpa = std.heap.DebugAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();

    const student1 = Student{ .name = "John", .score = 78 };
    const student2 = Student{ .name = "Ben", .score = 82 };
    const student3 = Student{ .name = "Mike", .score = 69 };
    const student4 = Student{ .name = "Sam", .score = 70 };
    const student5 = Student{ .name = "Cynthia", .score = 68 };
    const student6 = Student{ .name = "Saheed", .score = 60 };

    var students = [_]Student{ student1, student2, student3, student4, student5, student6 };
    std.debug.print("\n------ Before sorting ------\n", .{});
    for (students) |student| {
        std.debug.print("Name: {s: <15} | Score: {d: >3}\n", 
        .{ student.name, student.score });
    }

    try root.mergeSort(Student, &students, allocator, studentLessThan);
    std.debug.print("\n------ After sorting (Ascending) ------\n", .{});
    for (students) |student| {
        std.debug.print("Name: {s: <15} | Score: {d: >3}\n",
         .{ student.name, student.score });
    }

    std.debug.print("\n------ After sorting (Descending) ------\n", .{});
    try reverseList(Student, &students);
    for (students) |student| {
        std.debug.print("Name: {s: <15} | Score: {d: >3}\n", 
        .{ student.name, student.score });
    }
}

```

Result
```text
$ zig run main.zig

------ Before sorting ------
Name: John            | Score:  78
Name: Ben             | Score:  82
Name: Mike            | Score:  69
Name: Sam             | Score:  70
Name: Cynthia         | Score:  68
Name: Saheed          | Score:  60

------ After sorting (Ascending) ------
Name: Saheed          | Score:  60
Name: Cynthia         | Score:  68
Name: Mike            | Score:  69
Name: Sam             | Score:  70
Name: John            | Score:  78
Name: Ben             | Score:  82

------ After sorting (Descending) ------
Name: Ben             | Score:  82
Name: John            | Score:  78
Name: Sam             | Score:  70
Name: Mike            | Score:  69
Name: Cynthia         | Score:  68
Name: Saheed          | Score:  60

```

## Conclusion

At the start of this article, we had a sorting problem that needed urgent attention. We covered what it means to sort, looked at the range of sorting algorithms out there, and focused mainly on merge sort — how it works, why it's correct, and why it runs in O(n log n) time. Then we applied it to our original problem to get our result. In the next post, we'll look at another sorting algorithm.

Thanks for reading.
