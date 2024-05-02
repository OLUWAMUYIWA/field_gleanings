

### How to deduplicate a redshift table
    -- First identify all the rows that are duplicate
    CREATE TEMP TABLE duplicate_saleids AS
    SELECT saleid
    FROM sales
    WHERE saledateid BETWEEN 2224 AND 2231
    GROUP BY saleid
    HAVING COUNT(*) > 1;
    
    -- Extract one copy of all the duplicate rows
    CREATE TEMP TABLE new_sales(LIKE sales);
    
    INSERT INTO new_sales
    SELECT DISTINCT *
    FROM sales
    WHERE saledateid BETWEEN 2224 AND 2231
    AND saleid IN(
         SELECT saleid
         FROM duplicate_saleids
    );
    
    -- Remove all rows that were duplicated (all copies).
    DELETE FROM sales
    WHERE saledateid BETWEEN 2224 AND 2231
    AND saleid IN(
         SELECT saleid
         FROM duplicate_saleids
    );
    
    -- Insert back in the single copies
    INSERT INTO sales
    SELECT *
    FROM new_sales;
    
    -- Cleanup
    DROP TABLE duplicate_saleids;
    DROP TABLE new_sales;
    
    COMMIT;
