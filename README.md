# openc2_companion


&#x2705; 1 OpenC2 Response in 1 HTTP Response      &#x274C; undefined;
 
content_type: application/openc2-rsp+json;version=1.0
Content, content_type, and msg_type are required in all Messages.
 

* **Transfer Dependent Headers**
* [content_type]() : string; required by Language Spec, defined in Transfer Spec. This is part of your message header.
* [msg_type]() : Defined in Transfer spec, may not be its own field. 
 
* **Content / Payload**
  * ["action"]() : string; single word
  * ["target"]() : nested-dictionary; only one key at root
  * ["args"]()  : nested-dictionary; multiple keys at root
  * ["actuator"]() : nested-dictionary; only one key at root
  * ["command_id"]() : string
