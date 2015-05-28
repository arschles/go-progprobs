# Go programming problems

## Image server

Implement an HTTP server with the following routes:

- `/generate/png/{width_px}/{height_px}` - returns a black `image/png` that's
of the specified width and height
- `/generate/jpg/{width_px}/{height_px}` - returns a black `image/jpeg` that's
of the specified width and height
- `/stats` - returns `application/json` with the number of images generated
since the server was started and the average width and height of those images.

For the `/stats` route, the json should look like this:

```json
{
  "num_images": 1,
  "average_width_px": 123,
  "average_height_px": 456
}
```

## Peer to peer chatter

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

## Mini database

Write an HTTP server that implements a key/value store where each key/value can be
locked by a user. It should have the following endpoints:

### `POST /reservations/{key}`

Wait for `{key}` to be available, then acquire a lock on it (and its value).
Return the value of `{key}` along with a unique lock ID that the caller can use in later calls.
The response body should be `application/json` in the form

```json
{
  "value": "something",
  "lock_id": "something_else"
}
```

### `POST /values/{key}/{lock_id}?release={true, false}`

Set the value of `{key}` to the value given in the `POST` body if `{lock_id}` identifies the currently held lock.

If `{lock_id}` doesn't identify the currently held lock, do no action and respond immediately with `401 Unauthorized`.

Otherwise:

- If `release` was `true`, respond `204 No Content` and release the lock. After the response, `{lock_id}` will be invalid
- Id release was `false`, respond `204 No Content` but don't release the lock. After the response,
`{lock_id}` will still be valid
