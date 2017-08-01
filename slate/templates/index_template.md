---
title: API Reference

language_tabs:
  - shell
  - python

toc_footers:
  - <a href='http://dev.lightning.community'>Developer site</a>
  - <a href='mailto:max@lightning.engineering'>Contact Us</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:

search: true
---

# Introduction

Welcome to the API documentation for LND, the Lightning Network
Daemon.

This page serves purely as a reference, generally for those who already
understand how to work with LND. If this is your first time, please check out
our [developer site](https://dev.lightning.community) and
[tutorial](https://dev.lightning.community/tutorial).

This site features API documentation for command line arguments, gRPC in Python
and Javscript, and the gRPC REST proxy. The original `rpc.proto` file from which
the gRPC documentation was generated can be found in the [lnd Github
repo](https://github.com/lightningnetwork/lnd/blob/master/lnrpc/rpc.proto).

{% for method in methods %}

# {{ method.name }}
{% if not method.streaming_request and not method.streaming_response %}
### Simple RPC
{% elif not method.streaming_request and method.streaming_response %}
### Response-streaming RPC
{% elif method.streaming_request and not method.streaming_response %}
### Request-streaming RPC
{% elif method.streaming_request and method.streaming_response %}
### Bidirectional-streaming RPC
{% endif %}

{{ method.description }}

```shell
{% if method.lncli_name %}$ {{ method.lncli_info.usage }}{% endif %}
{% if method.lncli_name %}{% for option in method.lncli_info.options %}
# {{ option }}
{% endfor %}{% endif %}
```

```python
>>> import rpc_pb2 as ln, rpc_pb2_grpc as lnrpc
>>> import grpc
>>> channel = grpc.insecure_channel('localhost:10009')
>>> stub = lnrpc.LightningStub(channel){% if method.streaming_request %}
{% include 'streaming_request.html' %}{% else %}
>>> request = {% include 'request_obj.html' %}{% endif %}
>>> response{% if method.streaming_response %}_iterable{% endif %} = stub.{{ method.name }}(request{% if method.streaming_request %}_iterable{% endif %})
>>> {% if method.streaming_response %}for response in response_iterable:
    # Do something
    print response
{% else %}response{% endif %}
{% if method.response_message.fields | length == 0 %}{}{% else %}
{ {% for field in method.response_message.fields %}
    {{ field.name }}: <{{ field.type }}>,{% endfor %}
}
{% endif %}
```

### gRPC Request: {{ method.request_type }} {% if method.streaming_request %}(Streaming){% endif %}

{{ method.request_message.description }}
{% if method.request_message.fields | length == 0 %}
This request has no parameters.
{% else %}
Field | Type | Label | Description
----- | ---- | ----- | ----------- {% for field in method.request_message.fields %}
{{ field.name }} | {{ field.type }} | {{ field.label }} | {{ field.description }} {% endfor %}
{% endif %}

{% for request_field_message in method.request_field_messages %}
### {{ request_field_message.display_name }}
{{ request_field_message.description }}
{% if method.request_message.fields | length == 0 %}
This message has no parameters.
{% else %}
Field | Type | Label | Description
----- | ---- | ----- | ----------- {% for field in request_field_message.fields %}
{{ field.name }} | {{ field.type }} | {{ field.label }} | {{ field.description }} {% endfor %}
{% endif %}
{% endfor %}

### gRPC Response: {{ method.response_type }} {% if method.streaming_response %}(Streaming){% endif %}

{{ method.response_message.description }}
{% if method.response_message.fields | length == 0 %}
This response is empty.
{% else %}
Field | Type | Label | Description
----- | ---- | ----- | ----------- {% for field in method.response_message.fields %}
{{ field.name }} | {{ field.type }} | {{ field.label }} | {{ field.description }} {% endfor %}
{% endif %}

{% for response_field_message in method.response_field_messages %}
### {{ response_field_message.display_name }}
{{ response_field_message.description }}
{% if method.response_message.fields | length == 0 %}
This message has no parameters.
{% else %}
Field | Type | Label | Description
----- | ---- | ----- | ----------- {% for field in response_field_message.fields %}
{{ field.name }} | {{ field.type }} | {{ field.label }} | {{ field.description }} {% endfor %}
{% endif %}
{% endfor %}

{% endfor %}