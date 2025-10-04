1. Strings
2. Lists
3. Hashes
4. Sets
5. Sorted Sets

# Strings

- Basic, but powerful.
- Binary Safe.
- It can be used as generic data storage.
- If our data doesn't fit in any of the data structures, we can store it in a string data type.
- We can serialize any data into a string data type.
- It can also act as a random-access vector.
- A large amount of data can be encoded into small pieces in a string.
- The maximum size of the string is 512 MB.

## Use cases

1. Plain: Serving static content like static website pages.
2. Caching: Frequently used data can be cached as a string.
3. Counters: Store stats
4. Master Catalog/Configurations: Store configurations.

> [!Example]
> - set otp:use_100 12345 ex 120
> - get otp:use_100
> - ttl otp:use_100
> - set app:stats:daily_visitors 1000
> - get app:stats:daily_visitors
> - set app:config:title "KlickAnalytics"
> - get app:config:title
> - set app:users:types "Billable, Free"
> - set app:config:usertimeout 10000





