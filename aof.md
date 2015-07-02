# Append-only file compaction

Some databases store their data in files that they *only* append to when
they write data. Each entry in the file is never modified after it's appended,
but may be deleted after a *compaction* operation is done (see below).

## The append-only file format

For this exercise, the append-only file (AOF) has a *header* and *entries*.

### Header

The header appears at the beginning of the AOF. The first line of the header
is an integer that represents the number of lines in the header (not including that line itself).
The remaining lines map each key to the last line in the entries that mentions it.

Here's a complete header:

```
3
key1 5
key2 3
key5 10
```

This header indicates `key1` is mentioned last on line 5 of the entries, `key2` is mentioned last
on line 3, and `key5` last mentioned on line 10. Line numbers start at 0 - the first entry.

### Entries

The entries describe actions to perform on keys. The possible actions are:

- `CREATE ${key} ${value}` - create the given key with the given value
- `MODIFY ${key} ${value}` - change the given key to the given value
- `DELETE ${key}` - delete the given key and its value

In this exercise, all input AOFs are guaranteed to follow these rules:

- No key will be `CREATE`d if it currently exists
- No key will be `DELETE`d if it doesn't currently exist
- No key will be `MODIFY`-ed if it doesn't currently exist

## The compactor program

To prevent the file from growing unbounded over time, databases that use AOFs periodically
do *compactions* on the files. A compaction scans the AOF (or a portion of the AOF) and
combines all operations on each key into one command.

Write a program called `aofcompactor` that reads a file called `aof` (in the current working directory)
and outputs either a `CREATE ${key} ${value}` or a `DELETE ${key}` to `STDOUT` *immediately
after the key has been compacted*.

(Note: the `DELETE`s aren't technically necessary in many cases, but are useful when replicating the data to another AOF)

For example, given this input `aof` file:

```
4
key1 5
key2 3
key3 8
key4 6
CREATE key1 val1
CREATE key2 val2
CREATE key3 val3
MODIFY key2 val21
CREATE key4 val4
MODIFY key1 val11
MODIFY key4 val41
MODIFY key3 val33
DELETE key3 val34
```

`aofcompactor` should stream the following to `STDOUT`:

```
CREATE key2 val2
CREATE key1 val11
CREATE key4 val4
DELETE key3 val34
```

Finally, `aofcompactor` must follow these rules:

1. Read the file one line at a time (don't, for example, use Go's `ioutil.ReadAll` function)
2. Don't re-read any line in the file
