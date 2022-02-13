# CQRS in Postgres

This is an educational example of how to to use CQRS in Postgres.

### Two (or more) versions of code can run concurrently, each using their own version of tables.

This allows front end servers to upgraded one by one, as an upgrade is rolled out.  Multiple versions of code can coexist peacefully.  Usually you will only want two versions, at most.  (Support for more versions results in space wasted on multiple read-model tables.)

### The contraints of the read-model are always enforced.

One issue with some implementations of CQRS is that the read-model's constraints aren't enforced on the write-model.  This could lead to circumstances where the write-model contains data which would violate the read-model's constraints.

In this example, we keep the read and write-models in one database.  If the trigger on a write-model table fails to insert into the read-model table(s) because of constraint violations, the whole transaction will be rolled back.

e.g. You can't update the password for a user who hasn't registered.

(There may be some circumstances where this isn't the needed behaviour, but it fits most CRUD apps.)


## A Minimal Schema

We start with a minimal schema.  Command Tables (the write-model) have names which are the past-tense of actions, whereas Query Tables have names which are nouns.

```sql
CREATE TABLE user_registered_0 (
    id
    email
    password_hash
)

CREATE TABLE users_0 (
    id
    email
    password_hash
)

CREATE TRIGGER

INSERT INTO user_registered_0 VALUES (0, 'foo@example.com', '959c9f50aef1bc129a0e16564319a1b36515d570513079b6c73c72a5709abdce');
```

At this early stage, there is no difference between the read and write-models.

This database is used by code v0.

Note: These examples use explicit `id` values, to allow 


## Feature Request: Usernames

Next, we are told that we need to record a user's username at registration.  To do this, we add a new version of the `user_registered` table, to which new code will write, and a new version of the `users` table.

```sql

CREATE TABLE user_registered_1 (
    id
    email
    password_hash
    username
)

CREATE TABLE users_1 (
    id
    email
    password_hash
    username
)

CREATE TRIGGER
CREATE TRIGGER

INSERT INTO user_registered_1 VALUES (1, 'bar@example.com', '57846ba7f6f13c0454ebec6c9b31c7f52fe72afb606a8ba336170168ca0c408f');
```

At this stage, it is fine for two versions of front end code to exist, one which `writes-to`/`reads-from` to `user_registered_0`/`users_0` and another which uses `user_registered_1`/`users_1`.

There are still no differences between the write-models and the read-models.

This database is used by code v0 (which only uses the `_0` tables), and the new code v1 (which uses the `_1` tables).


## Feature Request: Changeable Passwords

Now we have users, they are forgetting their passwords and requiring them to be changed.

This is the stage at which the write-model and read-model first diverge.

```sql

CREATE TABLE password_changed_2 (
    id
    user_id
    password_hash
)

CREATE TABLE users_2 (
    id
    email
    password_hash
    username
)

CREATE TRIGGER
CREATE TRIGGER

INSERT INTO password_changes_2 VALUES (0, 1, '8aa64c9ca202961c0f6bcd34c45f95c9e0cf5555cbcacfd7f87163dad7b60d2f');
```

While `users_2` has the same shape as `users_1`, only `users_2` takes account of password changes.


## Tidying Up

We now have three versions of code running across our cluster of front end servers.  Once we are happy with our v2 code, we can shutdown the v0 and v1 front end servers and tidy the database.

We don't want to keep the v0 and v1 code alive any longer than we have to, as they do not know about password changes.

```sql
DROP TRIGGER
DROP TABLE users_0;
DROP TRIGGER
DROP TABLE users_1;
```

Notice that the write-model tables are never destroyed.  All version of the read-model aggregates rows from all equal-and-earlier versioned tables of the write-model.  There are W*R triggers, which is another reason to keep R (the number of extant read-model versions) small.


## Feature Request: Messages

Users now want to send each other messages.  We enable this with a new table.

```sql

CREATE TABLE message_sent_3 (
    id
    src_id
    dst_id
    content
)

CREATE TABLE messages_3 (
    id
    src_id
    dst_id
    content
)

TRIGGER

INSERT
```


## Rules

These are the rules I have used in these examples.

* All 
