# praia-db

PostgreSQL and MySQL/MariaDB driver for [Praia](https://github.com/praia-lang/praia). Implements the database wire protocols using raw TCP sockets — no external C libraries required.

## Install

```bash
sand install github.com/viggou/praia-db
```

## Usage

### PostgreSQL

```
use 'db'

let pg = db.postgres({
    host: "localhost",
    port: 5432,
    user: "admin",
    password: "secret",
    database: "myapp"
})

let rows = pg.query("SELECT * FROM users WHERE age > ?", [18])
for (row in rows) {
    print(row.name, row.age)
}

pg.run("INSERT INTO users (name, age) VALUES (?, ?)", ["Ada", 36])

pg.close()
```

### MySQL / MariaDB

```
use 'db'

let my = db.mysql({
    host: "localhost",
    port: 3306,
    user: "admin",
    password: "secret",
    database: "myapp"
})

let rows = my.query("SELECT * FROM products")
my.run("INSERT INTO products (name, price) VALUES (?, ?)", ["Widget", 9.99])

my.close()
```

## API

Both `db.postgres()` and `db.mysql()` return a client with the same interface:

| Method | Description |
|--------|-------------|
| `client.query(sql, params?)` | Execute a query, return an array of row maps |
| `client.run(sql, params?)` | Execute a statement, return `{changes, lastId}` |
| `client.close()` | Close the connection |

Parameters use `?` placeholders and are safely escaped.

## Config

### PostgreSQL

| Field | Default | Description |
|-------|---------|-------------|
| `host` | `"localhost"` | Server hostname |
| `port` | `5432` | Server port |
| `user` | (required) | Username |
| `password` | `""` | Password |
| `database` | same as user | Database name |

Supported auth methods: trust, cleartext, MD5, SCRAM-SHA-256.

### MySQL / MariaDB

| Field | Default | Description |
|-------|---------|-------------|
| `host` | `"localhost"` | Server hostname |
| `port` | `3306` | Server port |
| `user` | (required) | Username |
| `password` | `""` | Password |
| `database` | `""` | Database name (optional) |

Supported auth: `mysql_native_password` (MariaDB default, MySQL 5.x default).

For MySQL 8+ which defaults to `caching_sha2_password`, create the user with:

```sql
CREATE USER 'user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

## Examples

See the [examples/](examples/) directory:

- `postgres_example.praia` — CRUD operations with PostgreSQL
- `mysql_example.praia` — CRUD operations with MySQL/MariaDB
- `web_api.praia` — REST API backed by PostgreSQL using Praia's router grain

## License

MIT
