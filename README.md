# OpenC2 Companion Guide

You've read *most of* the OpenC2 specs, and even wrote a quick Consumer to try out. Now what?

Well, here is an informal guide to the knitty-gritty of OpenC2. Before jumping into a lot of text, it will help to see the basic format of Commands and Responses:


 
**Command Payload**
* **"action"** : `Required string; single-word eg "deny"`
* **"target"** : `Required one-key-dictionary, with its value dependent on the key. eg {"ipv4_net": ...}`
* **"args"** : `Multiple-key-dictionary eg {"response_requested" : ..., "duration" : ...}`
* **"actuator"** : `One-key-dictionary, with its value dependent on the key eg {"slpf": ...}` [Actuator Field Disambiguation](/disambiguation/actuator_field.md)
* **"command_id"** : `String`

**Response Payload**
* **"status"** : `Required number; eg 200`
* **"status_text"** : `String; eg "the command succeeded because..."`
* **"results"** : `Multiple-key-dictionary ...`

And don't forget the Message headers!

**Headers** : Known as [Common Message Elements](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message) in Language Spec, their format and use depends on the Transfer Spec.
* **content_type** : Is this JSON?
* **msg_type** : Is this an OpenC2 Command or Response?
* ... many more that are dependent on the Transfer Spec.

# The Basics
# Producer + Consumer

* **Producers** send Commands to Consumers. If you want to defend your network, your network nodes will be OpenC2 Consumers, awaiting commands from your Producer(s). The Producer could be a command-line script that you run manually, or a billion dollar orchestration system. We don't care.
* **Consumers** act when given a Command, and reply to Producers with Responses.

Notice how nobody has said:

     The Producer POSTS a JSON command over TLS to the Gateway on port 99 and expects a 200 OK on success.
     
Instead, everything related to Transfer Protocol, Serialization, Commands and even Device is abstractly defined or referenced in the OpenC2 Language. This way a system of Producers and Consumers could be OpenC2 compliant no matter if they're using HTTPS, MQTT, JSON, CBOR, running on a VM, Mac-Mini, or Physical Router, etc. The specific Transfer, Serialization, and set of Commands are composed together with their own specs, and OpenC2 doesn't tell you what to run it all on.

BECAUSE OF THIS, YOU WILL OFTEN FEEL LIKE YOU'RE MISSING CONCRETE DEFINITIONS OF WHAT OPENC2 IS. You will never find one document that tells you everything you need. Instead, you need to know your Transfer, Serialization, Commands, and what they're all running on ahead of time, and figure out how they work together yourself. If you are familiar with abstract interfaces in programming, you might feel right at home.


# Message: Command or Response

An OpenC2 Message is a Command OR Response.

* **Commands** are sent by Producers to Consumers. There can only be ONE Command in a Message.
* **Responses** are sent by Consumers to Producers. There can only be ONE Response in a Message

Again, notice how we didn't mention anything about Tranfer, Serialization, or even what the Commands are yet? We also haven't said how many Responses are generated for any Commands.


# Message: Headers

The forgotten children of OpenC2: the headers. They're called **Common Message Elements** in the Language Spec, and their presense and format is completely dependent on what Transfer you are using. However, some *look* like HTTP headers, and become so when used with HTTPS Transfer, eg:

Here is a Common Message Element **"content_type"**, cased and dashed appropriately for HTTPS:

```




                   This message is an
                   OpenC2 Command!
                          |               Using this
                          |               OpenC2 Version
                          |               |
                          v               v           

Content-type: application/openc2-cmd+json;version=1.0
                    
                                     ^
                                     |
                                     |
                                   Serialized with JSON!
```
    
How did we know how to format it? You would only know by reading the HTTPS Transfer Spec.

# Command: Action/Target Pair

This is the bread-and-butter of OpenC2, the biggest selling point, and what makes it so simple and powerful.

The meat of any OpenC2 Message is the payload; known as the "Content" of a message in the Language Spec.

In an OpenC2 **Command** Message, the only required payload is an **action** and **target** pair.

The basic syntax is shown below. One reason the syntax and format of commands doesn't feel too well-defined in the specs is, again, they're not defined directly, but instead reference other specifications, ie JSON. The specs say "OpenC2 is agnostic of serialization." But also, "You must support JSON". So we are left with a lot of examples in JSON, with an asterisk next to them saying "This section is non-normative". Soooooo, for the sake of ease and to actually implement something, let's assume OpenC2 messages are always JSON.


Back to the action/target pair:

```

     Action is always a single-word, eg "deny"
           |
           |
           |
           v
           
"action":  "deny"
"target":  {"ipv4_net" : ["192.168.17.0/24"] }

           ^             ^
           |             |
           |             |
           |             Type and value here depend on the target.
     Target is           For ipv4_net in json, it's a one-value array.
     always a one-key          
     dictionary, eg 
     {"ipv4_net": ...}
                              We could also have a another dictionary, 
                              as with target ipv4_connection:
                               |
                               |
                               |
                               v 
"action": "deny" 
"target": {"ipv4_connection" : {"protocol": "tcp",
                                "src_addr": "1.2.3.4"}
```

# Command: Actuator Field

### This field helps a Consumer determine if it should act on a command.

Two fundamental things to understand are that:

1. **Shotgun Commands**: Producers may **SPAM** Consumers with commands that don't apply to those Consumers, and the Consumers need a way to know which commands are applicable to them.
1. **Multiple Actuator Profiles**: Consumers may implement more than one Actuator Profile.



# Examples

Say we have Consumers implementing the following Actuator Profile(s):

|Consumer |Actuator Profile(s)| Description | Overlapping Action-Target Pairs |
|-|-|-|-|
|1|slpf | Stateless Packet Filter | -|
|2|x-troublemaker | Unknown, but supports deny ipv4_net  | - |
|3|x-acme | RoadRunner Hunting | - |
|4|slpf + x-acme | - | none |
|5|slpf + x-troublemaker | - | deny ipv4_net |


## deny ipv4_net
...with different values for the Actuator field:


|             |"actuator": "" | "actuator": {"slpf".. |
|-|:-|:-|
|slpf| &#x2705; 200 OK             | &#x2705;200 OK |
|x-troublemaker|&#x2705; 200 OK             |&#x274C; No work performed, but Response is UNDEFINED |
|x-acme| :negative_squared_cross_mark: 404; not found   |&#x274C; No work performed, but Response is UNDEFINED |
|slpf + x-acme| &#x2705; 200 OK                                                          |&#x2705; 200 OK |
|slpf + x-troublemaker| &#x274C; Behavior **and** Response are UNDEFINED  |&#x2705; 200 OK |









# Command: Required

What commands can your Producer send to your Consumer? There is always one command that is required to implemented on your Consumers:

     query features
     
This is probably the first command your Producer will send out. The Response will tell you everything you need to know about the Consumer that received the Command, including what other commands it implements. Where are those commands defined though?


# Actuator Profiles

This is where the meaning


This is defined in a specification called an Actuator Profile. This spec defines 



Ok, we've covered the minimum payload. Anything else we need to be up to spec?

Yes! The forgotten children of OpenC2: the headers. In the Language Spec, they're known as Common Message Elements, and their presense and format is completely dependent on what Transport you are using. When sending the above examples over HTTP, you need to include the following header:
    
    Content-type: application/openc2-cmd+json;version=1.0
    
That header combines TWO Common Message Elements: content_type and msg_type. You would only know this by reading the HTTPS Transfer Spec.


* **Transfer-Dependent Headers** : Known as [Common Message Elements](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message) in Language Spec. The presence and format of these are completely dependent on the Transfer Spec. For example, the HTTPS combines content_type and msg_type into one field.
  * **content_type** : Is this JSON?
  * **msg_type** : Is this an OpenC2 Command or Response?
  * ... many more that are dependent on the Transfer Spec.
 
* **Content / Payload**
  * **"action"** : string; single word
  * **"target"** : one-key-dictionary, with its value dependent on the target. eg "target" : {"ipv4_net": ...}
  * **"args"** : nested-dictionary; multiple-key-dictionary eg "args" : {"response_requested" : ..., "duration" : ...}
  * **"actuator"** : nested-dictionary; one-key-dictionary, with its value dependent on the target eg "actuator" : {"slpf": ...} [Actuator Field Disambiguation](/disambiguation/actuator_field.md)
  * **"command_id"** : string
