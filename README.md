# openc2_companion

 
:exclamation: Required

 

* **Transfer-Dependent Headers** : Known as [Common Message Elements](https://docs.oasis-open.org/openc2/oc2ls/v1.0/cs02/oc2ls-v1.0-cs02.html#32-message) in Language Spec. The presence and format of these are completely dependent on the Transfer Spec. For example, the HTTPS combines content_type and msg_type into one field.
  * **content_type** : i.e. is this JSON?  example: application/openc2-cmd+json;version=1.0
  * **msg_type** : i.e. is this an OpenC2 Command or Response?
  * ... many more that are dependent on the Transfer Spec.
 
* **Content / Payload**
  * **"action"** :exclamation:  : string; single word
  * **"target"** :exclamation:  : nested-dictionary; only one key at root
  * **"args"**  : nested-dictionary; multiple keys at root
  * **"actuator"** : nested-dictionary; only one key at root. [Disambiguation](/disambiguation/actuator.md)
  * **"command_id"** : string
