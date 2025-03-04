---
lastmod: 2019-09-15
date: 2019-09-15
notoc: true
linktitle: Prometheus
title: Exporting metrics to Prometheus
weight: 70
source: https://github.com/devopsfaith/krakend-opencensus
menu:
  documentation:
    parent: logging-metrics-tracing
---
[Prometheus](https://prometheus.io/) is an open-source systems monitoring and alerting toolkit.

The Opencensus exporter allows you push data to Prometheus. Enabling it only requires you to include in the root level of your configuration the Opencensus middleware with the `prometheus` exporter. Specify the `port` on which Prometheus is running, the `namespace` (optional), and Prometheus will start receiving the data.

	"github_com/devopsfaith/krakend-opencensus": {
      "exporters": {
        "prometheus": {
            "port": 9091
            "namespace": "krakend"
        }
	  }
	}
- `port` on which Prometheus is listening
- `namespace` sets the domain the metric belongs to. Optional field.

See also the [additional settings](/docs/logging-metrics-tracing/opencensus/) of the Opencensus module that can be declared.