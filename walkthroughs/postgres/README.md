---
title: "Walkthrough: PostgreSQL"
---

Follow along with the instructor as we work with PostgreSQL

## Install

- Go here [https://postgresapp.com/](https://postgresapp.com/)
- Download and open the file
- Note: this is specifcally for Mac OS

## Follow Along as we...

- Open Postgres.app on your mac
- Create database sports;
- \l - shows list of databases
- create schema baseball;
- Create table baseball.teams (name varchar(50), description varchar(100));
- \d baseball.teams - show info about table
- Insert
- Select
- delete
- Alter table
  - ALTER TABLE baseball.teams ADD COLUMN id integer PRIMARY KEY;
- Constraints
- Not null
- Primary key
- Foreign key
- Sequence
- CREATE SEQUENCE baseball.teams_id_seq START 10;
- DEFAULT nextval('baseball.teams_id_seq');
- Indexes
- Created automatically with Primary Key and Unique constraints
- You can also add an index manually
