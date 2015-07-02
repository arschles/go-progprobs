# Peer to peer chatter

Write a program `pp` that can connect to N peers (other `pp` processes
listening on different ports on the same machine, specified as a command line flag on startup
for each process) to send and receive messages.

The following messages control `pp`:

- `FWD\t{msg}\t{num}\n` - forward `{msg}` as a `SUCC` to `{num}` peers that are
not the sender. if there are not `{num}` other known peers, respond with an `ERR`,
otherwise respond with a `SUCC`
- `ERR\t{msg}\n` - receive and print `{msg}` to `STDERR`
- `SUCC\t{msg}\n` - receive and print `{msg}` to `STDOUT`
- `EXIT\t{msg}\n` - receive and print `{msg}` to `STDOUT`, then exit with status code 1
