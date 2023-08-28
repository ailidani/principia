---
title: "ABD Algorithm"
date: 2021-08-23
draft: true
---

## Write
Get phase:
`vt-set` = read `vt` pairs from majority of storage nodes
select unique `t` such that `t > max(t in vt-set)`

Set phase:
`write_request(v, t)` on storage nodes
storage nodes store `vt` only if `t >` their stored `t`
storage nodes send `ACK`
when majority `ACK`s are received return `OK`

## Read
Get phase
`vt-set` = read `vt` pairs from majority of storage nodes
select `vt` such that `t = max(t in vt-set)`

Set phase
`write_request(v, t)` on storage nodes
storage nodes store `vt` only if `t >` their stored `t`
storage nodes send `ACK`
when majority `ACK`s are received return `v`
