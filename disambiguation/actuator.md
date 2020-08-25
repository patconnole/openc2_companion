# Actuator Field

### This field allows a Consumer to determine if it should act on a command.

Two fundamental things to understand are that:

1. **Shotgunning Commands**: Producers may **SPAM** Consumers with commands that don't apply to those Consumers, and the Consumers need a way to know which commands are applicable to them.
1. **Multiple Actuator Profiles**: Consumers may implement more than one Actuator Profile.



### EXAMPLES
|Consumer Implementing <br> Actuator Profile(s)| Example Command |
|-|-|
|slpf |**"deny ipv4_net"** |
|x-troublemaker | **"deny ipv4_net"**  |
|x-acme | **"detonate x-acme:road_runner"** |
|slpf + x-acme | |
|slpf + x-troublemaker |  |


* Consumer_SLPF: **"slpf"**, including command **"deny ipv4_net"**
* Consumer_BAD: **"x-bad"**, including command **"deny ipv4_net"**
* Consumer_GOOD: **"x-good"**, including command **"locate x-good:road_runner"**
* Consumer_SLPF_GOOD: **"slpf"** + **"x-good"**
* Consumer_SLPF_BAD: **"slfp"** + **"x-bad"**

|             |"action": "deny" <br> "target": {"ipv4_net".. <br> "actuator": "" | "action": "deny" <br> "target": {"ipv4_net".. <br> "actuator": {"slpf".. |
|-|:-|:-|
|slpf| 200 OK             | 200 OK |
|x-troublemaker| 200 OK             | 200 OK |
|x-acme| 501; not implemented                                                        | No work performed, but response is UNDEFINED |
|slpf + x-acme| 200 OK                                                          | 200 OK |
|slpf + x-troublemaker| 200 OK                                                          | 200 OK |




&#x2705; 1 OpenC2 Response in 1 HTTP Response     
&#x274C; undefined;

content_type: application/openc2-rsp+json;version=1.0
Content, content_type, and msg_type are required in all Messages.
