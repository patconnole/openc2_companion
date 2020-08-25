# openc2_companion

 
:small_red_triangle: Required

:small_blue_diamond" Optional

 

* **Transfer-Dependent Headers** : These are called [Common Message Elements in Language Spec](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message)
  * :small_red_triangle: content_type : Defined in Transfer Spec. example: application/openc2-cmd+json;version=1.0 defined in Transfer Spec.
  * :small_red_triangle: msg_type : Defined in Transfer spec, may not be its own field.
  * :small_blue_diamond: many more that dependent on the Transfer Spec
 
  * **Content / Payload**
    * :small_red_triangle: "action" : string; single word
    * :small_red_triangle: "target" : nested-dictionary; only one key at root
    * :small_blue_diamond: "args"  : nested-dictionary; multiple keys at root
    * :small_blue_diamond: "actuator" : nested-dictionary; only one key at root. [Disambiguation](/disambiguation/actuator.md)
    * :small_blue_diamond: "command_id" : string
