# ASR9k mac-move police-mode

To avoid impact on network processors \(NP\) during high MAC moves by limiting the MAC moves, use the `hw-module mac-move police-mode` command in the appropriate mode.

MAC moves are policed to avoid stress and impact on NPs during high mac move situations such as the bridge loop. The negative on this are cases where another device fails-over, and sends a packet to move MAC tables but does not send continuous traffic. In some cases, the MAC move can be dropped and tables not updated until the device sends another packet. The new MAC move police mode \(mode on\) solves these issues.

```text
hw-module mac-move police-mode on|off
```

**on** — forces NP to utilize the new MAC move control approach. There is no MAC move policing when traffic load on NP is low. Start MAC move policing when NP is in risk of dropping traffic, congestion when the default policing is done at 1000 per second.

**off** — forces NP go back to default mode. MAC move policing is done always at 1000 per second. This is the default mode.

