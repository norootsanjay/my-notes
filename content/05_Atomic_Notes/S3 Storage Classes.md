AWS offers a range of Storage Classes to avoid overpaying by selecting the most suitable option based on your data access frequency and performance needs.

| #   | Storage Classes         | Description                                                                                                                                                     |
| --- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | S3 Standard             | - Default Storage Class<br>- Frequently accessed Data<br>- Immediate access to data<br>- No min storage Days                                                    |
| 2   | S3 Standard IA          | - Infrequently accessed data<br>- Immediate access to data<br>- Min storage duration = 30 days                                                                  |
| 3   | S3 Intelligent Tiering  | - Moves data between Standard and IA based on usage<br>- Immediate access to data<br>- Min Storage Duration = 30 days                                           |
| 4   | S3 One Zone IA          | - Infrequently accessed data stored in one zone<br>- Immediate access to data<br>- Min Storage duration = 30 days                                               |
| 5   | S3 Glacier              | - Archiving data<br>- No immediate access to data<br>- Retrieval Options: Expedited (1-5min), Standard (3-5h), Bulk (5-12h)<br>- Min Storage Duration = 30 days |
| 6   | S3 Glacier Deep Archive | - Archiving data<br>- No immediate access to data<br>- Retrieval Options: Standard (within 12h), Bulk (within 48h)                                              |


| Storage Class           | Durability            | Availability Zone(s) | Access Speed      | Min Duration | Min Object Size | Retrieval Fee            | Use Case                                                  |
| ----------------------- | --------------------- | -------------------- | ----------------- | ------------ | --------------- | ------------------------ | --------------------------------------------------------- |
| S3 Standard             | 99.999999999% (11 9s) | 3+ AZs               | Milliseconds      | None         | None            | No                       | Frequently accessed data (default class)                  |
| S3 Standard-IA          | 99.999999999%         | 3+ AZs               | Milliseconds      | 30 days      | 128 KB          | Yes                      | Infrequent access but needs fast retrieval                |
| S3 One Zone-IA          | 99.999999999%         | 1 AZ                 | Milliseconds      | 30 days      | 128 KB          | Yes                      | Infrequent data not needing multi-AZ resilience           |
| S3 Glacier Instant      | 99.999999999%         | 3+ AZs               | Milliseconds      | 90 days      | 128 KB          | Yes                      | Archival data with fast access requirements               |
| S3 Glacier Flexible     | 99.999999999%         | 3+ AZs               | Minutes to Hours  | 90 days      | 40 KB           | Yes                      | Cold archive with flexible retrieval time                 |
| S3 Glacier Deep Archive | 99.999999999%         | 3+ AZs               | Hours (up to 48h) | 180 days     | 40 KB           | Yes                      | Long-term archive; lowest cost; slowest retrieval         |
| S3 Intelligent-Tiering  | 99.999999999%         | 3+ AZs               | Auto-Tiered       | None         | None            | No (if in frequent tier) | Unpredictable access patterns with auto cost optimization |
![[Pasted image 20250808014044.png]]
