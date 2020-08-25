# openc2_companion


&#x2705; 1 OpenC2 Response in 1 HTTP Response      &#x274C; undefined;
 
content_type: application/openc2-rsp+json;version=1.0
Content, content_type, and msg_type are required in all Messages.
 

* **Transfer-Dependent Headers** : These are called [Common Message Elements in Language Spec](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message)
  * [content_type]() : Defined in Transfer Spec. example: application/openc2-cmd+json;version=1.0 defined in Transfer Spec.
  * [msg_type]() : Defined in Transfer spec, may not be its own field.
  * many more that dependent on the Transfer Spec
 
  * **Content / Payload**
    * ["action"]() : string; single word
    * ["target"]() : nested-dictionary; only one key at root
    * ["args"]()  : nested-dictionary; multiple keys at root
    * ["actuator"](/disambiguation/actuator.md) : nested-dictionary; only one key at root
    * ["command_id"]() : string
