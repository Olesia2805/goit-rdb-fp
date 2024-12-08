# goit-rdb-fp

## Step 1.

**1. Created a new database named "pandemic" to store the data for the project.**

**2. The "pandemic" database was used as the default database for the project.**

**3. Refreshed the list of databases used in the project to include the new "pandemic" database.**

**4. Used the Import Wizard to import any data required for the project into the "pandemic" database.**

```sql
CREATE DATABASE IF NOT EXISTS pandemic;

USE pandemic;

SHOW DATABASES;
```

![p1_import_data](./p1_import_data.png)

---

## Step 2.

**1. The first SQL queries analyze and count the number of empty string values in various columns. This helps identify which attributes need normalization.**

**2. The second set of queries updates columns to replace `''` (empty strings) with `NULL` for easier handling.**

**3. This step creates a normalized table, `countries`, containing unique `Country_Name` and `Country_Code` pairs extracted from `infectious_cases`.**

**4. This table establishes the relationship between a `Country_ID` and a specific year. The disease statistics are added as columns, with all values normalized to reference the primary key combination (`Country_ID`, `Year`).**

**5. This step inserts data into the `diseases` table by joining `infectious_cases` with `countries` on `Country_Name`. This ensures each record is tied to a valid `Country_ID`.**

**_Key Points_**

- Replacing empty strings with `NULL` ensures proper handling in SQL operations.
- `PRIMARY KEY (Country_ID, Year)` ensures no duplicate records for the same country and year. Since the combination of `Country_ID` and `Year` is sufficient to identify a row uniquely, there's no need to introduce an artificial auto-increment ID for this table. This minimizes redundancy and simplifies the schema.

```sql
SELECT 'Entity' AS case_type, COUNT(*) AS count
FROM infectious_cases
WHERE Entity = ''

UNION ALL

SELECT 'Code' AS case_type, COUNT(*) AS count
FROM infectious_cases
WHERE Code = ''

UNION ALL

SELECT 'Year' AS case_type, COUNT(*) AS count
FROM infectious_cases
WHERE Year = ''

UNION ALL

SELECT 'Number_cholera_cases' AS case_type, COUNT(*) AS count
FROM infectious_cases
WHERE Number_cholera_cases = ''

UNION ALL

SELECT 'polio_cases', COUNT(*)
FROM infectious_cases
WHERE polio_cases = ''

UNION ALL

SELECT 'cases_guinea_worm', COUNT(*)
FROM infectious_cases
WHERE cases_guinea_worm = ''

UNION ALL

SELECT 'Number_rabies', COUNT(*)
FROM infectious_cases
WHERE Number_rabies = ''

UNION ALL

SELECT 'Number_malaria', COUNT(*)
FROM infectious_cases
WHERE Number_malaria = ''

UNION ALL

SELECT 'Number_hiv', COUNT(*)
FROM infectious_cases
WHERE Number_hiv = ''

UNION ALL

SELECT 'Number_tuberculosis', COUNT(*)
FROM infectious_cases
WHERE Number_tuberculosis = ''

UNION ALL

SELECT 'Number_smallpox', COUNT(*)
FROM infectious_cases
WHERE Number_smallpox = '';
```

![p2_count_empty_fields](./p2_count_empty_fields.png)

```sql
SET SQL_SAFE_UPDATES = 0;

UPDATE infectious_cases
SET Number_yaws = NULL
WHERE Number_yaws = '';

UPDATE infectious_cases
SET polio_cases = NULL
WHERE polio_cases = '';

UPDATE infectious_cases
SET cases_guinea_worm = NULL
WHERE cases_guinea_worm = '';

UPDATE infectious_cases
SET Number_rabies = NULL
WHERE Number_rabies = '';

UPDATE infectious_cases
SET Number_malaria = NULL
WHERE Number_malaria = '';

UPDATE infectious_cases
SET Number_hiv = NULL
WHERE Number_hiv = '';

UPDATE infectious_cases
SET Number_tuberculosis = NULL
WHERE Number_tuberculosis = '';

UPDATE infectious_cases
SET Number_smallpox = NULL
WHERE Number_smallpox = '';

UPDATE infectious_cases
SET Number_cholera_cases = NULL
WHERE Number_cholera_cases = '';

SET SQL_SAFE_UPDATES = 1;
```

![p2_fill_NULL_fields](./p2_fill_NULL_fields.png)

![p2_count_NULL_fields](./p2_count_NULL_fields.png)

```sql
CREATE TABLE countries(
    Country_ID INT AUTO_INCREMENT PRIMARY KEY,
    Country_name VARCHAR(50),
    Country_code VARCHAR(10)
);

INSERT INTO countries(Country_name, Country_code)
SELECT DISTINCT Entity, Code FROM infectious_cases;

SELECT * FROM countries;
```

![p2_create_insert_countries](./p2_create_insert_table_countries.png)

```sql
CREATE TABLE diseases(Country_ID INT,
                      Year YEAR,
                      Number_yaws FLOAT NULL,
                      Number_polio FLOAT NULL,
                      Number_guinea_worm FLOAT NULL,
                      Number_rabies FLOAT NULL,
                      Number_malaria FLOAT NULL,
                      Number_hiv FLOAT NULL,
                      Number_tuberculosis FLOAT NULL,
                      Number_smallpox FLOAT NULL,
                      Number_cholera_cases FLOAT NULL,
                      PRIMARY KEY (Country_ID, Year),
                      FOREIGN KEY (Country_ID) REFERENCES countries(Country_ID));

INSERT INTO diseases(Country_ID, Year, Number_yaws,
           Number_polio, Number_guinea_worm,
           Number_rabies, Number_malaria,
           Number_hiv, Number_tuberculosis,
           Number_smallpox, Number_cholera_cases)
SELECT DISTINCT c.Country_ID, ic.Year, ic.Number_yaws,
                ic.polio_cases, ic.cases_guinea_worm,
                ic.Number_rabies, ic.Number_malaria,
                ic.Number_hiv, ic.Number_tuberculosis,
                ic.Number_smallpox, ic.Number_cholera_cases
FROM infectious_cases AS ic
JOIN countries AS c ON ic.Entity = c.Country_Name;

SELECT * FROM diseases;
```

![p2_create_diseases](./p2_create_table_diseases.png)

![p2_insert_diseases](./p2_insert_table_diseases.png)

---
