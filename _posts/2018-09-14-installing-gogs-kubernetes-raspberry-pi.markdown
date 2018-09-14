---
layout:      post
title:       "How to install Gogs on Kubernetes and Raspberry Pi"
description: "Having you own GitHub running on premise for free"
date:        2018-09-14 12:00:00
update-date: 2018-09-14 12:00:00
author:      "Alexandre"
comments:    True

---

## Why?

I wanted a Git service because I didn't like having to SSH on my NAS and
creating `--bare` repos by hands.

I didn't want to store it directly on the SD Card of a Raspberry Pi because of the
high risk of data corruption, and I happen to have [four Raspberry Pi set up a Kubernetes cluster](https://blog.sicara.com/build-own-cloud-kubernetes-raspberry-pi-9e5a98741b49){:target="_blank"}.

I first thought about GitLab but it has high memory requirements. Then I stumbled upon
[Gogs](https://gogs.io){:target="_blank"}.

## Gogs

Gogs is a self-hosted Git service written in Go. The program is just one binary
and only requires a database (SQLite, MySQL or Postgres) to work.

## Prerequisites

- Having created 2 folders on the filesystem that you expose to your cluster: one is of
the DB, the other is for Gogs (it will hold the config and the repos)

## Setup

My k8 config files are [on GitHub](https://github.com/achntrl/k8-deployments/tree/master/gogs){:target="_blank"}

First, you have to create a `secrets.yml` file

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: gogs-secrets
type: Opaque
data:
  username: <redacted>
  password: <redacted>
```

Put base64 encoded credentials in place of the `<redacted>`. `username`
and `password` will be used as the user and password for Postgres. To get them, run:

```console
# In your terminal
echo -n <your-string> | base64
```

Then, you have to edit the `PersistentVolume` according to your filesystem provider.
I use NFS. The list of filesystems that kubernetes supports can be found [here](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#types-of-persistent-volumes){:target="_blank"}. This can be edited in the files `gogs-{db, repos}-pv.yml`

You might also want to adjust the size of the volumes accordingly to your needs. In that case,
edit both `PersistentVolume` and `PersistentVolumeClaim`.

Then, run in that order:

```console
kubectl apply -f secrets.yml
kubectl apply -f gogs-db-pv.yml
kubectl apply -f gogs-db-pvc.yml
kubectl apply -f gogs-postgres.yml
kubectl apply -f gogs-repos-pv.yml
kubectl apply -f gogs-repos-pvc.yml
kubectl apply -f gogs-app.yml
```

When the pod is up and running, go to `http://<your-cluster-master-ip>:30009`. You should be greeted
by the init page of Gogs. Add your database credentials and other fields, and click follow the
instructions. You're all set!
