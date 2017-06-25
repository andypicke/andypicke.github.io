---
layout: post
title: Using R Manipulate for Data Exploration
tags: R
---

Recently for my research, I needed to investigate the effect of varying thresholds for several variable on the distribution of a new variable. I had been working in Matlab and had a script to plot the distributions given specified thresholds, but I was frustrated that I couldn't do this more easily. To see the effects, I would either keep changing the thresholds manually and re-executing the script, or could write a loop to cycle through values, but neither of these options seemed great. Fortunately, i've been learning R and heard about the 'manipulate' package, which seemed like the perfect solution. Manipulate (<https://support.rstudio.com/hc/en-us/articles/200551906-Interactive-Plotting-with-Manipulate>) allows you to make a plot and alter variables via a slider or checkbox; every time a variable is altered the plot is re-made. Now I could simply adjust my variables via slider bars and instantly visualize the effects on the distribution. The code example below creates a function to plot a histogram of 'gamma' which depends on the variables eps,n2,and dtdz. Only values of gamma where those variables meet a threshold criteria are plotted. Then I call manipulate to create this plot with the interactive sliders.

```
g3<-function(epsmin,n2min,dtdzmin){
        dat2<-subset(dat,log10(eps)>epsmin & log10(N2)>n2min & log10(dtdz)>dtdzmin)
        dat2<-subset(dat2,gamma>0 & gamma<2)
        hist(dat2$gamma,breaks=seq(0,2,0.025),xlim = c(0,0.3))
        abline(v=mean(dat2$gamma),col="red")
        abline(v=median(dat2$gamma),col="blue")
}

manipulate(g3(epsmin,n2min,dtdzmin),epsmin=slider(-10,-6,step=0.5),n2min=slider(-6,-1,step=0.5),dtdzmin=slider(-6,-0,step=0.5))
```

The only downside to maniuplate so far is that I haven't figured out an easy way to share it with others. Of course if someone else has the data and R, they could run the code. But i'd like to embed and share it in a R-markdown type document that doesn't require the other person to have the data or even R (since the majority of my colleagues use Matlab). It looks like this maybe can be done with a Shiny 'interactive document', or perhaps the new 'notebook' feature in Rstudio (which I haven't used yet but seems similar to the ipython/Jupyter notebooks).
