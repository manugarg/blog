---
title: "Why you need probers"
lead: ""
draft: true
images: []
weight: 300
date: 2024-06-05
lastmod: 2024-06-16
publishDate: 2024-06-17T07:00:00-07:00
contributors: ["Manu Garg"]
tags: ["Cloudprober", "Reliability", "SRE"]
description:
  "What is a prober? Why do we need them? It baffled me somewhat that probers
  were not well known, let alone used, in most of the places I worked at after
  leaving Google in 2021. How was it possible that something that plays a vital
  role in reliability at Google and possibly other big techs, is not used more
  widely."
---

What is a prober? Why do we need them? It baffled me somewhat that probers were
not well known, let alone used, in most of the places I worked at after leaving
Google in 2021. How was it possible that something that plays a vital role in
reliability at Google and possibly other big techs, is not used more widely. You
may not give any other credit to Google, but you can’t take “being reliable”
away from them. Probers play a big part in ensuring that reliability. So why is
this useful technology still mostly alien to most of the companies?

After spending a few years outside of Google, I believe now I know why. Probers
require a non-trivial upfront investment, and benefits aren’t always obvious to
someone who hasn’t experienced their benefits firsthand. Additionally, the lack
of awareness and demand for probers means there aren’t many good off-the-shelf
commercial solutions available. Most observability companies offer some kind of
synthetic monitoring, which is similar to probers for the most part, but those
options are often expensive and inadequate.

## What is a prober?

The basic idea behind probers is to monitor your service from the customer’s
point of view. Probers do the same thing that your customers do, through the
same interface, and verify it’s all working. This kind of monitoring is also
called blackbox monitoring because you treat the system as a blackbox.

<img src="/images/what-is-prober.png" alt="what-is-prober" width="400" style="display: block;margin-left: auto;margin-right: auto;"/>

## But, why?

Probers are a very good indicator of whether things are working or not. There
are reasons for that:

1. Probers see what customers see. Your monitoring agent, the prober, acts like
   a customer of yours and monitors for availability and performance
   regressions. If done right (more on this later), this is a very useful
   monitoring setup.
2. Probers provide a signal independent of the service implementation itself.
   You’re not relying on the service to export a specific metric or a log line.
   In other words, probers change independently of the service itself. This
   decoupling is very useful, and in spirit similar to how auditors are external
   to the entity being audited.
3. A corollary of the above point: probers stay stable over time. You can change
   your service (a blackbox from prober's point of view) from within as much as
   you like, but as long as your interfaces stay the same, the probers don’t
   need to change.

Probers provide you a good controlled end-to-end monitoring setup, unaffected by
a lot of other factors, like user network, service working correctly, business
hours, etc. Probers don't cover everything but they provide a rock solid signal.

## What about other types of monitoring?

### Service Metrics

Service metrics are what most organizations rely on today for monitoring.
Service metrics are great, and the idea is not to replace them, but I’d argue
that service metrics alone are not sufficient for the following reasons:

- Too close to the service being monitored. Often times, when a service is
  having an outage, it will also stop exporting useful data,
- Dependency on the implementation. As implementation changes, metrics will need
  to be updated or you’ll have partial to no real monitoring left. You won’t
  believe how many times I’ve heard teams say – “we had monitoring but it was
  not configured correctly”.
- Dependent on actual interactions to take place for service to detect errors.
  What if your service actually broke after the maintenance during the off hours
  but you won’t find it until users actually try to use the service.

### Real User Monitoring (RUM)

Real user monitoring or client side monitoring is based on the signals generated
by clients, browsers or apps. It takes you as close to the user as possible. but
it suffers from two big issues: 1) Noise: Client side monitoring tends to be
really noisy and depends on a lot of factors, like networking, user device,
etc, 2) It still relies on users using the service first.

In practice, real user monitoring is good for debugging customer issues but it
doesn’t provide a reliable monitoring signal that you can page yourself on.

## Comparision Summary

|                                                                               | Probers                                             | Service Metrics and Logs | Real User Monitoring                       |
| ----------------------------------------------------------------------------- | --------------------------------------------------- | ------------------------ | ------------------------------------------ |
| Reliability of signal                                                         | High                                                | Moderate                 | Low as signal is noisy                     |
| Initial ease and cost of implementation                                       | Significant initial investment                      | Easy                     | Moderate to significant initial investment |
| Closeness to the user?                                                        | Very Close                                          | Not close                | Super close but noisy                      |
| Return on investment for monitoring: ongoing cost, reliability of signal, etc | High as ongoing cost is low and reliability is high | Moderate                 | Low                                        |

## Tooling

I'll have to write aother post to discuss tooling in detail, but you can't not
go wrong with [Cloudprober](https://cloudprober.org) :wink:. It started at
Google, but grew in the world of open source (see [about
cloudprober]({{< ref "about-cloudprober.md" >}})) and is used by many
organizations now. Cloudprober offers a feature-rich and reliable probing
solution.

## What do typical probers look like?

Here are some of the probers use cases I’ve seen over the years:

- Network connectivity and performance test between two endpoints. Endpoints can
  be internal or external, or over VPN.
- TCP / UDP tests to ensure something is available or not available.
- REST (HTTP) probes to APIs with or without Auth.
- HTTP probes for important properties like homepage, docs, etc.
- gRPC requests to services.
- Kubernetes components, e.g. kube-dns, availability testing.
- DNS probes to verify DNS.
- Database availability checks.
- Object storage (S3/GCS) availability and performance.
- Workflow probers to verify complex user journeys.
