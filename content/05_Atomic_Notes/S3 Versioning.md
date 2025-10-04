A powerful mechanism to safeguard your data by preserving, retrieving, and restoring different versions of your object.
>[!info]
>By default, versioning is disabled in S3.

- [f] Enabling versioning mitigates risks of accidental deletion or unwanted overwrites by preserving older versions of your objects.
_Versioning is a bucket-level setting—it applies to every object stored in the bucket_

---

# 3 States of Buckets

1. **Un-versioned (default):** Versioning is disabled
2. **Versioning Enabled:** New versions of objects are recorded
3. **Versioning Suspended:** Existing versions are kept, but new uploads do not receive version IDs.

- [n] Once versioning is activated, you cannot disable it; you can only suspend it. When suspended, all existing object versions are maintained, but new uploads will have a null version ID, functioning as if versioning is turned off.

---

# How Versioning Works?


When versioning is enabled, each uploaded object is assigned a unique version ID. For example, if the first version of an object is assigned "1" (in practice, S3 uses a long unique string), subsequent uploads with the same key create new versions with distinct IDs (e.g., "2", "3", etc.). The most recent version is always considered the current version. If you access the object without specifying a version ID, the latest version is returned.![[Pasted image 20250809121040.png]]
Within the S3 console, object uploads such as file1.txt are listed with their corresponding version IDs and metadata, including modification dates. This allows you to track changes over time.
- [I] ==If versioning is disabled==, the ==version ID== associated with each object ==remains null==.
