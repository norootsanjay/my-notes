| **Command**      | **Example**             | **Description**       |
| ---------------- | ----------------------- | --------------------- |
| `SET key value`  | `SET name "sanjay"`     | Store a value         |
| `GET key`        | `GET name` → `"sanjay"` | Retrieve value        |
| `DEL key`        | `DEL name`              | Delete a key          |
| `EXISTS key`     | `EXISTS name` → `0`     | Check if key exists   |
| `EXPIRE key sec` | `EXPIRE name 60`        | Expire in 60 sec      |
| `TTL key`        | `TTL name` → `59`       | Check remaining TTL   |
| `KEYS pattern`   | `KEYS user:*`           | Find keys with prefix |

```redis-cli
SET name "sanjay"
SET name "sanjay" EX 100
SET name "sanjay" PX 1000
GET name
INCR counter
DECR counter
APPEND name " kumar"
MSET city "Bangalore" country "India"
MGET city country

EXISTS name
DEL city
EXPIRE name 60
PEXPIRE name 3600
PERSIST name
TTL name
PTTL name
KEYS *
SCAN 0 MATCH user:* COUNT 10
```

## How Redis handles Key Expiration

- Normal keys are set wihtout any expirations.
- For expired key, Key expiration information is stored in Unix timestamp (ms).

Keys are expired in 2 ways:
1. Passive: When some client tries to access it, and the key is found to be timed out.
2. Active: Redis does 10 times per second:
	1. Test 20 random keys from the set of keys with an associated expire.
	2. Delete all the keys found expired.
	3. If more than 25% of the keys were deleted, start from step 1.

## Key Spaces

- It is similar to database namespace schemas.
- Allows you to have same key name in multiple key spaces.
- Manage kyes per each key spaces.
- Key Space index start from 0.
Commands:
- SELECT \[index]
- KEYS \[pattern]
- flushdb
You cannot link one Key space with another.

## Key Naming Convention

==ObjectId:Id==
- The max allowed key size is 512 MB.
- Redis keys are binary safe - you can use any binary sequence as a key.
- Empty key is also a valid key.

### General Rules
- Use **: `:** as a separator (namespaces).`
- Use ==lowercase== letters and meaningful words.
- Keep keys ==short but descriptive==.
- Avoid spaces, use ==snake_case== or ==kebab-case==.
- Consistent pattern = easier debugging and scanning.
- Don’t exceed ==~100 chars== in length (performance).
### Recommended Pattern

> \<application>:\<entity>:\<id>\[:\<attribute>]


> [!Examples]
> app:user:1001:name → User name  
> app:user:1001:friends → User’s friends list  
> app:order:2025:items → Order items for 2025  
> cache:product:123:details → Cached product info  

### Examples by Data Type
- **String** → app\:session\:token\:abcd1234
- **List** → chat:room:123:messages`
- **Set** → user:1001:skills
- **Sorted Set** → leaderboard:game:2025
- **Hash** → user:1001:profile  (HSET user:1001:profile name "sanjay")
- **Counter** → stats:pageviews:homepage
### Common Prefixes
- ==app==: → Application-level
- ==user==: → User-related
- ==order==: → Orders
- ==session==: → Session management
- ==cache==: → Cached data
- ==stats==: → Metrics or counters
- ==config==: → App configuration

### Bad vs Good Naming
❌ user1001name
✅ app:user:1001:name

❌ cartitems123
✅ shop:cart:123:items

### Tips
- Use **namespaces per project** (`app1:*`, `app2:*`) if Redis is shared.
- Use **hashes for structured data** instead of too many keys.
- If keys are temporary, add a **ttl suffix** → `session:abcd1234:ttl`.
- Document your key structure for your team.

## Key Patterns

> [!Example]
> - set hello 1
> - set hallo 2
> - set hrllo 3
> - set heello 4
> - set hijllo 5
> - keys *
> - keys \*ll\*
> - keys \*e\*
> - keys h\?llo
> - keys h\*llo
> - keys h\[ae\]llo
> - keys h\[x\]llo
> - keys h\[ee\]llo
> - keys h\[e\]llo
> - keys \[a-b\]llo
> - keys \[a-z\]llo
> - keys \*
> - keys user:\*
> - keys user:????
> - keys user:???:group
> - keys user:\*:group




