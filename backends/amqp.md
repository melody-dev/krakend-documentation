---
lastmod: 2018-04-05
date: 2018-04-05
linktitle: AMQP - RabbitMQ
title: API Gateway integration with AMQP messaging
weight: 90
since: 0.9
source: https://github.com/devopsfaith/krakend-amqp
menu:
  documentation:
    parent: backends
---

The AMQP component allows to **send and receive messages to and from a queue** through the API Gateway.

The configuration of the queue is a straightforward process. To connect the endpoints to the messaging system you only need to include the `extra_config` key with the namespaces `github.com/devopsfaith/krakend-amqp/consume` or `github.com/devopsfaith/krakend-amqp/produce`.

The parameters of this integration follow the AMQP specification. To understand
what are the implications of a certain parameter, see the **[AMQP Complete Reference Guide](https://www.rabbitmq.com/amqp-0-9-1-reference.html)**.

**Common settings**

Both the consumers and the producers have this configuration keys in common:

- `name` - *string*
- `exchange` - *string*
- `routing_key` - []*string*
- `durable` - *bool* `true` is recommended, but depends on the use case.
- `delete` - *bool* `false` is recommended to avoid deletions when the consumer is disconnected
- `exclusive` - *bool*
- `no_wait` - *bool*

The following configurations demonstrate both the **consumer** and the **producer** to create the whole publish/subscribe pattern.

# Consumer
The consumer retrieves messages from the queue when a KrakenD endpoint plugs to its AMQP backend. The recommendation is to connect consumers to `GET` endpoints.

A single endpoint can consume messages from N queues, or can consume N messages from the same queue by adding N backends with the proper queue name.

## Example
The needed configuration to run a consumer is:

        "backend": [
            {
                "host": [
                    "amqp://guest:guest@myqueue.host.com:5672"
                ],
                "extra_config": {
                    "github.com/devopsfaith/krakend-amqp/consume": {
                        "name":           "queue-1",
                        "exchange":       "some-exchange",
                        "durable":        true,
                        "delete":         false,
                        "exclusive":      false,
                        "no_wait":        true,
                        "no_local":       false,
                        "routing_key":    ["#"],
                        "prefetch_count": 10
                    }
                }
            }

## Consumer settings
The full list of parameters for the consumer are:

- All the common settings above plus:
- `prefetch_count` - *int*
- `prefetch_size` - *int*
- `no_local` - *bool*

# Producer
The producer publishes messages to the messaging system for your asynchronous consumption. The recommendation is to plug producers to `POST` endpoints.

Worth mentioning that the producer needs you to pass a body in the request and that the endpoint should declare the `headers_to_pass` so producers are aware of the headers.

## Example
The needed configuration to run a producer is as follows:

        "endpoint": "/producer",
        "headers_to_pass": [ "...", "..." ],
        "backend": [
            {
                "host": [
                    "amqp://guest:guest@myqueue.host.com:5672"
                ],
                "extra_config": {
                    "github.com/devopsfaith/krakend-amqp/produce": {
                        "exchange":       "some-exchange",
                        "durable":        true,
                        "delete":         false,
                        "exclusive":      false,
                        "no_wait":        true,
                        "mandatory": true,
                        "immediate": false
                    }
                }
            }


## Producer settings
The full list of parameters for the producer are:

- All the common settings above plus:
- `prefetch_count` - *int*
- `prefetch_size` - *int*
- `mandatory` - *bool*
- `immediate` - *bool*

Additionally, these items below are parameter keys that can be present in the endpoint URL and are passed to the producer:

- `exp_key` - *string*
- `reply_to_key` - *string*
- `msg_id_key` - *string*
- `priority_key` - *string* - Key of the request parameters that is used as the priority value for the produced message.
- `routing_key` - *string* - Key of the request parameters that is used as the routing value for the produced message.

For instance, an `endpoint` URL could be declared as `/produce/{a}/{b}/{id}/{prio}/{route}` and the producer knows how to map them with a configuration like this:

    {
        ...
        "exp_key":"a",
        "reply_to_key":"b",
        "msg_id_key":"id",
        "priority_key":"prio",
        "routing_key":"route"
    }
