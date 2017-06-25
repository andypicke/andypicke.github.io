---
layout: post
title: Simple Gradient Descent
tags: 
---

If you’re learning data science and especially machine learning, you’ve probably heard of gradient descent. When I first heard it, it sounded a bit complex and intimidating. I knew it was some sort of optimization method, but imagined it involved some pretty complicated math. However, it turns out it is actually pretty simple. The purpose of this post is to just explain the basic concept in simple terms and show you that you can easily understand it.

Gradient descent is used to solve the following problem: you have some model with multiple parameters/weights, and you need to optimize them for your dataset. This is usually expressed as a loss function that you are trying to minimize (for example in linear regression you typically minimize the sum of squared errors or some similar function). 

Gradient Descent is basically “going downhill.” The name pretty much literally translates into that; gradient is the derivative (slope) of a function, and descent means decreasing/lowering. So you are descending in the direction (actually the negative direction) of the slope; i.e. downhill!

A good way to imagine the process for a function of 2 variables (I am not the first to come up with this) is the following : You are in some area with topography , and you need to get to the lowest point. Let’s just say you are blindfolded too, so you can’t just look around and see where the lowest point is. How do you do it? You feel which way is downhill, and take a step in that direction. Then you repeat, until you reach a flat point. 

I was going to code up a simple example, but quickly found that many people had already done a good job of that. I really liked this post on medium: <https://medium.com/k-folds/diving-into-gradient-descent-9404b6150965>. I recommend actually coding up the examples yourself to get a better feel for what’s going on.