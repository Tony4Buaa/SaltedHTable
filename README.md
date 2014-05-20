#What is SaltedHTable?
a slater will prepend some prefix before the row, and the prefix is the hash value of the original row key, so that the row key distribution is more even to avoid hot spotting. 

#Problems

• HBase stores the data into regions.

• For the results with sequential row keys, they are inserted into one single region which could become a hotspot within a specific period. 

–	Impact the write performance.

–	For example, use the time as the row key.



#Solution: Salt the Tables

•	Salting a table is to add a prefix to row keys to spread the records with sequential row keys into regions.

–	The new row key is Prefix+RowKey.

•	Eliminate the hotspot issue.

•	Increase the throughputs.

 

#Implement

•	SaltedHTable is an implementation of the HTableInterface.

•	KeySalter is used to salt tables.

An implemented one is OneBytePrefixKeySalter, where the prefix is hash(RowKey)%buckets
 
#Sample


**Put in the HTable**

   HTable ht = new HTable(conf, tablename);
   
   Put put = new Put(row);
    
   put. add( family, qualifier, value);
    
   ht.put(put);
   

**Put in the SaltedHTable**

   HTable ht = new HTable(conf, tablename);
   
   Put put = new Put(row);
   
   put. add( family, qualifier, value);
   
   KeySalter salter = new OneBytePrefixKeySalter();
   
   SaltedHTable saltedTable = new SaltedHTable (ht, salter);
   
   saltedTable.put(put);
   

**Scan in the Htable**

HTable ht = new Htable(conf, tablename);

Scan scan = new Scan();

ResultScanner scanner = ht.getScanner(scan);

Result result = scanner.next();


**Scan in the SaltedHTable**

HTable ht = new HTable(conf, tablename);

Scan scan = new Scan();

KeySalter salter = new OneBytePrefixKeySalter();

SaltedHTable saltedTable = new SaltedHTable (ht, salter);

ResultScanner scanner = saltedTable.getScanner(scan);

Result result = scanner.next();

