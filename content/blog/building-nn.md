+++
title = "Begrudgingly Trying Neural Networks"
draft = true
date = 2024-09-06
[taxonomies]
tags=["development", "rust", "ai/ml"]
categories=["development"]
+++

As my one reader knows, I've been having a little bit of an existential crisis when it comes to coding recently. I thought I might try engineering, and I still might, but there's a physical barrier to entry that kept me lazy and programming. I don't know exactly what it was, but maybe something in the air that day tasted of job security and money, so I decided to give learning AI/ML another try. 

I attended a camp a few summers back that was about building neural networks, but it was targeted for an age group that lacks the math skills fundamental to *actually* understanding neural networks. I was 15, I had no idea what a derivative was. And most of the times I had attempted to learn stuff in the field, I just ended up creating a black box that could tell me that a cat is a cat with zero understanding of how it actually works. And this didn't feel rewarding to me. I like projects where I leave them feeling as if I could do it from scratch if I needed to, and knowing the same 8 lines of python as everyone else made me feel the opposite.

This time I started from the ground up. I finished AP Calc that year so I figured my math skills would probably suffice, and I could fill in the gaps where they didn't. I started with [a series of YouTube videos](https://www.youtube.com/watch?v=aircAruvnKk&list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi) from [3Blue1Brown](https://www.youtube.com/c/3blue1brown). The series is frankly phenomenal. On top of being rich in education, it is beautifully animated and easy to understand as a result.

I finished the playlist in about 3 days and set my sights on building a model that could properly classify the images from the MNIST dataset of handwritten digits.
