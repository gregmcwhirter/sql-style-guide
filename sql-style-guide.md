SQL Naming Conventions and Style Guide

This document defines my standard for SQL naming conventions and source code formatting that I created for my software engineers. Although it is a mandatory standard for them, I thought I’d post it for the benefit of others in case anyone finds value in it :)

Document conventions:

Any text surrounded by less-than and greater-than brackets, i.e. “<”
and “>”, indicates project specific data that is populated into
a given template. e.g. <your code here>
Italicized text indicates comments to the reader
Preformatted, monospaced text indicates a code block.
e.g. in bold text indicates an example.
SQL Naming Conventions

When naming database components, the author must use the following naming standards:

When having logical noun/verb language separations, component names will
be separated by the underscore character (‘_’) to indicate the separation.
e.g. A column storing a user’s date of birth would be named
date_of_birth and a column storing time elapsed in seconds would
be named time_elapsed_in_seconds.

Table names will be defined in the plural. This is because a table is itself
a collection of 1 or more row entities (plural), and this naming convention
is also better understood in the object oriented world.
e.g. A table storing records of customer location addresses
would be named customer_location_addresses (rather than customer_location_address)
and a table storing records of dynamic event properties would be named dynamic_event_properties
(rather than dynamic_event_property).

Tables whose primary purpose are for defining Many to Many relationships
must conform to the following naming convention:
<singlular table name of entity Agt;_<singular
table name of entity B>_maps

e.g. An artist might sing many different songs, and a song might be sung by many different artists. In an RDBMS we would have an artists table and a songs. The mapping table that defines the relationship of a many songs to many users and vice versa would be named:

artist_song_maps

From this name only, we are immediately able to visually determine that this
table is primarily used to define a many-to-many relationship, and that the entities
mapped are artist to song (and vice versa). Although the related tables are
written in the singular, the table is actually a collection of mappings between
the two. This is why we suffix the table with _maps, which conforms to the
plural name requirement defined in #2 above.

If such a table is not referenced externally by another table, it’s primary
key should be a composite key between the id’s of the referenced columns of
the two joined tables. If it will be referenced externally by another table,
it is probably easier to add a single column primary key (e.g. a long) to
make creation of foreign keys in other tables easier.

Full example:

create table artist_song_maps (
    artist_id  long constraint artist_song_maps_artist_id_fk references artists(id),

    song_id  long constraint artist_song_maps_song_id_fk references songs(id),

    constraint artist_song_maps_pk primary key (artist_id, song_id)
);
    
Foreign key column names will be defined in the singular if the foreign
key references a single column. Although the table name of the foreign table
is in the plural, the key reference is to an individual row, therefore, the
local column name should reflect this singularity.
e.g. a column in table users that references
an address in the addresses table would be defined like the following:
address_id long constraint <constraint_name> references addresses(address_id)


    

ALL constraints except a primary key constraint must be named according
to the following naming convention:
<tablename>_<columnname>_<suffix>

e.g. a user’s table, with a column that references an address_id
in another table would name the foreign key constraint: users_address_id_fk


    The suffixes allowed are defined in #7 below.

A primary key constraint must be named according to the following naming
convention:
<tablename>_<suffix>.

The suffix for a primary key is pk, as defined below.

Constraint names must be suffixed by character codes to indicate the type
of constraint it is. The character codes are defined in the following table:
If the constraint is a: it’s name must be suffixed with:
primary key _pk
foreign key _fk
check   _ck
not null    _nn
unique  _uq
index   _idx
Space Formatting Standards

Tabs are forbidden.
Lines may not be extended past 80 characters.
Indents must be 4 spaces for column names and table wide constraint definitions.
Column definitions must be separated by 1 blank line.
Column types declarations must be on the same line as the column name declaration.
Column types, inline column constraints, and table-wide constraint names
must be declared 2 spaces after the longest column name in the table (see
the Table Template below).
Inline column constraint definitions (primary keys, foreign keys, unique,
etc) must be declared on the following line after the column type.
Long column constraint definitions that don’t fit within an 80 character
viewing width must extended to the next line, indented 4 spaces further than
the where the column definition began (see the example below).
Comments

All comments must use the sql standard double-dash method (--),
even if the database supports c-style comments (/* ... */). This
is because of portability across other databases, as not all db’s support
c-style comments.
All Tables must be commented.
All Views must be commented.
Table and View comments are to exist immediately preceding the table or
view defintion, aligned with the first character of the definition.
Column comments are optional, but encouraged. They should be used whenever
there is a possibility it might help in understanding.
Column comments must be declared immediately preceding the column definition,
aligned with the first character of the column type and/or column constraints.
Table Template

The following table definition is a template for how all table declarations
should be visually defined.

create table <table name and table options>
(
                        -- A column comment
    <column name>       <column type>
                        <a column constraint definition for this column>
                        <a long column constraint definition for this column
                            that is continued on another line>,

                        -- Another column comment
                        -- that extends more than one line
    <long column name>  <a column type>,

                        -- Column comment
    <a column name>     <another column type>

                        <another column constraint definition>,

                        -- Constraint comment here
    constraint          <table wide constraint name> <constraint definition>

                        -- Constraint comment
    constraint          <a long table wide constraint name with a long definition
                            that continues on another line>
);
Code Examples

The following table definitions conform to all standards specified in this
document:

create table objects
(
    id    char(36)
          constraint objects_pk primary key,

    name  varchar(100)
          constraint objects_name_nn not null
);

create table object_1_object_2_maps
(
    object_1_id       char(36)
                      constraint object_1_object_2_maps_long_table_1_id_fk
                          references objects(id),

    object_2_id       char(36)
                      constraint long_table_1_long_table_2_maps_long_table_1_id_fk
                          references objects(id),

    long_column_name  varchar(255)
                      constraint object_1_object_2_maps_some_long_column_name_nn not null
                      default 'some string',

    constraint        long_table_1_long_table_2_maps_pk
                          primary key(long_table_1_id, long_table_2_id)

);