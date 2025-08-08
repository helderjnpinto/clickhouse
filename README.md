# clickhouse

Clickhouse docker compose and docs

#### 1. **Your SQL-created user (`my_user`) is stored in:**

```bash
/var/lib/clickhouse/access/
```

- The file:

  ```sh
  9749c435-232d-05b5-3df2-4859ff975899.sql
  ```

  contains:

  ```sql
  CREATE USER IF NOT EXISTS my_user
    IDENTIFIED WITH plaintext_password BY 'my_password';

  or

  ATTACH USER my_user IDENTIFIED WITH plaintext_password BY 'my_password';
  ```

- The `users.list` file maps `my_user` to that UUID:

  ```sh
  my_user9749c435-232d-05b5-3df2-4859ff975899
  ```

This is ClickHouse’s internal mechanism for storing SQL-created users persistently.

---

#### 2. **The file `/etc/clickhouse-server/users.d/default-user.xml` is unrelated to SQL-created users.**

That’s a static configuration file used **at startup** to define users if you're using XML config-based user management. In your case:

- You define `clickuser` there manually.
- You remove the `default` user with `<default remove="remove">`.

⚠️ These config-defined users do **not** appear in `/var/lib/clickhouse/access` and are **not manageable via SQL**.

---

### 🔧 In Summary

| Source                | User        | Management          | Persistence Location              |
| --------------------- | ----------- | ------------------- | --------------------------------- |
| XML (`users.d/*.xml`) | `clickuser` | Static, config-only | `/etc/clickhouse-server/users.d/` |
| SQL (`CREATE USER`)   | `my_user`   | Dynamic, SQL-based  | `/var/lib/clickhouse/access/`     |

Both methods are valid, but **SQL-based management** is more flexible and recommended for modern use.

---

### ✅ Next Steps You Might Want

- See all users created via SQL:

  ```sql
  SELECT * FROM system.users;
  ```

- Set permissions:

  ```sql
  GRANT SELECT ON my_database.* TO my_user;
  ```

- Change password:

  ```sql
  ALTER USER my_user IDENTIFIED WITH plaintext_password BY 'new_password';
  ```

- Remove the user:

  ```sql
  DROP USER my_user;
  ```
