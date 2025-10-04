1. Create a user named kareem on App Server 2 in Stratos Datacenter. Set the expiry date to 2024-01-28, ensuring the user is created in lowercase as per standard protocol.
>[!Answer]
>sudo useradd -e 2024-01-28 kareem
>sudo chage -l kareem

2. Create a user named `james` with a non-interactive shell on `App Server 3`.
>[!Answer]
>sudo useradd -s /sbin/nologin james
>getent passwd james

