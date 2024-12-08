# 6 Lessons from doing Advent of Code in C 

As a data engineer by day, I spend a lot of time buried in Python, SQL, and
occasionally JavaScript. However, I'm also interested in many areas of tech that
don't overlap with my day-to-day. One of these areas is understanding how
computers work at a lower level of abstraction than what you would normally
think in when working with Python or JavaScript. This is what motivated me to do [advent of
code](https://adventofcode.com/) in C this year. While AoC is still ongoing (at
time of writing), I've already learned a lot and I'd like to share some of those
lessons here.

## 1. Learn the standard library 
The reason many developers do Advent of Code is to become more familiar with the
standard library for new programming languages. Here are a few functions that
showed up on my radar that have been useful in almost every AoC problem I've
solved.

- [`strtol`](https://man7.org/linux/man-pages/man3/strtol.3.html): convert strings to numbers. I missed this when I first learned C and
  built my own version of the function until ChatGPT pointed out that I was
reinventing the wheel.
- [`qsort`](https://www.man7.org/linux/man-pages/man3/qsort.3.html): similar to `strtol`, I did bubble sort from scratch for day 1 then
proceeded to find this function afterwards.
- [`errno`](https://man7.org/linux/man-pages/man3/errno.3.html): Library of error handling functions that helped me diagnose issues
when I was working with regular expressions 
- file operations: In doing AoC, I developed the boilerplate to handle command
line arguments and file operations that I may use in many future projects.
- string operations: If you take one thing away from this write up - always use
  the version of a function with an `n` when manipulating strings (e.g.
[`strnlen`](https://man7.org/linux/man-pages/man3/strnlen.3.html), not [`strlen`](https://www.man7.org/linux/man-pages/man3/strlen.3.html) and [`strncpy`](https://man7.org/linux/man-pages/man3/strncpy.3.html), not [`strcpy`](https://man7.org/linux/man-pages/man3/strcat.3.html)).
Many functions that manipulate strings assume the strings are null terminated.
If this is not the case, then you may be vulnerable to a buffer overflow. The `n`-version of these functions are safer as they are guaranteed stopping after a set number of characters.

## 2. Building my own 2d array
Experienced C developers often maintain their own library of frequently used
functions. I started mine with my own 2d array which you can
find in the [utils file](https://github.com/apalermo01/Advent-of-Code/blob/main/2024/day6/C/utils.c) that I recently started attaching to all of my solutions.
I set it up by allocating a contiguous block of memory and using the formula
`(numcols * row) + col` to access and modify individual elements. I have seen other
versions of this that use an array of pointers to other integer arrays that
allow indexing values the same way that you would index Python arrays (i.e.
`arr[3][4]` instead of `get_value(array, row, col)`). I plan to explore this
approach next time I overhaul this utility.


## 3. Regular expressions

I found that setting up [regular expressions](https://linux.die.net/man/3/regexec) in C was particularly challenging on
my first attempt. This is a step-by-step guide for using regular expressions in
C that I found helpful.

1. Allocate memory for:
    i. The compiled regular expression (`regex_t reg;`)
    ii. A copy of the line you want to match on (`char* s = pointer_to_string;`)
    iii. A buffer containing the pattern to match (`const char* re = "([0-9]+);`)
    iv. A regmatch struct to hold the matches (`regmatch_t regmatch;`)
2. Run regcomp to compile the regular expression
3. Run regexec to store a group of matches in regmatch 
4. Process those matches 
5. Advance the pointer to the start of your string (`char* s`) just past this
   match so that regexec gets the next match when you run it again (e.g. `s +=
regmatch.rm_eo`) (regexec only gives back the first match).

I will provide a more detailed explanation in a future writeup since it will
make this one too long.

There are 2 other issues that I ran into:
1. POSIX regex does not support many of the escape characters that many of the
   other variants have. For example, instead of `\d+`, I had to use `[0-9]+` to
   match integers
2. I've found that it's best to use the `REG_EXTENDED` flag when running
   `regcomp` by default. I have not looked into what exactly the extended
variant has that the basic syntax does not, but most of my use cases have
required the extended variant.


## 4. Test cases test cases test cases!
If you ever find yourself stuck on an AoC problem and you're out of ideas - the
first step should be writing your own test cases. while I still have
not
solved [day 4](https://adventofcode.com/2024/day/4) - writing my own test cases has uncovered a ton of issues that I
missed. These were not caught by the sample input - including a bug or two in my 2d
array implementation. You might resist this at first because you feel like it
would take too long, but trust me, it is worth it.

## 5. It doesn't have to be pretty
Keeping up with Advent of Code means solving 50 programming challenges in 25
days. This is on top of our full-time jobs, family obligations, and other hobbies. If you
can get it right, don't feel bad about brute-forcing a solution. You don't need
to spend the extra time to find the most optimal solution right now. You have
the next 11 months to revisit your solutions if you would like to refine them
later.

## 6. Use AI for code Reviews
LLMs have been a surprisingly valuable resource for getting feedback for my
solutions, especially as a beginner to C. It has found the occasional memory leak
(but don't expect it to find all memory issues) and highlighted useful functions
in the standard library that I tried to implement myself (strtol and qsort).
While you should absolutely not trust what an LLM tells you at face value, it is
great at giving a direction for further study and identifying which parts of
your solution could use refinement.

## Conclusion

Spending my December mornings working through Advent of Code problems has taught
me so much about how to think about problems without the handy abstractions that
I have relied on in Python. After just 8 days, I have already seen changes
in how I approach other kinds of problems. I hope you found the tips above
valuable if you are also new to C. Thanks for reading!
