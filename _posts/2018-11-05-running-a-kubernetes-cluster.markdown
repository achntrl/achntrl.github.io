---
layout:      post
title:       "Running a Raspberry Pi cluster, a few months after"
subtitle:    ""
date:        2018-11-05 12:00:00
update-date: 2018-11-05 12:00:00
author:      "Alexandre"
comments:    True

---

After a few months running my cluster, here are my impressions.

## Successes

[Running Gogs](/2018/09/14/installing-gogs-kubernetes-raspberry-pi/) was super easy.
I am happy with the setup and started to migrate my private repositories from GitHub to it.

I set up Traefik as the `IngressController` and it's been working flawlessly. It allows me to
have public urls such as gogs.home.achntrl.com that redirect to HTTPS
with a Let's Encrypt certificate.

Huge shoutout to [Chris Evans](https://medium.com/@evnsio/managing-my-home-with-kubernetes-traefik-and-raspberry-pis-d0330effea9a) for
his instructions on how to setup Traefik and the script to automatically update DNS records.

I used [rak8s](https://github.com/rak8s/rak8s) to automate the cluster creation.
I [customized it to my configuration](https://github.com/achntrl/rak8s). I tried it by recreating the cluster
from a fresh Raspbian install and it worked well. I'm not afraid of losing my
whole cluster anymore.

I also built a Docker image for Datadog agent that I use to monitor my cluster (disclaimer: I work at Datadog, but this
is not officially supported by Datadog). The image can be found [here](https://hub.docker.com/r/achntrl/datadog-agent).

## Problems

I faced problems regarding RAM usage. Kubernetes recommends 2GB or more of RAM per machine. The Raspberry Pis only have 1GB of RAM, and we can feel it on the master node.

![]({{ site.baseurl }}/img/ram-usage.png)

All slave nodes hovered around 500MB of free RAM, but the master node was at 100MB or less. Often, the controller manager or the scheduler died (every 7 minutes on average!). It seems to be caused by pod
eviction when the RAM is low (by default it's when free RAM hit 100MB or less). I tried to push the
eviction limit to 50MB. That improved a bit but I was still seeing evictions

```console
$ k get pods -n kube-system

NAME                                          READY   STATUS    RESTARTS   AGE
coredns-576cbf47c7-slsrt                      1/1     Running   34         3d14h
coredns-576cbf47c7-w88wt                      1/1     Running   40         3d14h
etcd-k8s-master                               1/1     Running   24         3d14h
kube-apiserver-k8s-master                     1/1     Running   32         3d14h
kube-controller-manager-k8s-master            1/1     Running   715        3d14h
kube-proxy-2nfrw                              1/1     Running   2          3d14h
kube-proxy-8gnd4                              1/1     Running   0          3d14h
kube-proxy-df2jk                              1/1     Running   0          3d14h
kube-proxy-nsdbn                              1/1     Running   0          3d14h
kube-scheduler-k8s-master                     1/1     Running   539        3d14h
kube-state-metrics-5458647bbf-9zjgr           2/2     Running   0          2d22h
kubernetes-dashboard-58446cb7fb-c8dq5         1/1     Running   2          3d14h
metrics-server-5584697594-hwdnd               1/1     Running   0          2d18h
traefik-ingress-controller-64c7fdf956-pv8zc   1/1     Running   0          3d3h
weave-net-4tqwg                               2/2     Running   0          3d14h
weave-net-58ckw                               1/2     Running   6          3d14h
weave-net-5l2tz                               2/2     Running   0          3d14h
weave-net-9spwt                               2/2     Running   0          3d14h
```

It was also the cause of a high read activity on the master node at around 12MB/s and problems with a `CronJob` that wasn't properly scheduled.

To fix this, I ordered a bigger board, with 2GB of RAM to run the master node.
I went with the ASUS Tinker Board because it is also an ARM board and I was available
for next-day delivery (approx. €65).

![]({{ site.baseurl }}/img/tinker-board.jpg)

The card is more aesthetic and the SD card slot is push to eject!

Raspbian won't boot on it so I flashed Armbian on the SD card. I had to tweak the rak8s playbook
to properly disable the swap on it, but after that, no more pod eviction. I have about 1.2GB usable
on the master node and 0 kbps reads.

## Next projects

- Measure humidity and temperature in my appartment and send them to a Datadog dashboard with Dogstatsd. I ordered some NodeMCU and DHT22 on Aliexpress but they will take time to arrive :(
- Find a way to rate limit my url with basic auth to prevent brute force attack / DDoS
