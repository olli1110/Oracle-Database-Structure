# BLOCK
What is a __BLOCK__ in an __ORACLE__ database?

__ORACLE__ allocates logical database space for all data in a database. The units of database space allocation are data __blocks__, extents, and segments.

The size of a __block__ is decided when the database is initialized using __DB_BLOCK_SIZE__.
Typical sizes are: __4096__ or __8192__ bytes.

## BLOCKSTRUCTURE:

![Blockstructure](https://www.ktexperts.com/wp-content/uploads/2019/02/6.png)

## Block properties:

* __Oracle__ operates in a variable columnsize this means even though you put a value lets say VARCHAR(50) and you put in 2 characters only 2 bytes of space plus administrative information gets occupied

* When the length of such data sets get changed through __UPDATE__ the system needs to create an overflow __block__ which is very bad for the performance

* To avoid such a problem a part of the database __block__ can be defined as an overflow-area

## Entries in a blockheader:

* __Oracle__ stores information about row locks in the databaase __block__ containing the row. The structure containing the information is known as the Interested Transaction List (ITL).

* The ITL contains the ID of the transaction that has modified (and therefore locked) a row, and the ID of the row. 

* Space for the ITL in each __block__ is reserved at __block__ creation time, based on the INITRANS and MAXTRANS parameters. 

    INITRANS specifies how many "slots" are initially allocated in a __block's__ ITL.

    When the number of transactions exceeds the number of existing ITL slots, __Oracle__ will allocate space for more slots, up to the value of MAXTRANS.

# PCTFREE

The size of the free area per __block__ can be defined through the __PCTFREE__ parameter which is the area that is only used by __UPDATE__-operations.

__INSERT__-operations only fill the __block__ to the point that is specified by __PCTFREE__.


# STATIC / DYNAMIC TABLES

* __STATIC TABLES__ are tables whose data is changed rarely (master data) __PCTFREE__ should be less than 10%
* __DYNAMIC TABLES__ are tables whose data is frequently changed __PCTFREE__ should be up to 60%

# PCTUSED
Is a __block__ storage parameter that indicates at which filling level the system recognizes it as a block that can store new data sets.
It will then be put in the __free-block-list__. (default value is 40%)

# ROWS
* An __Oracle__ row is the smalles physical storage unit.

* __Oracle__ will always try to store rows whole to not split them.

* Every row starts with a __ROW HEADER__.
    The length of a column is saved in a __ROW__.

* __PRIMARY KEYS__ should be specified first in the __CREATE__ statement.
    __LONG__-data should be put last.


![ROWS](https://docs.oracle.com/database/121/CNCPT/img/GUID-7BB5F1B1-B37A-4141-8D6C-4862B63B7012-default.gif)

# FLASHBACK QUERY
In the __UNDO__ tablespace older versions of a data __block__ are stored. (before image)

When a user saves his changes using __COMMIT__ the before image is no longer needed.

Beacause of performance reasons the before image only gets deleted when new __UNDO__ tablespace is needed .

Until this happens the old data can be accessed through __FLASHBACK QUERY__.

When the user goes past the before image creation data an error occurs.

# INSERT (physical layer)
* When a new data set is created the system decides in which __block__ it gets stored
* The sets get stored until no new set fits in the data area (sets get stored historically)
* __DELETE__ operations effect is that data sets get removed and a new free area is created
* Available space = DB block size - block header - pctfree - stored data 

# UPDATE
* Every __UPDATE__ operation is basically done inside the __block__ the data set is stored
* When a data set is is increased thorugh an __UPDATE__ operation the __PCTFREE__ are is used
* Is the storage space not enough an overflow __block__ is created which was mentioned above

    Oracle then splits the sets evenly between these two __blocks__

# DELETE
The according data set gets removed from the data base __block__.
If the boundary falls below the __PCTFREE__ parameter the __block__ gets added to the free DB __block__ list so it can be taken into account for the next __INSERT__ operation.

# SUMMARY

![SUMMARY](https://www.akadia.com/img/ora_data_block.gif)