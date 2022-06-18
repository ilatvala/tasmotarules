# Tasmota rule for safe router reboot
My brother got a house in Spain, so he asked me to figure out a way to automatically power cycle the router if internet gets stuck.
My solution was a Shelly Plug S, flashed with tasmota.
That allowed me to set up a rule to ping a couple of IP addresses and do a power cycle after 3 failures.
Script will also start with checks every 20 minutes (power cycle would happen 1 hour after failure), but will increase in case of failure all the way to 480 minutes (one power cycle per day).
I wanted to do that in case router is updating or something and I do not want to cut power when that is happening.

Rule example is here:
```
Rule1 
  ON system#boot do Var1 20 ENDON
  ON Var1#State>480 DO Var1 480 ENDON
  ON Time#Minute|%Var1% DO Ping4 1.1.1.1 ENDON
  ON Time#Minute|%Var1% DO Ping4 8.8.8.8 ENDON
  ON Ping#1.1.1.1#Success==0 and Ping#8.8.8.8#Success==0 DO Add2 1 ENDON
  ON Var2#State==3 DO backlog Mult1 2; Var2 0; Power1 0; Delay 150; Power1 1 ENDON
  ON Ping#1.1.1.1#Success>0 or Ping#8.8.8.8#Success>0 DO backlog Var1 20; Var2 0 ENDON
```
