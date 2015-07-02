# LRU Cache

Write an implementation of the below interface. Store keys and values in memory.

```go
// LRUCache is a cache with a fixed size that supports expiring keys.
// If the cache reaches its size capacity and a Put is called with a new key,
// it will evict the least recently used key/value pair to make room for the
// new value. The cache determines the "recently used" value based on the last time
// a Get or a Put call was made on the key.
//
// LRUCache also supports evictions based on a time-to-live (TTL), which is
// set when the key is inserted or updated
type LRUCache interface {
  // Get gets the value for the given string. Returns the value and true if the
  // key exists, an empty slice and false if the key doesn't exist.
  Get(string) ([]byte, bool)

  // Set sets the value for the given key and sets the key/value pair's
  // time-to-live (it will be deleted after the given duration).
  //
  // the first returned bool indicates whether the value already existed and
  // the second value indicates whether another value was evicted because of this call.
  //
  // If the key already exists, then this function overwrites both the value
  // and the time-to-live.
  Set(string, []byte, time.Duration) (bool, bool)
}
```
