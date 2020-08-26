# OpenC2 Companion Guide

You've read (*most of*) the OpenC2 specs, and even wrote a quick Consumer to try out. Now what?

Well, here is an informal guide to the knitty-gritty of OpenC2. 


## OpenC2 Message Payload: Action + Target

The meat of any OpenC2 Message is the payload; known as the "Content" of a message in the Language Spec.

In an OpenC2 Command Message, the only required payload is an **action** and **target**.

One reason the syntax and format of commands doesn't feel too well-defined is because the OpenC2 Specs don't define them directly, but instead reference other specifications, ie JSON. The specs say "OpenC2 is agnostic of serialization." But also, "You must support JSON". So we are left with a lot of examples in JSON, with an asterisk next to them saying "This section is non-normative". Soooooo, for the sake of ease - Let's assume we're always talking about JSON.



OpenC2 allows you to 

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
