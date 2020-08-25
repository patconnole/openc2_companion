# OpenC2 Companion Guide

You've read (*most of*) the OpenC2 specs, and even wrote a quick Consumer to try out. Now what?

Well, here is an informal guide to the knitty-gritty of OpenC2. 


## OpenC2 Message 

In an OpenC2 Command Message, the only required payload is an action and target:
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
"action": "deny"               v  
"target": {"ipv4_connection" : {"protocol": "tcp",
                                "src_addr": "1.2.3.4"}
```





* **Transfer-Dependent Headers** : Known as [Common Message Elements](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message) in Language Spec. The presence and format of these are completely dependent on the Transfer Spec. For example, the HTTPS combines content_type and msg_type into one field.
  * **content_type** : Is this JSON?
  * **msg_type** : Is this an OpenC2 Command or Response?
  * ... many more that are dependent on the Transfer Spec.
 
* **Content / Payload**
  * **"action"** : string; single word
  * **"target"** : one-key-dictionary, with its value dependent on the target. eg "target" : {"ipv4_net": ...}
  * **"args"** : nested-dictionary; multiple-key-dictionary eg "args" : {"response_requested" : ..., "duration" : ...}
  * **"actuator"** : nested-dictionary; one-key-dictionary, with its value dependent on the target eg "actuator" : {"slpf": ...} [Actuator Field Disambiguation](/disambiguation/actuator.md)
  * **"command_id"** : string
