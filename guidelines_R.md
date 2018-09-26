---
title: "Code"
author:
- Bram Burger \and \
- Luis Francisco Hernández Sánchez
date: "`r format(Sys.time(), '%d %B, %Y')`"
fontsize: 12pt
graphics: yes
output: html
---


The starting point is the Bioconductor[^<https://www.bioconductor.org/developers/how-to/coding-style/>] guidelines, and the Rchaeology paper[^<https://cran.r-project.org/web/packages/rockchalk/vignettes/Rstyle.pdf>].

# R Internals

Firstly, let's ask the obvious question: What does R say themselves?
From R Internals[^<https://cran.r-project.org/doc/manuals/R-ints.html#R-coding-standards>]:
    
    > [...] R is a GNU project, and the spirit of the GNU Coding Standards should be followed if possible.
    
But immediately they also throw an exception to that rule at you (see Indentation), so let's not go there too enthousiastically.

# Indentation

    Use 4 spaces for indenting. No tabs.ee 

I think the no tabs is general for every programming language?

I know that Google and Wickham use 2 spaces for indentation, but I have two counter arguments:

1. The use of 4 spaces is about the only thing that is consistent in the R source. Quoted from Rchaeology:

    > This is explicitly spelled out in the R documentation. No tabs! Insert 4 blank spaces.
    
    And to quote R Internals:

    > The R Core Team recommends to use a basic indentation of 4 for R and C (and most likely also Perl) code, and 2 for documentation in Rd format.

2. There are two more ways in which you get indentation: Overflowing argument lists of functions (align those so they fall just inside the opening parenthesis and don't start the new line with a comma) and constructions as ```apply``` family where you write functions inside a function call. After a couple lines you tend to need those extra spaces to make it easy to distinguish between the function itself and function calls within the function.

# Line length

    No lines longer than 80 characters.

This is not only from Bioconductor, but a general thing. Even though some people try to stretch it.

I would really prefer 70 instead of 80.
With 80 characters on a line, I have to start to scroll horizontally even on the big screen.
I know this is kind of crappy, as with four levels of indentation you start at column ... whatever, but then again, if you have four levels of indentation you need a couple helper functions anyway.
Also, sometimes breaking a couple nested function calls to a new line can give you some indentation back again. I haven't figured out exactly how and when, so the only thing I can say is "ESS usually does things in a reasonable way". (which I guess is not helpful advice for you)


# Variable names and Function names

    Use camelCaps: initial lowercase, then alternate case between words.

This comes from Bioconductor again.
This seems to be what the masses do as well[^<https://journal.r-project.org/archive/2012-2/RJournal_2012-2_Baaaath.pdf>]:
Although, there seems to be a division of opinions looking at Rchaeology. (still, you're in good company by using camelCase)

Do not use underscores.
Underscores are evil. (and an old assignment operator from the early days)

    Do not use '.' (in the S3 class system, some(x) where x is class A will dispatch to some.A).
    Prefix non-exported functions with a '.'.
    
These are also from Bioconductor, but Rchaeology has a similar warning.
The prefix is mainly important when writing packages. Which, I guess, is a bit overkill for our short scripts.

Some more from Rchaeology:

> Avoid declaring variables that have the same names as widely used functions.

> Use long names for infrequently used variables
> 
> If we are going to use a name 50 times in a 5 line program, we should choose a short one. 
>
> For abbreviations, include a comment to remind the reader what the thing stands for.

Related: Ideally everything should be a package. Unfortunately... I'm afraid that might be a bit much for our purposes, and then again you have the Hadley Wickham way of doing things versus the base R way of doing things, which might cause a little confusion in the beginning. I do think that Wickham has a good system for making skeletons for packages, so if we do decide to go the way of making everything into a package, I think it makes sense to start there.

# Class names

    Use CamelCaps: initial upper case, then alternate case between words.

This comes from Bioconductor.

I disagree. R itself doesn't use a first capital for classes. (better to just not use classes, perhaps? It's a whole new can of worms which wiggle different than the Java and Cpp versions...)

# File names

    Filename extension for R code should be '.R'. Use the prefix 'methods-' for S4 class methods, e.g., 'methods-coverage.R'. Generic definitions can be listed in a single file, 'AllGenerics.R', and class definitions in 'AllClasses.R'.
    Filename extension for man pages should be '.Rd'.

Again, from Bioconductor.

As an aside: don't write man pages manually, use Roxygen as we'll touch upon at the Comments section.

# Use of space

    Always use space after a comma. This: a, b, c.
    No space around "=" when using named arguments to functions. This: somefunc(a=1, b=2)
    Space around all binary operators: a == b.

This is from Bioconductor and is not detailed enough. When/where to put spaces around parentheses, curly brackets, etc.?

Better to follow Rchaeology:

> This is a general GNU coding standard.
> 
> 1.  Insert spaces before and after
>     a)  mathematical symbols like: "=", "<-", "<", "*", "+"
>     b)  R binary operations like: "%*%", "%o%", and "%in%".
> 2.  Put one space after commas.
> 3.  Insert one space before the opening squiggly braces "{".
> 4.  Put one space after the closing parenthesis ")" and the closing squiggly brace "}".
>
> This is purely a matter of convention and legibility, it does not affect the “rightness” of code.

I tend to limit spaces after the ) and } at the end of applys, especially if I can make nice functional blocks of them indication different applys. But, of course, those should become a couple of short functions instead...

> Other observations about spaces,
> 
> 1.  Do not insert spaces between function names and their opening parentheses.

Point 2 indicates that ```if``` and ```for``` are not functions, but keywords. So do put a space after those. (```while```, ```function```, and ```repeat``` as well apparently)

> 3. Do not insert “extra spaces” inside parentheses.

> The insertion of (the) interior parentheses for the smaller conditions inside (the) [an] if statement is consistent with the GNU standard for C.

Also better for me, because I am terrible at remembering operator precedence!

As for point two from Bioconductor about no spaces around "=":

> Is there an “argument exception” to the space rule for equal signs?
>
> Package writers are not entirely consistent, and Rchaeologically speaking, we cannot be sure if these variations are accidental.
>
> Spaces  may  sometimes  be  omitted  in  an  effort  to  keep  code  on  one  line. [...] Please note, however, that it is NEVER acceptable to omit the spaces after commas!

When you have many arguments in a function, break them and indent to the parenthesis.
You can also put "function" on a new line, in those cases where you have lots of arguments, and then indent it as usual:

```
    myFun <-
        function (tree, k=NULL, which=NULL,
                  x=NULL, h=NULL, border=2, cluster=NULL) {
                  tree
    }
```

Note that in this case the opening curly bracket is not on its own line. In the internal R code sometimes it is and sometimes not, and apparently there is no strict large majority guide in this. (I would follow the way it's done above.)

I don't know whether there should be a space between ```function``` and its opening parenthesis. It is a keyword, so given the above there should be a space just as with ```for``` and ```if```, but usually it seems not to have one, but ```lm``` does, for example, and I like it personally. Unfortunately it seems that (in a very small sample of R code) this does not happen often. So probably better to not do that.

Also note that I don't use "return". That is only used when you exit early. The last evaluated thing is automatically "returned".

# Comments

From Bioconductor:

    Use "##" to start full-line comments.
    Indent at the same level as surrounding code.

But the ESS style is a bit nicer:

    Put a space after the "#" when you write comments
    A single "#" is an end-of-line comment (after code)
    On it's own line a "# comment" is indented at column 40
    "##" is a comment indented at the same level as the code
    "###" comments are indented to the left margin

For documentation use Roxygen[^<http://roxygen.org/roxygen.pdf>].

# Namespaces

Following Bioconductor: 

    Import all symbols used from packages other than "base". Except for default packages (base, graphics, stats, etc.) or when overly tedious, fully enumerate imports.
    Export all symbols useful to end users. Fully enumerate exports.
    
This applies mostly/only when writing packages.

People more and more (according to Rchaeology) completely write lme4::lmer(...) etc, instead of just lmer(...).

# End-User messages

Bioconductor says: 

    message() communicates diagnostic messages (e.g., progress during lengthy computations) during code evaluation.
    warning() communicates unusual situations handled by your code.
    stop() indicates an error condition.
    cat() or print() are used only when displaying an object to the user, e.g., in a show method.
    
Make sure the message is useful.
Don't print indices in a loop, as the printing itself probably takes longer than the rest of the iteration.

# Assignment

Not just Bioconductor, but we copied from there:

    Use <- not = for assignment.

From Rchaeology: If "=" works for assignment then that is ... luck: 

> The equal sign was used by mistake so frequently that the R system was re-designed to tolerate that mistake. Most usages of the equal sign for assignments do not cause runtime errors. Not all possible problems were eliminated, however

# If - else

You can omit curly braces in very short statements, if they are (pretty much) the only thing in a function. Otherwise do use them.

When you use curly braces the else should look like

    } else {

The reason else is not allowed on it's own line is that that way you can't run the code line by line. (Rchaeology figured out the exception: when you enclose the whole thing in a curly-brackets pair)

# Booleans

Never name a variable T or F. That is too easily confused with (and unfortunately used as)  ```TRUE``` and ```FALSE```.

And please always write ```TRUE``` and ```FALSE``` instead of just the one letter.


# Some common functions

R has some functions, which seem to be less-widely known, that make oft-used expressions short and clear.
I don't know all of them, but let's make a start.

Also look at the help for these functions, as they give more options.


## Checking for emptiness

    is.na(x)

Checks whether something is ```NA```. Bear in mind that not all NA's are the same kind of ```NA```. (This might give some trouble when assigning NA to a character vector instead of a numeric vector)

    is.null(x)
    
Checks whether something is ```NULL```.
For example:

    > a <- list(one = c(1,2,3), two = c(11,22,33))
    > is.null(a$three)
    [1] TRUE
    
As ```a$three``` is nothing.
Testing for ```NA``` gives:

    > is.na(a$three)
    logical(0)
    
Because you're testing whether 'nothing' is ```NA```. So you get a logical vector of length 0. Which you can't test with the above functions:

    > is.null(logical(0))
    [1] FALSE
    > is.na(logical(0))
    logical(0)
    
So instead:

    > length(logical(0))
    0

Seems to be the appropriate test.


## Subsetting

If you have a matrix or data frame ```df```

    df[, 1]               # results in a vector
    df[, 1, drop = FALSE] # results in a data frame with one column
    
## Sequences

When you want to use a for loop over indices

    food <- c("cake", "buns", "pie")
    for (i in seq_along(food)) {
        ...
    }
    bigMat <- matrix(rnorm(1000), ncol=20)
    for (i in seq_len(nrow(bigMat))) {
        ...
    }
    

## Loops

Are sometimes nicer and clearer with an appropriate ```[l|s]apply```.
Usually not faster than a good for-loop, though. So use only to make things clearer.

## With

If you have a data frame, you can use ```with(df, ...)``` to avoid having to subset the dataset every time.

## Finding maximum index

```which.max()```

## Growing objects

Do not.

If you have to grow objects, apparently doing it per column is faster. (so add columns to a matrix and then transpose, for example, if you want to grow rows)

If you know how many elements you are going to store, pre-allocate!
Otherwise, every assignment will make a copy of the object before replacing it with the extended object.

    retVal <- rep(NA, 10)
    for (i in seq_along(retVal)) {
        retVal[i] <- rnorm(1)
    }
    ## OK, that one should be vectorised:
    ## retVal <- rnorm(10)
    ## but you see the idea
    
That's why ```rbind()``` and such are s.l.o.w.

## Data Frames

There are a couple ways of using data frames.
1. Using data.frame
2. Using tibbles (from tidyverse)
3. Using data.table

If you like SQL data.table might be very intuitive.
Data.table can also be quite a bit faster than the other two, especially when growing them.
One thing that is a good/bad thing, is that you can alter elements in place (without having to copy the whole data frame).

I don't have a huge preference, but choice depends on usage.
Sometimes you don't really need the speed, but do need clarity. In that case tibbles might be a little nicer.
Sometimes the base data frames are completely fine and sufficient, not need for fancy stuff.
Sometimes you need the speed, or the expressiveness of a data.table.

They all have their place, but we should be consistent per application.
(I'm looking sternly at myself, here :-) )

