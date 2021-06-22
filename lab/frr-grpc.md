# FRR gRPC

[FRR User Manual](https://docs.frrouting.org/_/downloads/en/latest/pdf/)

gRPC provides a combined front end to all FRR daemons using the YANG northbound. It is currently disabled by
default due its experimental stage, but it can be enabled with --enable-grpc option in the configure script.

## Northbound gRPC Features
• Get/set configuration using JSON/XML/XPath encondings.
• Execute YANG RPC calls.
• Lock/unlock configuration.
• Create/edit/load/update/commit candidate configuration.
• List/get transactions.

http://docs.frrouting.org/projects/dev-guide/en/latest/grpc.html


## Daemon gRPC Configuration
The gRPC module accepts the following run time option:
• port: the port to listen to (defaults to 50051).

Note: At the moment only localhost connections with no SSL/TLS are supported.

To configure FRR daemons to listen to gRPC you need to append the following parameter to the daemon’s command
line: -M grpc (optionally -M grpc:PORT to specify listening port).
To do that in production you need to edit the /etc/frr/daemons file so the daemons get started with the command
line argument. Example:

```bash
# other daemons...
bfdd_options=" --daemon -A 127.0.0.1 -M grpc"
```

## YANG models

[List](https://github.com/FRRouting/frr/tree/master/yang)