# Actuator Field

### This field enables a Consumer to determine if it should act on a command.

Two fundamental things to understand are that:

1. **Shotgunning Commands**: Producers may **SPAM** Consumers with commands that don't apply to those Consumers, and the Consumers need a way to know which commands are applicable to them.
1. **Multiple Actuator Profiles**: Consumers may implement more than one Actuator Profile.



# Example Responses

Say we have the following Consumers:

|Consumer Implementing <br> Actuator Profile(s)| Relevant Action-Target Pair |
|-|-|
|slpf |**"deny ipv4_net"** |
|x-troublemaker | **"deny ipv4_net"**  |
|x-acme | **"detonate x-acme:road_runner"** |
|slpf + x-acme | ... |
|slpf + x-troublemaker | ... |

Consumer receives **"deny ipv4_net"** with different values for the Actuator field:


|             |"actuator": "" | "actuator": {"slpf".. |
|-|:-|:-|
|slpf| &#x2705; 200 OK             | &#x2705;200 OK |
|x-troublemaker|&#x2705; 200 OK             |&#x2705; 200 OK |
|x-acme| &#x2705; 501; not implemented                                                        |&#x2705; No work performed, but response is UNDEFINED |
|slpf + x-acme| &#x2705; 200 OK                                                          |&#x2705; 200 OK |
|slpf + x-troublemaker| &#x2705;Behavior **and** Response are UNDEFINED  |&#x2705; 200 OK |




&#x2705; 1 OpenC2 Response in 1 HTTP Response     
&#x274C; undefined;

content_type: application/openc2-rsp+json;version=1.0
Content, content_type, and msg_type are required in all Messages.
