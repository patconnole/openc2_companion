# Actuator Field

### This field helps a Consumer determine if it should act on a command.

Two fundamental things to understand are that:

1. **Shotgunning Commands**: Producers may **SPAM** Consumers with commands that don't apply to those Consumers, and the Consumers need a way to know which commands are applicable to them.
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





