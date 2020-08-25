# Actuator Field

### This field enables a Consumer to determine if it should act on a command.

Two fundamental things to understand are that:

1. **Shotgunning Commands**: Producers may **SPAM** Consumers with commands that don't apply to those Consumers, and the Consumers need a way to know which commands are applicable to them.
1. **Multiple Actuator Profiles**: Consumers may implement more than one Actuator Profile.



# Example Responses

Say we have Consumers implementing the following Actuator Profile(s):

|Actuator Profile(s)| Relevant Action-Target Pair |
|-|-|
|slpf |**"deny ipv4_net"** |
|x-troublemaker | **"deny ipv4_net"**  |
|x-acme | "detonate x-acme:road_runner" |
|slpf + x-acme | ... |
|slpf + x-troublemaker | ... |

Consumer receives **"deny ipv4_net"** with different values for the Actuator field:


|             |"actuator": "" | "actuator": {"slpf".. |
|-|:-|:-|
|slpf| &#x2705; 200 OK             | &#x2705;200 OK |
|x-troublemaker|&#x2705; 200 OK             |&#x274C; Behavior **and** Response are UNDEFINED |
|x-acme| :negative_squared_cross_mark: 404; not found   |&#x274C; No work performed, but response is UNDEFINED |
|slpf + x-acme| &#x2705; 200 OK                                                          |&#x2705; 200 OK |
|slpf + x-troublemaker| &#x274C; Behavior **and** Response are UNDEFINED  |&#x2705; 200 OK |





content_type: application/openc2-rsp+json;version=1.0
Content, content_type, and msg_type are required in all Messages.
