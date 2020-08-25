# openc2_companion


&#x2705; 1 OpenC2 Response in 1 HTTP Response     
&#x274C; undefined;
 
:triangular_flag_on_post:

content_type: application/openc2-rsp+json;version=1.0
Content, content_type, and msg_type are required in all Messages.
 

* **Transfer-Dependent Headers** : These are called [Common Message Elements in Language Spec](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message)
  * :small_red_triangle_down: content_type : Defined in Transfer Spec. example: application/openc2-cmd+json;version=1.0 defined in Transfer Spec.
  * :small_red_triangle_down: msg_type : Defined in Transfer spec, may not be its own field.
  * :small_blue_diamond: many more that dependent on the Transfer Spec
 
  * **Content / Payload**
    * :small_red_triangle_down: "action" : string; single word
    * :small_red_triangle_down: "target" : nested-dictionary; only one key at root
    * :small_blue_diamond: "args"  : nested-dictionary; multiple keys at root
    * :small_blue_diamond: "actuator" : nested-dictionary; only one key at root. [Disambiguation](/disambiguation/actuator.md)
    * :small_blue_diamond: "command_id" : string
