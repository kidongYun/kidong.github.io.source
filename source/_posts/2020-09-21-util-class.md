---
layout: post
title:  "Private constructor on the util class"
date:   2020-09-21 11:21:54 +0900
categories: java
---

We often use the util class by any purpose. whenever you should think about the way how to block create the instance of this class
Util classes usually are announced as the static method and fields. That means we don't want to create these as the instance.
for that, We should announce the private keyword on the constructor of it.
  
```java

public class Utility {
    /** This code can protect to create your object from another places */
    private Utility() { }
}
```
