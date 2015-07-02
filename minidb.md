# Mini database

Write an HTTP server that implements an in-memory key/value store where each key/value can be
locked by a user. It should have the following endpoints:

## `POST /reservations/{key}`

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

## `POST /values/{key}/{lock_id}?release={true, false}`

Attempt to update the value of `{key}` to the value given in the `POST` body according to
the following rules:

- If `{key}` doesn't exist, return `404 Not Found`
- If `{key}` exists but `{lock_id}` doesn't identify the currently held lock, do no action and respond immediately with `401 Unauthorized`.
- If `{key}` exists, `{lock_id}` identifies the currently held lock and `release=true`,
set the new value, release the lock and invalidate `{lock_id}`. Return `204 No Content`
- If `{key}` exists, `{lock_id}` identifies the currently held lock and `release=false`,
set the new value but don't release the lock and keep `{lock_id}` value. Return `204 No Content`

## `PUT /values/{key}`

If `{key}` already exists, wait until it's available then acquire the lock on it. If it doesn't already exist, create it and immediately acquire the lock on it (that operation should never block).

Once the lock is acquired and the key exists, set the key's value to the data in the `PUT` body regardless of whether it existed before.

In both cases, return the new lock ID like this:

```json
{
  "lock_id": "abc"
}
```

This endpoint should always succeed and return `200 Ok`.
