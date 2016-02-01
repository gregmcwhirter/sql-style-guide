A Naming Scheme for Database Tables and Fields

Robert Pittenger, MCPD-EAD, 16 Jan 2008 CPOL
 
       3.89 (25 votes)
Rate this:  
vote 1vote 2vote 3vote 4vote 5
This article presents a naming scheme for database tables and fields.
Why is There a Problem?
Database developers have historically used a somewhat cryptic system for naming database tables and fields. Originally a result of the limitations of database management systems (DBMS), these naming schemes - or the lack thereof - have been adopted by convention and tradition. However, as database applications become more complex, with more tables and larger teams of developers, and as developers come and go, it becomes much more important to implement a robust and disciplined naming scheme for database objects. A well-defined naming scheme becomes more important when you adopt object relational mapping (ORM) technologies or automatic code generation.

This article presents one scheme that has worked for me for years in multiple successful commercial applications.

Perhaps the most common practice in database naming is to use all one case (upper or lower) with words separated by underscores, and heavily abbreviated. Fixed case names have become the standard because database systems are traditional case-insensitive in table and field names (or required fixed case). It is also a holdover from academia where use of a single case is common not just in database design, but in programming languages (for example, C language is traditionally taught in lower case and FORTRAN in upper case). Separating words with underscores is a natural result of fixed case names and also is commonplace in academia. The heavy use of abbreviations is a holdover from the days of dBase and other systems where there was a stringent limit on table and field name lengths. Many database designers schooled in the older systems still retain this use of abbreviated names in a severe case of the psychological principle of learned helplessness.

Most database books still follow these principles. Consequently, even a recent book on SQL Server 2005 uses examples of tables and fields such as:

Table: customers

With fields:

cust_id
first_name
last_name
addr1
The SQL for a join on a similarly named orders table would look something like this:

Hide   Copy Code
SELECT customers.*, orders.* FROM customers INNER JOIN orders ON _
    customers.cust_id = orders.cust_id
There are several problems with such a naming scheme:

It is unnecessarily unreadable by humans and results in messy SQL statements.
The join results have two columns named cust_id - one from customers and one from orders. While this is not an insurmountable problem, it is best avoided with a proper naming scheme.
It does not lend itself well to automatic code generation programs or Object Relational Mapping (ORM) technologies such as Hibernate.

I have been developing database systems for over twelve years. My work includes custom applications, web-based database, and "shrink-wrapped" applications. I have been the lead designer for the database and had to suffer my own early mistakes. I have been forced to work with database designs developed by others - some good and some bad. And I have dug into the details of database designs for competing products in order to import their data into my company's products. Finally, I have developed an extensive set of automatic code generation tools that allows me to develop and modify robust database applications quickly.

From this experience, I have come up with a database naming system that I have used for at least five years on multiple projects and found it to be very satisfactory. Even if you don't want to adopt every feature of this naming scheme, I am confident that you will find some useful naming tips in this article.

My database naming scheme has some basic properties:

I use mixed case names.
I do not use underscores. Mixed-case makes underscores unnecessary and once you are used to omitting them, they look ugly and it is easier not to type them.
I prefix table names to indicate one of three different types of tables.
I prefix field names with a three letter code to indicate which table owns the field.
I use GUID strings as primary keys.
I will explain my reasons for each below.

Naming Tables
In most databases, there are three types of tables:

Data Tables - Certainly all tables in a database contain data, but I am using this term to refer to the tables that actually store the data for which we were motivated to build the database to begin with, such as customers, orders, or products. For example, a customers table would contain information about customers with fields such as name, address, and phone. Customers is a data table - unlike these other table types.
Link Tables - Link tables do nothing more than connect two key fields from two different data tables to form a many-to-many relationship. For example, you could have a many-to-many relationship between a vendors table and a products table because each vendor could support multiple products and each product could be sold through multiple vendors. This would require a third table to link the vendors to the products.
Picklist Tables - It is common to have tables that contain a list of choices for fields in a data table. For example, you may have a status field in your vendors table. Values for vendor status could be selected from yet another table. I refer to these types of tables as "picklist" tables because they allow users to pick from a list.
In my naming scheme, I like to prefix each table name with one of three prefixes to indicate the type of table. I use the following prefixes:

Data Tables - I use the prefix tbl. So remembering my rules for mixed case and no underscores, you could have the following data tables:
tblCustomer
tblOrder
tblOrderEntry
tblVendor
tblProduct
Link Tables - I use the prefix link. So to link vendors with products, you would have a table linkVendorProduct.
Picklist Tables - I use the prefix pltbl. So for vendor status, you would have a table called pltblVendorStatus. If you also have a table for customer status, you could have pltblCustomerStatus.
Advantages

I find that this table naming system has several advantages.

Obviously, it is very easy to tell from the table name what kind of data the table contains.
Every database application I have ever seen (such as Microsoft Access or SQL Server), lists your tables in alphabetical order. Using this prefix scheme causes your tables to be grouped by type when displayed alphabetically.
If you develop any kind of automatic code generation tools, it is easy to programmatically determine from the table name what kind of data the table contains. You simply check the prefix.
Singular/Plural Names

Note that in my data tables above, all table names are singular, i.e. tblCustomer rather than tblCustomers. Whether you prefer singular or plural names, you should always use one or the other consistently. I prefer singular because it seems cleaner to me.

Other Table Types

In your development, you may find it advantageous to include other table types such as:

log tables
error tables
system tables
Each of these could have their own prefix as well.

Naming Fields
One of the big problems in traditional database naming schemes is that it is common to have the same field name in two different tables that will end up joined together. While this problem is not insurmountable, it is inconvenient and should be avoided for two reasons:

When you join tables together that each contain fields of the identical name, you need to clutter your SQL statements with a bunch of AS clauses to make the resulting field names unique. Not only does this create messy SQL, but it particularly complicates the use of any kind of automated code generation tools or ORM technology.
It makes it more difficult to interpret certain error messages. For example, suppose you are using traditional naming and you get the error "Field cust_id not found." If you have multiple tables with a cust_id field, you may not immediately know which table generated the error.
I have designed my field naming scheme so that no two data tables ever have the same field name (this is not so important for link tables and picklist tables because they are rarely joined on each other). For each table name, I come up with a three-character prefix unique for each data table. For example the prefix for tblCustomer could be cus. The prefix for tblOrder could be ord. The prefix for tblProduct could be prd. Each field name begins with this prefix.

tblCustomer

cusCustomerID
cusCustomerName
tblOrder

ordOrderID
ordCustomerID
ordOrderDate
Now when I do a join on these two tables by customer ID, I do not have a duplicate field name because the customer ID field in the customers table has a different name from the customer ID field in the orders table.

Never Use Spaces in Field Names

One thing you should never do when naming fields is put spaces in your names - even if your DBMS (such as Microsoft Access) supports it. Putting spaces in table names results in horribly ugly and unreadable SQL statements. It is ironic, because DBMS vendors allowed for spaces in field names so that the tables themselves looked nicer to neophyte programmers and database developers. It works well when graphical query building tools generate the SQL behind the scenes. But once your database application becomes more complex and you become more adept at SQL, you will regret these "friendly names".

Naming Fields of Common Purpose (Notes, Status, Name, Date, etc.)

As you develop your database, you may notice that many tables will have some fields of common purpose. These are typically fields called notes, status, name, or some variation. For example, your customer table may have a notes field for notes about the customer and your order table may have a notes field for notes about the order. Using my prefix system, you may be inclined to name them this way:

cusNotes
ordNotes
You can see here how using prefixes to indicate the owner table is already convenient to distinguish order notes from customer notes on a join operation.

However, I recommend this method:

cusCustomerNotes
ordOrderNotes
The difference is subtle but very important if your data tables get mapped to objects in your program code. In my program code, I create objects that wrap the data in my data tables. But I don't carry over the field name prefixes into the program code. Those prefixes are for database naming. I use my own code naming system for coding. In the first case, my customer object would have a field Notes and my order object would have a field Notes. While programmatically there is nothing wrong with that, I have learned from experience that it is inconvenient for project-wide searches in source code. Suppose I want to find every code reference for customer notes. If I just search on the term Notes, the search results will include references for order notes and any other type of "notes" in my code. However, if I use the more descriptive naming scheme, I can search on CustomerNotes. My search results will be limited only to notes for the customer object.

It may sound minor, but as your projects grow to include hundreds of code files, naming objects to make searches easier becomes more important and you will appreciate the effort.

Field Names for Link Tables and Picklist Tables

Remember that for many-to-many relationships, you will be joining your data tables with link tables, so you don't want link tables to have the same field names as data tables. That means that your table linkVendorProduct should not have fields venVendorID and prdProductID because those field names already exist in data tables. I like to simply prefix link table fields with link so my linkVendorProduct table will have the fields:

linkVendorID
linkProductID
Likewise, I like to prefix picklist table fields with pl. Again, you don't want to have the same field name as exists in your data tables. Table plCustomerStatus will have fields:

plCustomerStatusID
plCustomerStatusValue
A Note on Hungarian Notation

Some database designers use a type of Hungarian notation - prefixing field names with a value to indicate field type, such as nCustomerID. While I swear by Hungarian notation in program code (but that is another article), it is bad for database field names for two reasons:

Field names should be type independent. In other words, you should be able to change the type of data in a field and not have it impact your SQL statements or program code in any way.
You can still have duplicate field names in two tables joined together.
Our SQL Statement Revisited

Now let's see what our customer-order join would look like with the new naming scheme.

Hide   Copy Code
SELECT tblCustomer.*, tblOrder.* FROM tblOrder INNER JOIN tblCustomer _
    ON tblOrder.ordCustomerKey = tblCustomer.cusCutomerKey
Now the result set is guaranteed to have all unique names, even if both tables have a notes field.

Primary Keys
Traditionally, primary keys in databases have been integers. There are some good reasons: Integers take up a minimal amount of space, they sort quickly, and most database systems allow for an incremental increase in the key field so that integers are consecutive and unique. Microsoft Access defaults to AutoNumber integers as primary keys, so database neophytes who get their start in with that program become accustomed to using integers as primary keys.

However, I prefer an alternative - the Globally Unique Identifier (GUID, pronounced goo-id), or more specifically, a GUID string. GUIDs can be generated by most operating systems and even some database systems such as SQL Server 2005 (which supports GUIDs as a field type). Just to clarify, GUIDs are 128-bit values guaranteed to be unique worldwide if they are created on a computer that has a network card. If they are created on a computer that does not have a network card, there is some very small chance that they will not be unique, but because they incorporate a time-stamp and random numbers, duplicate GUIDs are practically impossible if not reasonable unlikely.

Using GUIDs as primary keys has two advantages:

1. Merging Databases

The first advantage will not apply to most projects, but when it does apply, GUIDs are far superior to integers. That involves merging databases.

Suppose you sell a commercial contact management product that uses a relational database for storage. You use integers as primary keys and integers automatically increment with each new record. Every person who uses your products is going to have a customers table with customers number 1 through n. If a situation ever arises where you will want to merge two databases, it will be very difficult to do so. One of the database will have to have all of the primary keys reset. I have dealt with this exact problem. While it can be done, it is best to be avoided.

Now consider the same situation, but each primary key is a GUID instead of an integer. Everyone who uses your contact manager is going to have completely unique IDs for primary key values. Now, merging data is simply a matter of appending the records from one database to another. No key values have to change, and all relationships between tables remain intact.

I have developed a number of commercial database applications. Using GUIDs makes it easy for customers to merge data from different databases - something that does happen - particularly when upgrading from one database version to another.

Such a scenario may be uncommon for one-off database projects such as a custom inventory management application for a program vendor. But what if there is a corporate merger in the future? Any database with GUIDs as primary keys is much easier to integrate with other systems provided that the other systems also support GUIDs as primary keys (mixing GUIDs and integers as primary keys in the same database in undesirable).

2. Knowing the Primary Key Value Before Adding the Record

Even if the first situation doesn't apply to you, this one certainly will.

Here is a common scenario: You use integers as primary keys and the integers are assigned by the DBMS at the time the record is added. Now you want to add a new customer to the customers table, and a new order to the orders table with a foreign key back to the customer ID. The steps involved include:

Write the customer data to the database.
Find the new customer record and find out what integer key value the DBMS assigned it. Read that value.
Set the customer ID value in the order object and write the order to the database.
The problem is step 2. Using GUIDs, we can eliminate this read operation because we assign the customer ID value - a GUID - to the customer before we write the customer to the database. Now the process is:

Generate a GUID and assign it to the new customer. Write the customer data to the database.
Assign the GUID as the customer ID foreign key in the order table and write the order to the database.
We have eliminated the search-and-read step to find out what ID the DBMS has assigned to the customer.

Prefixed GUID Strings

Using GUIDs can help during that period of development of any database product, where the database table structure is continually changing and the code is not always in synch with the database.

Consider our link table linking vendors to products in a many to many relationship. If you use integers as primary keys, opening that table will just show two columns of integers - one columns of vendor IDs and one column of product IDs - or so you would think!

As I said, I like to use GUID strings rather than actual GUIDs, so my primary key fields are actually of type text. The difference is that a GUID is actually a binary value, whereas the string is actually the string representation of the binary GUID. A GUID string looks something like this:

005bf43a-01ca-454a-996c-2c73621ceeb6

Recall that in our data table naming convention, I like to prefix field names with a three character value to indicate the table, e.g. "cus" for tblCustomers. I also like to prefix my GUID string with the same three-character code. Therefore, my customer primary key would look like:

CUS:005bf43a-01ca-454a-996c-2c73621ceeb6

(By my own convention, I like to use upper case for the prefix and I separate the prefix from the GUID itself with a semi-colon).

Why is this important? Because now any time I see a primary key value, I can immediately tell what data table it is from. That is not possible with integers. Now when I open my vendor-product link table, I can easily check to make sure that all the foreign keys in the vendor column are prefixed with VEN and that all of the foreign keys in the product column are prefixed with PRD. This has proven very helpful during development and debugging of the various database applications I have worked on. (It is true that setting proper referential integrity between tables would eliminate this problem, but during the development period when the table structure is continually changing, it is not unusual for referential integrity to be relaxed).

Using a GUID string has the advantage that you can tag it with other diagnostic values. Perhaps you would append a D to the string if it was created in debug mode or P if the record was created in production mode. This would make it easy to make sure your debug data did not get mixed with production data - or to at least know which is which. Or you could append an I if the database record was created by importing from another database or from a previous version of the product.

Web Database Key Values in the Browser Bar

If you ever write a database application for the Web, one thing you probably want to avoid is to use consecutive integers as your primary key values. This is because most database-driven web sites at some point display a key field in the address bar of the web browser. Any enterprising hacker can simply increment or decrement the key value and potentially see data that they shouldn't. By using GUIDs as primary keys, it is much more difficult for the hacker to penetrate the inner workings of the site by this method. It is still not completely secure, but it is an improvement over the integer method.

You Can Never Run Out of GUIDs

GUIDs have a major advantage over integers as primary keys in that there is an unlimited supply of GUIDs. Certainly, the limitation on the number of available integers isn't likely to cause a problem in your products or vendors tables - few companies have over 4 million products (maybe Amazon.com?). However, if you are using a database table to log web page views, the limitation on the number of integers may well be a problem five years from now.

Disadvantages of GUIDS

Use of GUIDs as primary keys does have some disadvantages, so they may not be right for every application.

GUID Length - Note that a GUID string is always 36 characters in length. Add three more characters for the prefix and 1 for the colon and that means that your primary key field is a character string of length 40. Add any additional space for any other tags you may want, and the disadvantage of GUIDs becomes apparent - Performance!

Ideally, primary keys should be small and sort quickly for faster performance. That is what makes integers so attractive. However, the advantages of GUIDs may offset the performance hit.

Readability - GUIDs are ugly to look at. While integers are messy in that they don't tell you much about the object, during the development process, it is much easier to tell a colleague that there is a problem with customer number 3452 than to tell him that there is a problem with customer CUS:005bf43a-01ca-454a-996c-2c73621ceeb6. In my development, GUIDs have proved somewhat inconvenient when trying to figure out why the application cannot find customer CUS:005bf43a-01ca-454a-996c-2c73621ceeb6 or trying to find the corresponding foreign key value in a database. It can be done, but looking at columns and rows or GUIDs for any length of time can give you a serious headache!

Creating GUIDs in Code

If you decide to use GUID strings as primary keys, you will want to know how to generate them in your code.

Creating GUIDs in .NET

Creating GUIDs in .NET is very simple. Here is an example in C#.

Hide   Copy Code
System.Guid guid = System.Guid.NewGuid();
string s = guid.ToString();
Creating GUIDs in C++ with MFC

Creating GUIDs in C++ with MFC is a little more complex. Here is the code I use with support for both ANSI and Unicode:

Hide   Copy Code
CString GetGuidString()
{
  GUID guid;
  CoCreateGuid(&guid);
#ifdef _UNICODE
  WORD * str;
  UuidToString((UUID*)&guid, &str);
  CString s((LPTSTR)str);
  RpcStringFree(&str);
  return s;
#else
  BYTE * str;
  UuidToString((UUID*)&guid, &str);
  CString s((LPTSTR)str);
  RpcStringFree(&str);
  return s;
#endif
}
For C++, you will need to link to RPCRT4.LIB or you will get a linker error on the Rpc functions.

Sorry VBer's. Those are the only two languages I work with.

Naming Queries and Stored Procedures
Using prefixes for your stored procedures (called queries in Microsoft Access) in your DBMS can also be helpful. I like to use a prefix that indicates the type of stored procedure:

i for INSERT commands
s for SELECT queries
d for DELETE commands
u for UPDATE commands
Such a naming scheme has several advantages. First, you can quickly tell what type of stored procedure it is by the name. Second, since most DBMS's list your stored procedures alphabetically, they will be listed in groups based on the type.

Any stored procedure that I am not actually using, I like to prefix with an x. When stored procedures are displayed alphabetically, the ones I am not using are displayed at the bottom of the list.

Using some distinct naming scheme for stored procedures isn't just helpful when working in your DBMS, but it also allows you when looking through program code to quickly determine that you are "opening" a stored procedure rather than a table. Again it can be particularly helpful when doing global searches in program code. Suppose in my .NET application, I want to find all program code where I execute a DELETE stored procedure. Because I have prefixed all the names with d, I can do a search for:

Hide   Copy Code
new OleDbCommand("d
Considerations for ORM Technologies and Automatic Code Generation
Look through the program code for a database application and one thing will stand out - wherever there is database access, you see pretty much the same code logic over and over. Database programming is incredibly repetitive and can be very tedious. If you haven't considered either ORM or automatic code generation technologies for your database applications, I think it is well worth the effort.

ORM technologies allow you to easily map objects in your program code to database tables. The ORM package handles much of the database activity of reading and writing records and automatically populates your object with data from the database. I have used NHibernate - the .NET version of the popular Hibernate product with mixed results. It simplified a lot of the details of .NET database programming (I find ADO.NET to be very verbose), but when it didn't work right, the problems could be difficult to diagnose.

Automatic code generation takes this a step further. An automatic code generator is a software program that actually writes program code - such as an object class - based on your database design. I have been developing automatic code generation tools for years and the programs have gotten pretty sophisticated. You can see an early version of my work in my CodeProject article C++ Object Oriented Database Generator. Although they are a fair amount of work to develop, once you have them, the time savings are significant. They also produce more robust software. Once you get your generator to produce bug-free code, it will always be bug-free. If bugs are found, you can easily fix the code generator and regenerate all of your classes. It is also easy to add features to your objects.

The use of code generation tools goes hand-in-hand with a well developed database naming convention. Your code generation tools can use table and field names to determine what types of code to generate. For example, by naming picklist tables with a different prefix than data tables, your code generator can know which tables are which and write code for different class objects.

License
This article, along with any associated source code and files, is licensed under The Code Project Open License (CPOL)