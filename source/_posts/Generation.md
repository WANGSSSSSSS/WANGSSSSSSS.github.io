---
title: Scene Graph Generation
date: 2021-08-20 16:15:27 
categories:
-  cv
tags:
-  cv
-  machine learning
-  graph
cover: image-20210820225714355.png
---

## Scene Graph Generation

### the Basic concept of Scene Graph Generation

![image-20210820223409995](Generation/image-20210820223409995.png)

### challenge of Scene Graph Generation

- [x] Quadratic explosion of relationships

- [x] Long tail distribution 

  amount rare but exist relationships

### Vision-Language Model

![image-20210820225104306](Generation/image-20210820225104306.png)

* vision model : predict the object and the relationship (N+k)
* language model : to correct the relationship and objects 

#### the cons :

- [x] neglect other relationships

### Graph representation Model

representing objects as graph with dense connection 

![image-20210820225714355](Generation/image-20210820225714355.png)

