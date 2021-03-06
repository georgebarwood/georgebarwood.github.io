<h1>Manual</h1>
<p>This manual describes the various SQL statements that are available. Where syntax is described, optional elements are enclosed in square brackets.

<h2>Schema definition</h2>
<h3>CREATE SCHEMA</h3>
<p>CREATE SCHEMA name
<p>Creates a new schema. Every database object (Table,View,Procedure,Function) has an associated schema. Schemas are used to organise database objects into logical categories.

<h2>Table definition</h2>
<h3>CREATE TABLE</h3><p>CREATE TABLE schema.tablename ( Colname1 Coltype1, Colname2 Coltype2, ... )
<p>Creates a new base table. Every base table is automatically given an Id column, which auto-increments on INSERT ( if no explicit value is supplied).<p>The data types are as follows:
<ul>
<li>tinyint, smallint, int, bigint : signed integers of size 1, 2, 4 and 8 bytes respectively.</li>
<li>float, double : floating point numbers of size 4 and 8 bytes respectively.</li>
<li>decimal(p,s) : a number with p decimal digits, with s digits after the decimal point. The maximum value of p is 18.</li>
<li>string : a string of unicode characters.</li>
<li>binary : a string of bytes.</li>
<li>bool : boolean ( true or false ).</li>
</ul>

<p>Each data type has a default value : zero for numbers, a zero length string for string and binary, and false for the boolean type. The variable length data types are stored in special system tables, and are automatically encoded so that only one copy of a given string or binary value is stored.
<h3>ALTER TABLE</h3>
<p>ALTER TABLE schema.tablename action1, action2 .... <p>The actions are as follows:
<ul>
<li>ADD Colname Coltype : a new column is added to the table.</li>
<li>RENAME Colname TO NewColname : the column is renamed.</li>
<li>MODIFY Colname Coltype : the datatype of an existing column is changed. The only changes allowed are between the different sizes of integers, between float and double, and decimals with the same scale.</li>
<li>DROP Colname : the column is removed from the table.</li>
</ul>

<h2>Data manipulation statements</h2>
<h3>INSERT</h3>
<p>INSERT INTO schema.tablename ( Colname1, Colname2 ... ) VALUES ( Val1, Val2... ) [,] ( Val3, Val4 ...) ...
<p>The specified values are inserted into the table. The values may be any expressions ( possibly involving local variables or function calls ).

<p>INSERT INTO schema.tablename ( Colname1, Colname2 ... ) select-expression
<p>The values specified by the select-expression are inserted into the table.
<h3>SELECT</h3><p>SELECT expressions FROM source-table [WHERE bool-exp ] [GROUP BY expressions] [ORDER BY expressions]
<p>A new table is computed, based on the list of expressions and the WHERE, GROUP BY and ORDER BY clauses.
<p>If the keyword DESC is placed after an ORDER BY expression, the order is reversed ( descending order ).
<p>The SELECT expressions can be given names using AS.
<p>source-table can be a named base table, a view or another SELECT enclosed in brackets.
<p>When used as a stand-alone statement, the results are passed to the code that invoked the batch, and may be displayed to a user or sent to a client for further processing and eventual display. 
<p>See the web schema for stored procedures that can be used to generate http responses.
<h3>UPDATE</h3><p>UPDATE schema.tablename SET Colname1 = Exp1, Colname2 = Exp2 .... WHERE bool-exp
<p>Rows in the table which satisfy the WHERE condition are updated.
<h3>DELETE</h3><p>DELETE FROM schema.tablename WHERE bool-exp
<p>Rows in the table which satisfy the WHERE condition are removed.

<h2>Local variable declaration and assignment statements</h2>
<h3>DECLARE</h3><p>DECLARE name1 type1, name2 type2 ....
<p>Local variables are declared with the specified types. Note that the precision makes no difference, tinyint, smallint, int and bigint are all equivalent in this context. The variables are initialised to default values ( but only once, not each time the DECLARE is encountered if there is a loop ).
<h3>SET</h3>
<p>SET name1 = exp1, name2 = exp2 .... [ FROM table ] [ WHERE bool-exp ] [ GROUP BY expressions ]
<p>Local variables are assigned. If the FROM clause is specified, the values are taken from a table row which satisfies the WHERE condition. If there is no such row, the values of the local variables remain unchanged.
<h3>FOR</h3><p>FOR name1 = exp1, name2 = exp2 .... FROM table [ WHERE bool-exp ] [ GROUP BY expressions ] [ORDER BY expressions] Statement
<p>Statement is repeatedly executed for each row from the table which satisfies the WHERE condition, with the named local variables being assigned expressions which depend on the rows.

<h2>Control flow statements</h2>
<h3>BEGIN .. END</h3><p>BEGIN Statement1 Statement2 ... END
<p>The statements are executed in order. A BEGIN..END compound statement can be used whenever a single statement is allowed.
<h3>IF .. THEN ... ELSE ...</h3>
<p>IF bool-exp THEN Statement1 [ ELSE Statement2 ]
<p>If bool-exp evaluates to true Statement1 is executed, otherwise Statement2 ( if specified ) is executed.
<h3>WHILE</h3><p>WHILE bool-exp Statement
<p>Statement is repeatedly executed as long as bool-exp evaluates to true. See also BREAK.
<h3>GOTO</h3><p>GOTO label
<p>Control is transferred to the labelled statement. A label consists of a name followed by a colon (:)
<h3>BREAK</h3><p>BREAK
<p>Execution of the enclosing FOR or WHILE loop is terminated.

<h2>Batch execution</h2><p>EXECUTE ( string-expression )
<p>Evaluates the string expression, and then executes the result ( which should be a list of SQL statements ).
<p>Note that database objects ( tables, views, stored routines ) must be created in a prior batch before being used. A GO statement may be used to signify the start of a new batch.

<h2>Stored Procedures and Functions</h2>
<h3>CREATE PROCEDURE</h3><p>CREATE PROCEDURE schema.name ( param1 type1, param2 type2... ) AS BEGIN statements END
<p>A stored procedure is created, which can later be called by an EXEC statement.
<h3>EXEC</h3><p>EXEC schema.name( exp1, exp2 ... )
<p>The stored procedure is called with the supplied parameters.

<h3>Exceptions</h3><p>An exception will terminate the execution of a procedure or EXECUTE batch. EXCEPTION() can be used to obtain a string describing the most recent exception (and clears the exception string). If any exception occurs, the database is left unchanged.

<h3>THROW</h3>
<p>THROW string-expression 
<p>An exception is raised, with the error message being set to the string.

<h3>CREATE FUNCTION</h3><p>CREATE FUNCTION schema.name ( param1 type1, param2 type2... ) RETURNS type AS BEGIN statements END
<p>A stored function is created which can later be used in expressions.
<h3>RETURN</h3>
<p>RETURN expression
<p>Returns a value from a stored function. RETURN with no expression returns from a stored procedure.

<h2>Expressions</h2>
<p>Expressions are composed from literals, named local variables, local parameters and named columns from tables or views. These may be combined using operators, stored functions, pre-defined functions. There is also the CASE expression, which has syntax CASE WHEN bool1 THEN exp1 WHEN bool2 THEN exp2 .... ELSE exp END - the result is the expression associated with the first bool expression which evaluates to true.

<h3>Literals</h3>
<p>String literals are written enclosed in single quotes. If a single quote is needed in a string literal, it is written as two single quotes. Binary literals are written in hexadecimal preceded by 0x. Integers are a list of digits (0-9), decimals have a decimal point. The bool literals are true and false.
<h3>Names</h3><p>Names are enclosed in square brackets and are case sensitive ( although language keywords such as CREATE SELECT are case insensitive, and are written without the square brackets, often in upper case only by convention ). The square brackets can be omitted if the name consists of only letters (A-Z,a-z).

<h3>Operators</h3>
<p>The operators ( all binary, except for - which can be unary, and NOT which is only unary ) in order of precedence, high to low, are as follows:
<ul>
<li>*  / % : multiplication, division and remainder (after division) of numbers. Remainder only applies to integers.</li>
<li>+ - : addition, subtraction of numbers.</li>
<li>| : concatenation of strings. The second expression is automatically converted to string if necessary.</li>
<li>= != > < >= <= : comparison of any data type.</li>
<li>IN : tests whether an expression in is in a set. The set may be a list of expressions or a select expression enclosed in brackets.</li>
<li>NOT : boolean negation ( result is true if arg is false, false if arg is true ).</li>
<li>AND : boolean operator ( result is true if both args are true )</li>
<li>OR : boolean operator  ( result is true if either arg is true )</li>
</ul>
<p>Brackets can be used where necessary, for example ( a + b ) * c.
<h3>Pre-defined functions</h3>
<ul>
<li>MIN,MAX,SUM,COUNT : these are used in conjunction with GROUP BY to calculate an aggregate value. If the value of an expression in the SELECT list varies over the grouping, but no aggregate function is specified, the result will be computed from the first input row, prior to grouping - this is probably not useful, but is not an error.</li>
<li>LEN( s string ) : returns the length of s, which must be a string or binary expression.</li>
<li>SUBSTRING( s string, start int, len int ) : returns the substring of s from start (1-based) length len.</li>
<li>REPLACE( s string, pat string, sub string ) : returns a copy of s where every occurrence of pat is replaced with sub.</li>
<li>LASTID() : returns the last Id value allocated by an INSERT statement.</li>
<li>PARSEINT( s string ) : parses an integer from s.</li>
<li>PARSEFLOAT( s string ) : parses a floating point number from s.</li>
<li>PARSEDECIMAL( s string, scale int ) : parses a decimal number from s with the specified scale. The result should be assigned to a decimal variable or table column of matching scale.</li>
<li>EXCEPTION() returns a string with any error that occurred during an EXECUTE statement.</li>
<li>See the web schema for functions that can be used to access http requests.</li>
</ul>
<h3>Conversions</h3>
<p>Any type will implicitly convert to string where required. Integers will convert to float and decimal numbers, and float and decimal will convert to each other as required. ToDo: what about conversions to integer? Truncation vs Rounding etc.

<h2>Views</h2>
<h3>CREATE VIEW</h3>
<p>CREATE VIEW schema.viewname AS SELECT expressions FROM table [WHERE bool-exp ] [GROUP BY expressions]<p>Creates a new view. Every expression must have a unique name.

<h2>Indexes
<h3>CREATE INDEX</h3><p>CREATE INDEX indexname ON schema.tablename( Colname1, Colname2 ... )<p>Creates a new index. Indexes allow efficient access to rows other than by Id values. 
<p>For example, <br>CREATE INDEX ByCust ON dbo.Order(Cust) 
<br>creates an index allowing the orders associated with a particular customer to be efficiently retrieved without scanning the entire order table.

<h2>Rename and Drop</h2>
<h3>RENAME</h3><p>RENAME object-type object-name TO object-name
<p>object-type can be any one of SCHEMA,TABLE,VIEW,PROCEDURE or FUNCTION. The name of the specified object is changed.
<h3>DROP object-type object-name</h3><p>object-type can be any one of SCHEMA,TABLE,VIEW,PROCEDURE or FUNCTION.
<p>The specified object is removed from the database. In the case of a SCHEMA, all objects in the SCHEMA are also removed. In the case of TABLE, all the rows in the table are also removed.
<h3>DROP INDEX</h3><p>DROP INDEX indexname ON schema.tablename<p>The specified index is removed from the database.

<h2>Comments</h2>
<p>There are two kinds of comments. Single line comments start with -- and extend to the end of the line. Delimited comments start with /* and are terminated by */. Comments have no effect, they are simply to help document the code.

<h2>Comparison with other SQL implementations</h2><p>There is a single variable length string datatype "string" for unicode strings ( equivalent to nvarchar(max) in MSSQL ), no fixed length strings.

<p>Similarly there is a single binary datatype "binary" equivalent to varbinary(max) in MSSQL.

<p>Every table automatically gets an integer Id field ( it does not have to be specified ), which is automatically filled in if not specified in an INSERT statement. Id values must be unique ( an attempt to insert or assign a duplicate Id will raise an exception ). 

<p>WHERE condition is not optional in UPDATE and DELETE statements - WHERE true can be used if you really want to UPDATE or DELETE all rows. This is a "safety" feature.

<p>PROCEDURE parameters are in brackets, the procedure body must be enclosed by BEGIN ... END.

<p>Local variables cannot be assigned with SELECT, instead SET or FOR is used, can be FROM a table, e.g.
<p>DECLARE s string SET s = Name FROM sys.Schema WHERE Id = schema

<p>No cursors ( use FOR instead ).

<p>Local variables cannot be assigned in a DECLARE statement.

<p>No default schemas. Schema of tables, routines, functions, views etc. must always be stated explicitly.

<p>No nulls. Columns are initialised to default a value if not specified by INSERT, or when new columns are added to a table by ALTER TABLE.

<p>No triggers. No joins. No outer references.

<h2>Guide to the pre-defined schemas</h2>
<h3>sys</h3>
<p>Has core system tables for language objects and related functions.
<h3>web</h3>
<p>Has the procedure that handles web requests ( web.main ) and other functions related to handling web requests.
<h3>handler</h3>
<p>Has handler procedures, one for each web page.
<h3>htm</h3>
<p>Has functions related to encoding html.
<h3>browse</h3><p>Has tables and functions for displaying, editing arbitrary tables in the database.
<h3>date</h3><p>Has functions for manipulating dates - conversions between Days ( from year 0 ), Year-Day, Year-Month-Day and string.
