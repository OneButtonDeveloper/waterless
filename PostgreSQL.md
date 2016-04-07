## psql

```
psql -h hostname -U username db-name
\d -- list tables, views, seqs
\c other-db -- connect to another db

-- Send query results to a file:
\copy (SELECT 2+2) TO '/tmp/outfile'

-- Read commands from a file:
\i /path/to/file.sql

\? -- list all commands
```

## Queries

```
SELECT name, price FROM Parts WHERE id=123 OR quantity>0 ORDER BY name DESC;

-- Average price for each vendor:
SELECT vendor_id, avg(price) from Parts
  GROUP BY vendor_id;

-- Average price for certain vendors:
SELECT vendor_id, avg(price) from Parts
  WHERE vendor_id IN (1, 2)
  GROUP BY vendor_id;

SELECT max(price), min(price) FROM Parts;
SELECT COUNT(*) FROM Parts;
SELECT * FROM Parts WHERE name LIKE 'Grey%';
```

## Transactions

```
BEGIN TRANSACTION;
-- do stuff
COMMIT;
-- or --
ROLLBACK;
```

## Joins

TODO

## JSON

```
SELECT id, name, attributes->'frob'
  FROM parts
  WHERE attributes @> '{"usefulness" : "high"}';
```

## Tables

### Common Data Types
INTEGER, BIGINT - signed 4/8 byte integers SERIAL
BIGSERIAL - auto-incrementing integers
UUID - universally unique identifier

REAL, DOUBLE - floating point number
NUMERIC - fixed point number - for money
DATE - month, day, year
TIME - time of day
INTERVAL - timespan
TIMESTAMP WITH TIME ZONE - time + date

TEXT - variable length string
VARCHAR(x) - variable length up to x
CHARACTER(x) - fixed x-length string
JSONB - compressed JSON data

### Create

```
CREATE TABLE Vendors (id SERIAL PRIMARY KEY, name TEXT UNIQUE NOT NULL);
CREATE TABLE Parts (
  id SERIAL PRIMARY KEY,
  name TEXT UNIQUE NOT NULL,
  quantity INTEGER,
  price NUMERIC,
  attributes JSONB,
  vendor_id INTEGER NOT NULL REFERENCES
  vendors(id),
  createdAt TIMESTAMP WITH TIME ZONE NOT NULL
  DEFAULT now());
```

### Alter, Drop

```
ALTER TABLE Parts ADD COLUMN description TEXT;
ALTER TABLE Parts RENAME COLUMN description TO descr;
ALTER TABLE Parts DROP COLUMN descr;
ALTER TABLE Parts ALTER COLUMN quantity SET NOT NULL;
ALTER TABLE Parts RENAME TO PartsTable;
DROP TABLE Parts;
```

### Insert, Update, Delete

```
INSERT INTO Vendors (Name) VALUES ('A Vendor'); INSERT INTO Vendors (Name) VALUES ('Other Vendor');

INSERT INTO Parts (name, quantity, price, attributes,vendor_id)
VALUES ('Grey Widget', 12, 1.23,'{"frob":12,"usefulness":"high"}',(SELECT ID FROM vendors WHERE name='A Vendor'));

UPDATE Vendors SET name='A Vendor, Inc.' WHERE NAME='A Vendor';
DELETE FROM Parts WHERE name='Grey Widget';
```
