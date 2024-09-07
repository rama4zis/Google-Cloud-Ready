GSP355

Eable Database Migration API and Service Networking API

You must install and configure the pglogical database extension on the stand-alone PostgreSQL database on the postgres-vm Compute Instance VM. The pglogical database extension package that you must install is named postgresql-13-pglogical.

Just SSH the ``postgres-vm`` and install ``postgresql-13-pglogical``

```
sudo apt install postgresql-13-pglogical
```

```
sudo su - postgres -c "gsutil cp gs://cloud-training/gsp918/pg_hba_append.conf ."
sudo su - postgres -c "gsutil cp gs://cloud-training/gsp918/postgresql_append.conf ."
sudo su - postgres -c "cat pg_hba_append.conf >> /etc/postgresql/13/main/pg_hba.conf"
sudo su - postgres -c "cat postgresql_append.conf >> /etc/postgresql/13/main/postgresql.conf"

sudo systemctl restart postgresql@13-main
```

psql
\c postgres;
CREATE EXTENSION pglogical;
\c orders;
CREATE EXTENSION pglogical;



CREATE USER migration_user PASSWORD 'DMS_1s_cool!';
ALTER DATABASE orders OWNER TO migration_user;
ALTER ROLE migration_user WITH REPLICATION;


\c postgres;
GRANT USAGE ON SCHEMA pglogical TO migration_user;
GRANT ALL ON SCHEMA pglogical TO migration_user;

GRANT SELECT ON pglogical.tables TO migration_user;
GRANT SELECT ON pglogical.depend TO migration_user;
GRANT SELECT ON pglogical.local_node TO migration_user;
GRANT SELECT ON pglogical.local_sync_status TO migration_user;
GRANT SELECT ON pglogical.node TO migration_user;
GRANT SELECT ON pglogical.node_interface TO migration_user;
GRANT SELECT ON pglogical.queue TO migration_user;
GRANT SELECT ON pglogical.replication_set TO migration_user;
GRANT SELECT ON pglogical.replication_set_seq TO migration_user;
GRANT SELECT ON pglogical.replication_set_table TO migration_user;
GRANT SELECT ON pglogical.sequence_state TO migration_user;
GRANT SELECT ON pglogical.subscription TO migration_user;


\c orders;
GRANT USAGE ON SCHEMA pglogical TO migration_user;
GRANT ALL ON SCHEMA pglogical TO migration_user;

GRANT SELECT ON pglogical.tables TO migration_user;
GRANT SELECT ON pglogical.depend TO migration_user;
GRANT SELECT ON pglogical.local_node TO migration_user;
GRANT SELECT ON pglogical.local_sync_status TO migration_user;
GRANT SELECT ON pglogical.node TO migration_user;
GRANT SELECT ON pglogical.node_interface TO migration_user;
GRANT SELECT ON pglogical.queue TO migration_user;
GRANT SELECT ON pglogical.replication_set TO migration_user;
GRANT SELECT ON pglogical.replication_set_seq TO migration_user;
GRANT SELECT ON pglogical.replication_set_table TO migration_user;
GRANT SELECT ON pglogical.sequence_state TO migration_user;
GRANT SELECT ON pglogical.subscription TO migration_user;
GRANT USAGE ON SCHEMA public TO migration_user;
GRANT ALL ON SCHEMA public TO migration_user;

GRANT SELECT ON public.distribution_centers TO migration_user;
GRANT SELECT ON public.inventory_items TO migration_user;
GRANT SELECT ON public.order_items TO migration_user;
GRANT SELECT ON public.products TO migration_user;
GRANT SELECT ON public.users TO migration_user;

ALTER TABLE public.distribution_centers OWNER TO migration_user;
ALTER TABLE public.inventory_items OWNER TO migration_user;
ALTER TABLE public.order_items OWNER TO migration_user;
ALTER TABLE public.products OWNER TO migration_user;
ALTER TABLE public.users OWNER TO migration_user;
\dt



ALTER TABLE inventory_items ADD CONSTRAINT inventory_items_pkey PRIMARY KEY (id);

ALTER TABLE inventory_items ADD CONSTRAINT inventory_items_pkey PRIMARY KEY (id);
\q
exit

GRANT SELECT ON users TO "student-01-c10fdc67bf6d@qwiklabs.net";