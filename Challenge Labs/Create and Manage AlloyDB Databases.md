GSP395

Task 1. Create a cluster and instance

```
gcloud beta alloydb clusters create SAMPLE-CLUSTER-ID \
    --password=SAMPLE_PASSWORD \
    --network=SAMPLE_NETWORK \
    --region=GCP_REGION_VALUE \
    --project=QWIKLABS_PROJECT_ID
```

```
gcloud beta alloydb instances create SAMPLE-INSTANCE-ID \
    --instance-type=PRIMARY \
    --cpu-count=2 \
    --region=GCP_REGION_VALUE  \
    --cluster=SAMPLE-CLUSTER-ID  \
    --project=QWIKLABS_PROJECT_ID
```

Task 2. Create tables in your instance

Copy Private ``IP Cluster PSQL``

For the instance named alloydb-client, in the Connect column, click `SSH` to open a terminal window.

Set the following environment variable, replacing ALLOYDB_ADDRESS with the Private IP address of the AlloyDB instance from the previous task.

```
export ALLOYDB=ALLOYDB_ADDRESS

echo $ALLOYDB  > alloydbip.txt 
```

Use the following command to launch the PostgreSQL (psql) client. You will be prompted to provide the postgres user's password (Change3Me) which you entered when you created the cluster.

```
psql -h $ALLOYDB -U postgres
```

An example DDL command to create an AlloyDB table is as follows:

```
CREATE TABLE regions (
    region_id bigint NOT NULL,
    region_name varchar(25)
) ;
ALTER TABLE regions ADD PRIMARY KEY (region_id);
```

```
CREATE TABLE countries (
    country_id char(2) NOT NULL,
    country_name varchar(40),
    region_id bigint
) ;
ALTER TABLE countries ADD PRIMARY KEY (country_id);
```

```
CREATE TABLE departments (
    department_id smallint NOT NULL,
    department_name varchar(30),
    manager_id integer,
    location_id smallint
) ;
ALTER TABLE departments ADD PRIMARY KEY (department_id);
```

Task 3. Load simple datasets into tables

You must use the psql client to load these tables. Use the alloydb-client VM as outlined in the previous task to access the psql client.

```
INSERT INTO regions
VALUES 
    (1, 'Europe'),
    (2, 'Americas'),
    (3, 'Asia'),
    (4, 'Middle East and Africa');
```

```
INSERT INTO countries
VALUES 
    ('IT', 'Italy', 1 ),
    ('JP', 'Japan', 3 ),
    ('US', 'United States of America', 2 ),
    ('CA', 'Canada', 2 ),
    ('CN', 'China', 3 ),
    ('IN', 'India', 3 ),
    ('AU', 'Australia', 3 ),
    ('ZW', 'Zimbabwe', 4 ),
    ('SG', 'Singapore', 3 );
```

```
INSERT INTO departments
VALUES 
    (10, 'Administration', 200, 1700),
    (20, 'Marketing', 201, 1800),
    (30, 'Purchasing', 114, 1700),
    (40, 'Human Resources', 203, 2400),
    (50, 'Shipping', 121, 1500),
    (60, 'IT', 103, 1400);
```

Task 4. Create a Read Pool instance

Next create a Read Pool instance for your cluster. You may create the Read Pool instance using the Cloud Console or the gcloud CLI. If you use the glcoud CLI you must refer to and use the GCP Region value that is provided on your lab launch page.

Go to AlloyDB > Clusters

Add read pool instance yo your "lab-cluster" like the following task

```
gcloud beta alloydb instances create SAMPLE-READ-POOL-INSTANCE-ID \
    --instance-type=READ_POOL \
    --cpu-count=2 \
    --read-pool-node-count=2 \
    --region=GCP_REGION_VALUE  \
    --cluster=SAMPLE-CLUSTER-ID  \
    --project=QWIKLABS_PROJECT_ID
```

Task 5. Create a backup

On the Cloud Console Navigation menu (console_nav_small.png), under Databases click AlloyDB for PostgreSQL then Backups to launch the Backups page.

Naming like your task quest

```
gcloud beta alloydb backups create SAMPLE-BACKUP_ID \
    --cluster=SAMPLE-CLUSTER-ID \
    --region=GCP_REGION_VALUE \
    --project=QWIKLABS_PROJECT_ID
```