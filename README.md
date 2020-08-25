# openc2_companion

 
:exclamation: Required

 

* **Transfer-Dependent Headers** : These are called [Common Message Elements in Language Spec](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message)
  * :exclamation: **content_type** : Defined in Transfer Spec. example: application/openc2-cmd+json;version=1.0 defined in Transfer Spec.
  * :exclamation: **msg_type** : Defined in Transfer spec, may not be its own field.
  * many more that dependent on the Transfer Spec
 
  * **Content / Payload**
    * :exclamation: **"action"** : string; single word
    * :exclamation: **"target"** : nested-dictionary; only one key at root
    *   **"args"**  : nested-dictionary; multiple keys at root
    *   **"actuator"** : nested-dictionary; only one key at root. [Disambiguation](/disambiguation/actuator.md)
    *   **"command_id"** : string
