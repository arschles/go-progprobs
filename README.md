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

If `{key}` doesn't exist, return `404 Not Found`.

### `POST /values/{key}/{lock_id}?release={true, false}`

Attempt to update the value of `{key}` to the value given in the `POST` body according to
the following rules:

- If `{key}` doesn't exist, return `404 Not Found`
- If `{key}` exists but `{lock_id}` doesn't identify the currently held lock, do no action and respond immediately with `401 Unauthorized`.
- If `{key}` exists, `{lock_id}` identifies the currently held lock and `release=true`,
set the new value, release the lock and invalidate `{lock_id}`. Return `204 No Content`
- If `{key}` exists, `{lock_id}` identifies the currently held lock and `release=false`,
set the new value but don't release the lock and keep `{lock_id}` value. Return `204 No Content`

### `PUT /values/{key}`

Set the value (in the `PUT` body) of `{key}` and return the new `lock_id` for the
key/value pair in `application/json` like this:

```json
{
  "lock_id": "abc"
}
```

If multiple callers race for this call, only one should succeed (the order doesn't matter)
and the others should behave as `POST /reservations/{key}` does.
