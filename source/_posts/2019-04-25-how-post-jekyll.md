---
layout: post
title:  "How to post The Jekyll page via Markdown"
date:   2019-04-25 03:23:00 +0900
categories: blog
---

### 1. Make a __.md__ file in .../_posts/
__/_posts__ folder is for holding your Jekyll posts. 

and I recommend you to use the editor(__Atom__, __VSCode__, __Editplus__) for typing this.

I commonly use __VSCode__. if you want, you can download __VSCode__ in [here](https://code.visualstudio.com/download)

![](/res/2019-04-25-how-post-jekyll/1.png)

then please open your Jekyll Project folder in __File > Open Folder__

and make a __.md__ file in /_posts folder.

![](/res/2019-04-25-how-post-jekyll/2.png)

![](/res/2019-04-25-how-post-jekyll/3.png)

you should keep the shape of your file name like below when you making Jekyll posts.
```
    date-title.md

    example) 2019-04-25-jekyll-test.md
```

then you can check your updates at locally

### 2. Let's fill the content in your post

![](/res/2019-04-25-how-post-jekyll/4.png)

please type what you want in your post as shown below.

### 3. Push your updates to Github Repository.
```
    git add .
    git commit -m "anything"
    git push -u origin master
```

and also you can check your updates at your github pages.