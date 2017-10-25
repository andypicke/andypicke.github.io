---
layout: post
title: Clear Creek Rising: Analyzing USGS Stream Gauge Data
tags: python
---


# Clear Creek Rising: Analyzing USGS Stream Gauge Data

One of my favorite things about Golden is Clear Creek. It runs right through downtown, and is lined by walking/biking paths and parks. Most of the year it is an idyllic, slow flowing creek perfect for tubing, swimming, and letting your dog cool off. On some hot summer days, there are so many tubers it looks like a lazy river at the water park. But if you come in June, you’ll probably find more of a raging river. As the temperatures climb up into summer, all that snow in the mountains starts melting and flowing down Clear Creek into Golden. I especially love to walk by the creek during this time and watch it rise and speed up. The police usually [close the creek](http://www.denverpost.com/2017/06/12/jefferson-county-restricts-tubing-clear-creek/) to tubing or swimming during this time because it is too dangerous . The only people on the water during this period are the experience white water kayakers, and fire departments from around the area practicing [swift-water rescues](http://www.denverpost.com/2017/06/03/water-rescue-training-clear-creek/).





Normally I judge the level of the creek by how far underwater the [fish](https://2.bp.blogspot.com/-fS3L_sopcx0/TnLHnasuXMI/AAAAAAAADR8/KgFXPlom5Qg/s1600/IMGP4041.JPG) are by the Washington Ave. Bridge, but as a scientist I figured I should use something a little more accurate :). I knew that the USGS has stream gauges along the creek, so I decided to try to get the data and do some analysis. The USGS website has a bunch of data visualization tools, but I wanted to download the data programmatically so I could do my own reproducible analysis. I was happy to find that the USGS also has an API that lets you search for and download data for stream gauges across Colorado (and the country?), going back to 2007.

I started by getting data for the stream gauge in Golden, on the north side of the creek just east of the US6 bridge. If you’ve walked west on the path from Golden, you’ve probably seen it before.

## 2017

![Image description](/images/clearcreek/allyears_golden.png)
figure, add line when creek was closed?


## Going back further



## Up Next

In part 2 of this post, i’m going to look at other stream gauges on Clear Creek between Golden and the continental divide, and try to answer the following questions:

- What time of day does peak snowmelt occur?
- How long does it take for changes at the divide to propagate downstream?
- How much does streamflow vary along Clear Creek at a given time?

Stay tuned!
