# OpenC2 Companion Guide
You've read *most of* the OpenC2 specs, and even wrote a quick Consumer to try out.

Here is an informal guide to the knitty-gritty of OpenC2. Before jumping in to a lot of text, it will help to see the basic format of Commands and Responses:

**Command Payload in JSON**
```
    "action"     : "deny"
    "target"     : {"ipv4_net" : ["192.168.1.0/24"] }
    
(optional)
    "actuator"   : {"slpf": {} }
    "args"       : {"response_requested" : "ack", "start_time" : 1534775460000 }
    "command_id" : "12345"
```

**Response Payload in JSON**
```
    "status"      : 200
    
(optional)
    "status_text" : "The command succeeded"
    "results"     : {"slpf" : {"rule_number": 1234}, "versions" : ["1.0"]}
```

**Message Headers in HTTP**
```
Content-type: application/openc2-cmd+json;version=1.0
```

 
# Composing your Implementation

In the above examples, notice how they're all qualified with **..in JSON** or **..in HTTP**? Why is that? Why not just say "Example Command" or "Message Header" without the qualification?

Well, this is the POWER and BARRIER-TO-ENTRY of OpenC2.


In reading the specs, you never saw anything like this:

     The Producer POSTS a JSON "deny" command over TLS to the Gateway on port 99,
     and expects a 200 OK Payload in an HTTP Response on success.
     
Instead, everything in the OpenC2 Language related to Transfer Protocol, Serialization, Commands and even Device is abstractly defined or referenced in the spec. Then their specific implementations are given a growing list of individual specifications. 

This way a system of Producers and Consumers could be OpenC2 compliant no matter if they're using HTTPS, MQTT, JSON, CBOR, running on a VM, Mac-Mini, or Physical Router, etc. The specific Transfer, Serialization, and set of Commands are composed together with their own specs, and OpenC2 doesn't prescribe what they run on.

BECAUSE OF THIS, YOU WILL OFTEN FEEL LIKE YOU'RE MISSING CONCRETE DEFINITIONS OF WHAT OPENC2 IS. You will never find one document that tells you everything you need. Instead, you need to know your Transfer, Serialization, Commands, and what they're all running on ahead of time, and figure out how they work together yourself. If you are familiar with abstract interfaces in programming, you might feel right at home when reading the OpenC2 Language spec.


```
  OpenC2 Specifications          Other Specifications                       Your Implementation

    |               |                     |
    v               v                     v                               +---------------------+
                                                                          |      Producer(s)    |
                                                                          +--+---------------+--+
+--------+      +--------+                                                   |               ^
|Language|      |Transfer|                                                   |               |
|        +----> | * HTTP +-------------------------------------------------> |               |
|        |      | * MQTT |                                                   |               |
|        |      +--------+                                                   |               +
|        |                                                                   |
|        |      +--------+                                                   |           "status": 200
|        |      |Commands|                                                   |
|        +----> | * SLPF +-------------------------------+                   v               ^
|        |      | * ...  |                               |                                   |
|        |      +--------+         +-------------+       +----------> "action":"deny"        |
|        |                         |Serialization|       |            "target":"ipv4_addr"   |
|        +-----------------------> | * JSON      +-------+                                   |
|        |                         | * CBOR      |                           +               |
+--------+                         +-------------+                           |               |
                                                                             v               |
                                                                          +-- ---------------+--+
                                                                          |      Consumer(s)    |
                                                                          +---------------------+

```

# Producer + Consumer

* **Producers** send Commands to Consumers. If you want to defend your network, your network nodes will be OpenC2 Consumers, awaiting commands from your Producer(s). The Producer could be a command-line script that you run manually, one of your Consumers, or a billion dollar orchestration system. It doesn't matter.
* **Consumers** act when given a Command, and reply to Producers with Responses.



# Message: Command or Response

An OpenC2 Message is a Command OR Response.

* **Commands** are sent by Producers to Consumers. There can only be ONE Command in a Message.
* **Responses** are sent by Consumers to Producers. There can only be ONE Response in a Message

Again, notice how we didn't mention anything about Transfer, Serialization, or even what the Commands are yet? We also haven't said how many Responses are generated for any Commands, or when/how they're sent.

**Command Payload**
```
Fields:                             JSON Example:
                                           
action     : Required             "action"     : "deny"
target     : Required             "target"     : {"ipv4_net" : ["192.168.1.0/24"] }
actuator   : -                    "actuator"   : {"slpf": {} }
args       : -                    "args"       : {"response_requested" : "ack", "start_time" : 1534775460000 }
command_id : -                    "command_id" : "12345"
```

**Response Payload**
```
Fields:                             JSON Example:

status      : Required            "status"      : 200
status_text : -                   "status_text" : "The command succeeded"
results     : -                   "results"     : {"slpf" : {"rule_number": 1234}, "versions" : ["1.0"]}
```

# Message: Headers

The forgotten children of OpenC2: The Headers. They're called [Common Message Elements](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message) in the Language Spec, but their implementation details are in the Transfer specs, because 'headers' is very dependent on transport protocol. They tell you if the payload is a Command or Response, in JSON or something else, etc.

**Message Headers**
```
content_type : Is the payload JSON?
msg_type     : Is the payload an OpenC2 Command or Response?
request_id   : Easily conflated with "command_id", but can be used to help group commands. Again, look in your Transfer Spec.
...          : Many more that are dependent on the Transfer Spec.
```

Notice that some *look* like HTTP headers; they become so when used with HTTPS Transfer, eg:

Here are the OpenC2 Common Message Elements **"content_type"** and **"msg_type"**, put into the HTTP Header **"Content-type"**. We also stuck in the version for good measure.

```
HTTP Header:
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

In an OpenC2 **Command** Message, the only required payload is an **action** and **target** pair.
This is the bread-and-butter of OpenC2, the biggest selling point, and what makes it so simple and powerful. Is there a bad-guy coming in on some IP Address? Block him with 

```json
"action" : "deny",
"target" : {"ipv4_net" : ["...."]}
```
The basic syntax is shown below. 

One reason the syntax and format of commands doesn't feel too well-defined in the specs is, again, they're not defined directly, but instead reference other specifications, ie JSON. The specs say "OpenC2 is agnostic of serialization." But also, "You must support JSON". So we are shown a lot of examples in JSON, with an asterisk next to them saying "This section is non-normative". But, for the sake of ease and to actually implement something, let's assume OpenC2 messages are always JSON.


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
     
     
                              We could also have another dictionary, 
                              as with target ipv4_connection:
                               |
                               |
                               |
                               v 
"action": "deny" 
"target": {"ipv4_connection" : {"protocol": "tcp",
                                "src_addr": "1.2.3.4"} }
```

**The "action" field is obviously simple**; it's just one word, and can only be a word from the actions listed in the Language Spec.

**"target" is its own beast.** For example, 

How did we know that ipv4_net is a one-value string array?

    ["192.168.17.0/24"]

It's actually complicated to figure out, and this is one of the barriers-to-entry we mentioned earlier. Instead of prescribing exactly how to format something in JSON, we have to deduce the format for ANY serialization.

Here's one way to figure out **ipv4_net** in **JSON**. Read at your own peril.

1. Search for examples in any of the OpenC2 specs that use **ipv4_net**.
2. Can't find any? Look in the language spec for its **type**.
3. That type is **IPv4-Net**. Notice the capitalization and dash.
4. Search the Language Spec for that.
5. We find 6 references. Look for one that begins with **Type: IPv4-Net (Array /ipv4-net)**
6. In the table there we see 2 values: **IPv4-Addr** and **Integer**
7. Search the Language Spec for each of those.
8. Just kidding. Your next move is to read the text *above* that table.
9. The fourth line there says, **"JSON serialization of an IPv4 address range SHALL use the 'dotted/slash'.."**
10. Ok, now we know to use "192.168..../24" style addresses, but how do we know to put it into a JSON array? A one-value array???
11. Go back to the *first* line, which reads "An IPv4 address range... **consists of two values**, an IPv4 address and a prefix."
12. From there, we can guess that we are dealing with an array because of **"..two values.."**, and the fact that we saw the word "Array" in **Type: IPv4-Net (Array /ipv4-net)**
13. What is **(Array /ipv4-net)**? It does look like a type definition, but I don't recognize the language.

So now, we are **PRETTY SURE** that a JSON formatted "ipv4_net" value is

    ["192.168.17.0/24"]

Congratulations!

# Command: Actuator Field. No, not yet.

Before we look at the **actuator field** of an OpenC2 Command, we need to know what an actuator is.

**An actuator is a Consumer's implementation of an Actuator Profile.**

# Actuator Profile

An **Actuator Profile** defines your commands and what they do. For example, the **Stateless Packet Filter Actuator Profile** grabs a bunch of actions and targets from the Language spec, pairs them up as individual commands, then declares what those commands should do. On top of that, it gives itself a standard name: **"slpf"** (known as a namespace identifier "nsid").

**Consumers implement Actuator Profile(s)**

So, if we have a Consumer that implements the SLPF Actuator Profile, the Consumer **has** an SLPF Actuator.

It's not complicated, but one analogy that helps is the following:

![laptop_stickers](/images/laptop_sticker.png)

In this analogy, the laptop is a Consumer, and the stickers advertise the Actuator Profiles it implements. 
* The Windows sticker advertises an interface you are familiar with that has a "start" command. 
* If there was a Blu-ray Disc sticker, you would expect to find a disc drive with an "open/close" command.

Well, where are the stickers in OpenC2? There are three kinds:

* **Discovered**: You send the Consumer a command called **'query-features'**, and it tells you the Actuator Profiles it implements. That's the only command that all Consumers must implement, and the only one that isn't specific to Actuators.
* **Pre-Shared**: You **configured** the Consumer, so you already know the Actuator Profiles it implements.
* **Unknown**   : You don't know what the Consumer implements, but you just send it commands because this is an emergency and we don't have time to play games.

Ok, back to using the **actuator field** in an OpenC2 Command.

# Command: Actuator Field
### This field helps a Consumer determine if it should act on a command.

Two fundamental things to understand:

1. **Shotgun Commands**: Producers may **SPAM** Consumers with commands that don't apply to those Consumers, and the Consumers need a way to know which commands are applicable to them.
1. **Multiple Actuator Profiles**: Consumers may implement more than one Actuator Profile.



## Example

Say we have the following Actuator Profiles:


| Actuator Profile | Description |
|-|-|
|slpf | Stateless Packet Filter |
|x-troublemaker | Unknown, but supports **deny ipv4_net**  |
|x-acme | RoadRunner Hunting |

And Consumers that implement them:

|Consumer |Actuator Profile(s)|  Duplicate Action-Target Pairs (besides query-features) |
|-|-|-|
|1|slpf |  -|
|2|x-troublemaker |  - |
|3|x-acme |  - |
|4|slpf + x-acme | **none** |
|5|slpf + x-troublemaker | **deny ipv4_net** |

We send these consumers the same command:

```json
"action" : "deny",
"target" : {"ipv4_net": ["192.168.1.0/24"]}
```

And sometimes we include the actuator field specifying SLPF:

```json
"actuator" : {"slpf" : {}}
```

What does the Consumer do, assuming it processed the command successfully if able? How does it respond?


| |             |"actuator": "" | "actuator": {"slpf".. |
|-|-|:-|:-|
|1|slpf| &#x2705; 200 OK             | &#x2705;200 OK |
|2|x-troublemaker|&#x2705; 200 OK             |:negative_squared_cross_mark: 404; not found |
|3|x-acme| :negative_squared_cross_mark: 404; not found   |:negative_squared_cross_mark: 404; not found |
|4|slpf + x-acme| &#x2705; 200 OK                                                          |&#x2705; 200 OK |
|5|slpf + x-troublemaker| &#x274C; Behavior **and** Response are UNDEFINED  |&#x2705; 200 OK |


From this, hopefully it's clear how you could use the actuator field to your advantage, and also that you should avoid defining Consumers that contain duplicate action-target pairs.

# Command ID vs Request ID

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
