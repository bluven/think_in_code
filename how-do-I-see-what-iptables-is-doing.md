原文：[https://www.opsist.com/blog/2015/08/11/how-do-i-see-what-iptables-is-doing.html](https://www.opsist.com/blog/2015/08/11/how-do-i-see-what-iptables-is-doing.html)

The Linux iptables firewall is a wonderful, powerful thing. However, when it decides not to do as you want, it can be a frustrating experience to work out what’s going wrong. This article will show you how to see how iptables is processing your packets, so you can fix your firewall and get things working again.  

To do this, we’ll use a special iptables target called TRACE, which will cause every rule and policy which every matching packet touches to be logged. We’ll then examine what the logs tell us, and diagnose the problem.  

**NOTE**: All commands in this article should be run as root. Some of them will not work if you just put sudo in front of them. So, as a first step, get yourself a root shell by running sudo -i.

# Step 1: Setup iptables logging

In order to actually log something, iptables needs to be taught a couple of things about logging:

1. Load the ipt_LOG module: `modprobe ipt_LOG`
1. Tell iptables to send log messages for IPv4 packets to the ipt_LOG module: 

```
echo ipt_LOG >/proc/sys/net/netfilter/nf_log/2 
```

You only need to do this once per reboot. You can (and probably should) make this the boot-time default by adding it to your sysctl setup.

# Step 2: Trace the traffic you’re interested in 

The basic form of the command you need to run looks like this:
```
iptables -t raw -I <direction> <filters> -j TRACE 
```

First off, `<direction>` should one of:

- PREROUTING if you want to trace packets that are coming **into** the system; or
- OUTPUT if you want to trace packets that are going **out of** the system.

Every packet that gets traced will generate between three and _several hundred_ log entries. There’s no point doing all that for packets you’re not having problems with. So, we need to adjust the tracing command to only select interesting packets. That’s where the `<filters>` section comes in.

If you’ve ever used iptables directly before, the <filters> section will be quite familiar – it’s any option which restricts the packets that match the rule, such as `-s, -d, -p, --dport, --sport`, etc. If you’ve only ever used a firewall manager, like ufw, shorewall, firewalld, or what-have-you, here are some simple scenarios that should cover most basic cases. The “PARAMETERS” section of man iptables can give you more detail, should you need it.
  
## Traffic from a certain IP address
  
Say you’re working on a ticket that says the CEO’s VPN can’t connect from the hotel wifi. You’ve had her visit icanhazip.com to get her source IP (192.0.2.42), and tcpdump says the VPN packets are definitely getting _to_ the VPN server, but the VPN service isn’t seeing the connection request. Absolute _classic_ firewall problem.
  
In this case, you should trace all packets coming **into** the VPN server from her IP address (192.0.2.42). Your command will look like this:
  ```
  iptables -t raw -I PREROUTING -s 192.0.2.42 -j TRACE 
  ```
  
The <direction> is set to PREROUTING, because we’re looking at packets coming **into** the VPN server, and the <filter> is -s 192.0.2.42. The -s means “the source address of the packet”, and then we give the IP address we care about (192.0.2.42, in this example).
  
## Traffic to a certain port
  
Perhaps you’re trying to get an SMTP server running on your server, but you’re not able to connect. Running tcpdump show the packets arriving on port 25, but your SMTP server isn’t getting any connections. In this case, you’ll need to trace packets coming to 25/tcp to see why the firewall isn’t accepting the packets. Your command will look like this:
```
iptables -t raw -I PREROUTING -p tcp --dport 25 -j TRACE 
```
  
This time, we’re asking to trace packets destined for port 25 (--dport 25), and because multiple protocols have the concept of “port numbers” (TCP _and_ UDP, amongst others) we need to tell iptables that we want to trace TCP, specifically, using the -p tcp option (-p for “protocol”).
  
  
## Traffic to a certain IP address
  
Today you’re writing a script to make a connection to a remote service running on a non-standard port, 12345. The script seems fine, but you can’t manage to make a connection to the remote server. The ever-handy tcpdump tool shows no packets are even being sent out the machine’s network interface, which your extensive knowledge of iptables tells you means that the firewall is dropping the packets somewhere.
  
To trace this, you might use a command like this:
```
iptables -t raw -I OUTPUT -p tcp --dport 12345 -j TRACE 
```
  
This command is quite similar to the previous one, except this time you want to trace _outgoing_ packets, not incoming ones, so you use -I OUTPUT.
  
# Step 3: Send the traffic and collect the logs
  
Now you have the trace rule in place, you’ll need to have the traffic sent to or from the server again. Usually, this just involves doing again whatever you’re trying to get to work.
  
To make tracing through your firewall ruleset a bit easier, open up another terminal window, place it so that you can see both windows at the same time, and then run
 
```
iptables -L -n -v --line-numbers | less 
```
  
Once you’ve got your firewall rules visible, it’s time to examine the logs. In theory, you can just run dmesg and pick out the relevant log messages, but dmesg collects a _lot_ of stuff. To avoid information overload, we’ll use some simple pipelines to keep things readable.
  
  
Every packet that gets logged has an ID associated with it. All of the log entries associated with a single packet will have the same ID. (These IDs aren’t unique, as such, but they’re close enough for our purposes) To get the ID of the first packet that was traced, run this:

```
dmesg | grep 'TRACE: raw:PREROUTING:policy' | head -1 
```
  
That will give you a very, very long line, that might look something like this:

```
[1990385.729590] TRACE: raw:PREROUTING:policy:13 IN=lo OUT= MAC=00:00:00:00:00:00:00:00:00:00:00:00:08:00 SRC=127.0.0.1 DST=127.0.0.1 LEN=89 TOS=0x00 PREC=0x00 TTL=64 ID=62926 DF PROTO=UDP SPT=41467 DPT=53 LEN=69 
```
  
The important bit in there is the ID field, which in the above example is ID=62926. That’s the ID of the packet we’ll be tracing. To get all the log entries associated with that packet, run this:
```
dmesg | grep ID=62926 
```
  
(Replace the ID number with whatever was shown in **your** log entry)
  
This will give you a number of lines which, at first glance, all look like that first one – lots of long, uninteresting gibberish. Thankfully, we don’t care about most of it, so we’re going to do a bit more shell magic to just isolate out the one bit we care about:
 
```
dmesg | grep ID=62926 | cut -d ' ' -f 3 
```
  
This will produce output that might look something vaguely like this:

```
raw:PREROUTING:policy:2 
mangle:PREROUTING:rule:1 
mangle:PREROUTING:policy:1 
mangle:INPUT:rule:1 
mangle:INPUT:policy:1 
filter:INPUT:rule:18 
```
  
Phew, that looks a lot nicer!
  
This is a little terse, but it’s straightforward to understand. Each line represents a single rule which matched the packet as it traversed through the firewall ruleset. Each “part” of the message is separated by a colon.
  
The first part (raw, mangle, filter, etc), is the _table_ of the rule. There are a lot of tables inside iptables (hence the name), and you choose which table to work on with the -t (“table”) option. You will recognise this from the iptables command we ran earlier – we inserted our rule into the raw table, using the -t raw option. You may also be familiar with -t nat, which is the address translation table. The table which is most likely to be of interest when tracking down a problem in your firewall rules is the filter table, which, as the name suggests, is responsible for filtering packets.  
  
  
The second part of each line (PREROUTING, INPUT, etc) is the “chain” in which the rule resides. In the example above, all the rules are in built-in chains (by convention, built-in chains are in all-uppercase), but this can also be a user-defined chain name, which can be almost anything.  
  
  
The third part lets you know whether the rule was an explicitly defined one (rule), or a built-in chain’s “default” rule (its policy). It can also be return, to show that the packet got to the end of the user-defined chain.  
  
The final part is the line number, and it’s only interesting for rule entries. It is, as the name suggests, the line number in the chain for the rule.
To find the rule that matched, therefore, for each of the filter entries in the dmesg output, you just have to go through the output of iptables -L -n -v --line-numbers, looking for a chain with the name given in the log entry, and then go down to the matching line number. Easy!  
  
Armed with all this information, you can now trace exactly where your packets are going, and where they’re getting lost or mishandled. Your problem will probably boil down to one of a couple of common patterms:

- **Early Termination**: The packet doesn’t get to a rule you were expecting it to match, but gets dropped / rejected / whatever before it gets that far. The rule that’s dropping the packet is matching traffic it shouldn’t (perhaps you mistyped an IP address or netmask?), or perhaps you need to reorder your firewall rules to get the ACCEPT rule for the traffic you want before the DROP rule for the traffic you don’t.  
- **Failure to Match**: The packet whizzes straight past a rule you expected to handle it. Your match conditions are wrong – probably a typo.  

As with most issues, once you’ve found the problem it’s fairly easy to resolve.  
  
# Step 4: Cleanup  
  
Tracing packets takes a bit of CPU time, and it also clogs your dmesg output. Once you’re finished diagnosing your problems, you can remove the tracing rule you added earlier by finding the iptables [...] -j TRACE command you ran earlier, changing the -I to -D, and running it again. To be sure it’s gone, run iptables -t raw -L -n to see that no TRACE rules are listed, and you’re done.  
  
# Thaaaat’s all, folks!  
That’s all there is to it. To summarise:

1. modprobe ipt_LOG
1. echo ipt_LOG >/proc/sys/net/netfilter/nf_log/2
1. iptables -t raw -I <OUTPUT|PREROUTING> <filters> -j TRACE
1. iptables -L -n -v --line-numbers | less
1. dmesg | grep 'TRACE: raw:PREROUTING:policy' | head -1
1. dmesg | grep ID=????? | cut -d ' ' -f 3
1. iptables -t raw -D <OUTPUT|PREROUTING> <filters> -j TRACE

I hope this helps you to solve your firewalling problems.
