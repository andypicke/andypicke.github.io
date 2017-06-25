---
layout: post
title: Blind Monkeys?
---

I was recently reading the book "Black box thinking" by Matthew Syed (<http://www.matthewsyed.co.uk/books/>), and came across an interesting example he quotes illustrating the power of natural selection. I thought it'd be fun to code up in R and play around, so here's the results. Try it out for yourself: <https://github.com/andypicke/BlindMonkey/blob/master/BlindMonkey.md>.



Below is some R code to simulate this scenario. We start with a specified phrase, randomly generate the same number of letters. Then we keep the letters that are correct (this is our 'natural selection'), and keep repeating this process until we get the exact phrase. Try it out with a word or phrase of your choice! 

```r
rm(list=ls())
thePhrase<-c("c","h","e","e","s","e")#"Cheese"
Nchar<-length(thePhrase)
    # generate a random sample
    b<-sample(letters,Nchar)
    # find which characters are correct
    ig<-which(thePhrase==b)
    
    #ig<-0
    n_iter<-1
    while (length(ig)<Nchar){
        # generate a random sample
        b<-sample(letters,Nchar)
        # keep the correct letters
        b[ig]=thePhrase[ig]
        # find which characters are correct
        ig<-which(thePhrase==b)
        print(b)
        n_iter<-n_iter+1
    }
```

```
## [1] "g" "l" "f" "a" "j" "c"
## [1] "e" "u" "k" "o" "c" "v"
## [1] "v" "l" "w" "m" "g" "o"
## [1] "c" "n" "f" "g" "k" "t"
## [1] "c" "p" "f" "b" "l" "e"
## [1] "c" "z" "r" "n" "x" "e"
## [1] "c" "n" "z" "l" "o" "e"
## [1] "c" "w" "j" "a" "u" "e"
## [1] "c" "f" "v" "r" "i" "e"
## [1] "c" "x" "l" "t" "a" "e"
## [1] "c" "n" "s" "g" "x" "e"
## [1] "c" "s" "f" "u" "l" "e"
## [1] "c" "a" "b" "f" "s" "e"
## [1] "c" "l" "v" "n" "s" "e"
## [1] "c" "d" "b" "t" "s" "e"
## [1] "c" "n" "y" "i" "s" "e"
## [1] "c" "i" "y" "w" "s" "e"
## [1] "c" "k" "m" "j" "s" "e"
## [1] "c" "d" "y" "j" "s" "e"
## [1] "c" "m" "l" "z" "s" "e"
## [1] "c" "z" "f" "j" "s" "e"
## [1] "c" "z" "d" "j" "s" "e"
## [1] "c" "n" "y" "h" "s" "e"
## [1] "c" "e" "n" "y" "s" "e"
## [1] "c" "i" "h" "l" "s" "e"
## [1] "c" "c" "u" "n" "s" "e"
## [1] "c" "l" "x" "n" "s" "e"
## [1] "c" "p" "g" "n" "s" "e"
## [1] "c" "t" "d" "l" "s" "e"
## [1] "c" "r" "g" "d" "s" "e"
## [1] "c" "y" "l" "u" "s" "e"
## [1] "c" "z" "b" "s" "s" "e"
## [1] "c" "g" "x" "l" "s" "e"
## [1] "c" "m" "p" "s" "s" "e"
## [1] "c" "n" "s" "j" "s" "e"
## [1] "c" "r" "f" "c" "s" "e"
## [1] "c" "k" "e" "s" "s" "e"
## [1] "c" "h" "e" "u" "s" "e"
## [1] "c" "h" "e" "r" "s" "e"
## [1] "c" "h" "e" "v" "s" "e"
## [1] "c" "h" "e" "a" "s" "e"
## [1] "c" "h" "e" "g" "s" "e"
## [1] "c" "h" "e" "a" "s" "e"
## [1] "c" "h" "e" "i" "s" "e"
## [1] "c" "h" "e" "m" "s" "e"
## [1] "c" "h" "e" "v" "s" "e"
## [1] "c" "h" "e" "p" "s" "e"
## [1] "c" "h" "e" "m" "s" "e"
## [1] "c" "h" "e" "j" "s" "e"
## [1] "c" "h" "e" "t" "s" "e"
## [1] "c" "h" "e" "r" "s" "e"
## [1] "c" "h" "e" "v" "s" "e"
## [1] "c" "h" "e" "s" "s" "e"
## [1] "c" "h" "e" "f" "s" "e"
## [1] "c" "h" "e" "u" "s" "e"
## [1] "c" "h" "e" "i" "s" "e"
## [1] "c" "h" "e" "l" "s" "e"
## [1] "c" "h" "e" "c" "s" "e"
## [1] "c" "h" "e" "k" "s" "e"
## [1] "c" "h" "e" "j" "s" "e"
## [1] "c" "h" "e" "j" "s" "e"
## [1] "c" "h" "e" "e" "s" "e"
```

```r
    # How many iterations ('generations') did it take?
    print(n_iter)
```

```
## [1] 63
```

```r
    # Print the final result
    print(b)
```

```
## [1] "c" "h" "e" "e" "s" "e"
```




