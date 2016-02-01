Here's how I do it:

Table names are lower case, uses underscores to separate words, and are singular (e.g. 'foo', 'foo_bar', etc.
I generally (not always) have a auto increment PK. I use the following convention: tablename_id (e.g. 'foo_id', 'foo_bar_id', etc.).
When a table contains a column that is a foreign key, I just copy the column name of that key from whatever table it came from. For example, say table 'foo_bar' has the FK 'foo_id' (where 'foo_id' is the PK of 'foo').
When defining FKs to enforce referencial integrity, I use the following: tablename_fk_columnname (e.g. furthering example 3, it would be 'foo_bar_foo_id'). Since this is a table name/column name combination, it is guaranteed to be unique within the database.
I order the columns like this: PKs, FKs, then the rest of columns alphabetically
Is there a better, more standard way to do this?

mysql naming-conventions mysql-workbench
shareimprove this question
asked Oct 26 '11 at 6:08

StackOverflowNewbie
8,69258168311
2       
Is it wrong to use for auto increment PK just "id"? Why? The name of the column have meaning only in the context of the table. So I have one "id" in every table, and may have many id_<table_name> for FK. – Zbyszek May 31 '13 at 11:25
1       
@Zbyszek I think the simplest reason against that is simply for consistency/simplicity. Rather than having id_tableB => oh no different-named column id, the consistency of id_tableB => id_tableB just looks neater... or as OP does it: foo_id => foo_id rather than foo_id => id – mmcrae Jan 26 '15 at 22:15 
add a comment
3 Answers
active oldest votes
up vote
42
down vote
accepted
I would say that first and foremost: be consistent.

I reckon you are almost there with the conventions that you have outlined in your question. A couple of comments though:

Points 1 and 2 are good I reckon.

Point 3 - sadly this is not always possible. Think about how you would cope with a single table foo_bar that has columns foo_id and another_foo_id both of which reference the foo table foo_id column. You might want to consider how to deal with this. This is a bit of a corner case though!

Point 4 - Similar to Point 3. You may want to introduce a number at the end of the foreign key name to cater for having more than one referencing column.

Point 5 - I would avoid this. It provides you with little and will become a headache when you want to add or remove columns from a table at a later date.

Some other points are:

Index Naming Conventions

You may wish to introduce a naming convention for indexes - this will be a great help for any database metadata work that you might want to carry out. For example you might just want to call an index foo_bar_idx1 or foo_idx1 - totally up to you but worth considering.

Singular vs Plural Column Names

It might be a good idea to address the thorny issue of plural vs single in your column names as well as your table name(s). This subject often causes big debates in the DB community. I would stick with singular forms for both table names and columns. There. I've said it.

The main thing here is of course consistency!