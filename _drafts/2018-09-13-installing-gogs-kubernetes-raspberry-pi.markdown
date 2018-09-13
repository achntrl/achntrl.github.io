---
layout:      post
title:       "How to install Gogs on Kubernetes and Raspberry Pi"
description: "Having you own GitHub running on premise for free"
date:        2018-09-13 12:00:00
update-date: 2018-09-13 12:00:00
author:      "Alexandre"
comments:    True

---

## Why?

I am close to the expiry date of my GitHub student pricing. I wanted to have a solution
for hosting my private repos, mostly as a backup for old code that I might use one day
(probably won't happen but who knows).

I didn't wanted to store it directly on the SD Card of a Raspberry Pi because of the
high risk of corruption, and I happen to have [four Raspberry Pi set up a Kubernetes cluster](https://blog.sicara.com/build-own-cloud-kubernetes-raspberry-pi-9e5a98741b49){:target="_blank"}.

I first though about GitLab but it has quite high memory requirements. Then I stumbled upon
[Gogs](https://gogs.io){:target="_blank"}
