# How to test docker-compose?

## Step 1. Create docker-compose.yml with following content:

```
version: '3'
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
  db:
    image: postgres:latest
    environment:
      POSTGRES_PASSWORD: test123
    ports:
      - "5432:5432"
  psql:
    image: postgres:latest
    command: "sleep infinity"
    environment:
      POSTGRES_PASSWORD: test123
    links:
      - db
```

## Step 2. Bring up the apps.

1. Open a terminal in the directory where the compose file is located
2. Run the command `docker-compose up`. This will start the containers defined in the compose file and attach to their logs.

## Step 3. Test nginx.

Access `http://localhost` to verify the nginx container is running.

## Step 4. Test PostgreSQL.

Connect to the PostgreSQL database by using the host localhost, port 5432, username postgres, password mysecretpassword.

1. Open a terminal while the above container is still running.
2. Connect to the psql client by running `docker-compose exec psql psql -h db -U postgres`.
3. Enter the password as `test123`.
3. Verify the connection to the database by running following commands:

```
govind@thinkpad:~/rnd/docker-compose-test$ docker-compose exec psql psql -h db -U postgres
Password for user postgres: 
psql (15.1 (Debian 15.1-1.pgdg110+1))
Type "help" for help.

postgres=# CREATE TABLE test_table (
   id serial PRIMARY KEY,
   name VARCHAR NOT NULL,
   email VARCHAR NOT NULL
);
CREATE TABLE
postgres=# INSERT INTO test_table (name, email)
VALUES ('John Doe', 'govindthange@abc.com'), 
       ('Jane Doe', 'thange@xyz.com');
INSERT 0 2
postgres=# SELECT * FROM test_table;
 id |   name   |        email        
----+----------+---------------------
  1 | John Doe | govindthange@abc.com
  2 | Jane Doe | thange@xyz.com
(2 rows)

postgres=# UPDATE test_table SET name = 'John Doe Updated' WHERE id = 1;
UPDATE 1
postgres=# DELETE FROM test_table WHERE id = 2;
DELETE 1
postgres=# /q
postgres-# DROP TABLE test_table;
ERROR:  syntax error at or near "/"
LINE 1: /q
        ^
postgres=# 
```

5. When done testing, exit the psql client by running the command `\q`.

## Step 5. Shutdown apps.

When done testing, run `docker-compose down` to stop and remove the containers and network created by the compose file.
