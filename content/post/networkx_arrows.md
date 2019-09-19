+++
title = "Adding arrows to networkx"
author = ["Rodrigo Dorantes-Gilardi"]
date = 2018-05-29T00:00:00-05:00
draft = false
+++

## No arrows in networkx {#no-arrows-in-networkx}

Believe it or not, the popular network package `networkx` didn't have arrows to draw directed
networks. Instead, it used some ugly rectangles to indicate direction.

Networkx is a package that I use regularly, and I wanted to contribute to the open source community
that had given me so much, so I decided to implement the arrows.

Before, a network would look like this:

{{< figure src="/images/old_networkx.png" caption="Figure 1: Example of an old directed network." >}}


## Arrows in networkx {#arrows-in-networkx}

The flow I used for my contribution was the following:

1.  Open an [issue](https://github.com/networkx/networkx/issues/2757) on the networkx' github repo.
2.  After I was told the issue was worth it, I created a [pull-request](https://github.com/networkx/networkx/pull/2760).
3.  After some back and forth discussion, they pulled my code!

Now, a directed network looks like this:

{{< figure src="/images/new_networkx.png" caption="Figure 2: Example of an actual directed network." >}}

This contribution really made me learn a lot about the code behind the drawings of networkx, but
also about `matplotlib`. I think that contributing to a project is a great idea to learn how to code
better and to give something to the community.
