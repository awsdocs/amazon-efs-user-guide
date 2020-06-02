# Source Ports for Working with EFS<a name="source-ports"></a>

To support a broad set of NFS clients, Amazon EFS allows connections from any source port\. If you require that only privileged users can access Amazon EFS, we recommend using the following client firewall rule\.

```
iptables -I OUTPUT 1 -m owner --uid-owner 1-4294967294 -m tcp -p tcp --dport 2049 -j DROP
```

This command inserts a new rule at the start of the OUTPUT chain \(`-I OUTPUT 1`\)\. The rule prevents any unprivileged, nonkernel process \(`-m owner --uid-owner 1-4294967294`\) from opening a connection to the NFS port \(`-m tcp -p tcp –dport 2049`\)\.