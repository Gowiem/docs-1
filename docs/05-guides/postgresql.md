# PostgresQL

## Create a read-only role

Snaplet connects to your PostgresQL database in order to create snapshots. We recommend that you give us read-only access to your database, and that you restrict connection to a specific set of IP addresses.

<div style={{textAlign: 'center'}}>

![Never cry over split milk!](/img/snappy-spilt-milk.svg)

</div>

Connect to your database and run the following SQL statements. These creates a `snaplet_readonly` user with the password `a very good password` and gives them the `snaplet_read_all_data` role.

PostgresQL v14 includes a `pg_read_all_data` role. Run `SELECT version()` in PostgresQL to determine your version.

:::warning

Change the **username** and the **password**!

:::

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CodeBlock from '@theme/CodeBlock';

<Tabs>
  <TabItem value="apple" label="PostgresQL v14" default>
    <CodeBlock language="sql">
    {`SELECT version();
-- Create a "snaplet_readonly" user and associate the "pg_read_all_data" role.
CREATE USER snaplet_readonly WITH PASSWORD 'a very good password';
GRANT pg_read_all_data TO snaplet_readonly;
`}
    </CodeBlock>
  </TabItem>
  <TabItem value="orange" label="PostgresQL v13 and below">
    <CodeBlock language="sql">
      {`SELECT version();
-- Create a "snaplet_read_all_data" role, find all the user generated schemas, 
-- and associate the correct grants.
CREATE ROLE snaplet_read_all_data;
DO $do$
DECLARE
    sch text;
BEGIN
    FOR sch IN SELECT schema_name FROM information_schema.schemata WHERE schema_name NOT LIKE 'pg_%' AND schema_name != 'information_schema'
    LOOP
        EXECUTE format($$ GRANT USAGE ON SCHEMA %I TO snaplet_read_all_data $$, sch);
        EXECUTE format($$ GRANT SELECT ON ALL TABLES IN SCHEMA %I TO snaplet_read_all_data $$, sch);
        EXECUTE format($$ GRANT SELECT ON ALL SEQUENCES IN SCHEMA %I TO snaplet_read_all_data $$, sch);
        EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON TABLES TO snaplet_read_all_data $$, sch);
        EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON SEQUENCES TO snaplet_read_all_data $$, sch);
    END LOOP;
END;
$do$;
-- Create a "snaplet_readonly" user and associate the "snaplet_read_all_data" role.
-- We give the user BYPASSRLS privileges in order to introspect the db structure.
CREATE USER snaplet_readonly WITH PASSWORD 'a very good password' BYPASSRLS;
GRANT snaplet_read_all_data TO snaplet_readonly;
`}
    </CodeBlock>
  </TabItem>
  
</Tabs>

## Grant IP address access

Snaplet uses `54.93.75.84`, `3.64.37.135` and `18.158.21.101` to connect to your database. It's a good idea to restrict all traffic to PostgresQL, and only grant access where it's absolutely required.

## Self-Signed Certificates

To make Snaplet work with servers using self-signed certificates. Please add `sslmode=require` to the database connection string:
```terminal
SNAPLET_DATABASE_URL='postgresql://<user>:<password>@<host>:<port>/<database>?sslmode=required&ssl=true&sslmode=require' snaplet snapshot restore
```

During the snapshot capture process, use `NODE_TLS_REJECT_UNAUTHORIZED=0`:
```terminal
NODE_TLS_REJECT_UNAUTHORIZED=0 SNAPLET_DATABASE_URL='postgresql://<user>:<password>@<host>:<port>/<database>?sslmode=required&ssl=true&sslmode=require' snaplet snapshot capture
```


Example:
