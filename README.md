# Altium-IntLib-to-Kicad_dbl-conversion
How to convert Altium integrated library (or atomic schlib/pcblib) into Kicad database library  
  
Currently, it is an incomplete process.  The steps below only take Altium library parameters and convert into a database that Kicad can read.  Symbols and footprint still need to be ported one-by-one manually.  There is a conversion tool for schematics and PCB but it requires a lot of manually modification afterwards.  And I am not aware of any conversion tools that can convert symbols (schlib) and footprints (pcblib).  If that's changed, please let me know.  

Prepare the schlib and pcblib if not already done  
1. Put the SchLibs and PcbLibs in an Integrated Library project.  
 &ensp;Libpath\mylib.LibPkg
2. Compile mylib.LibPkg.  An integrated library is created in a subfolder  
 &ensp;Libpath\Integrated Library\mylib.IntLib
3. Fix errors if necessary.  Compile again.
4. Suggestion: Remove special characters in the part name e.g. \/,;~

Create Access database
1. Create database library.  
	 &ensp; File -> New -> Library.  Save the new library to a new folder dB and new filename mylib:  
 &ensp; &ensp;Libpath\db\mylib.DbLib  
2. Tools-> Import from Integrated Library  
	 &ensp;Select new Access database  
	 &ensp;Set database location to a new file  
 &ensp; &ensp;Libpath\db\mylib.MDB  
	 &ensp;Select mylib.IntLib and convert  
	 &ensp;Save mylib.DbLib  

Convert Access database to Excel  
1. Open mylib.MDB in Access  
2. Export to Excel with formatting.  
 ![image](https://github.com/mryokai/Altium-IntLib-to-Kicad_dbl-conversion/assets/136013177/d7918847-3525-4fb5-b37b-a4015e1dc836)


Prepare the data in excel  
1. Open the excel file  
2. The column ordering is quite messy.  Move columns around to an order that make sense.  Note the first column “Part Number” will be the key in the database.  
3. Create a second column call “Symbols”.  
4. Create a third column call “Footprints”. Note that there should be a “Footprint” column which is not used at this point.  
5. Create a forth column call “No BOM”.  
6. Create a fifth column call “Schematics Only”.  
7. Identify where Component Kind and Component Type is  
8. In first cell of No BOM column, put formula:  
	 &ensp;=IF(J2="Standard (No BOM)",1,0)	where J2 is the Component Kind cell  
9. In first cell of Schematics Only column, put formula:  
   &ensp;=IF(J2="Mechanical",1,0) 		where J2 is the Component Kind cell  
10. Copy and paste to the rest of the 2 columns.  
11. Manual check for Graphical and Net Tie component kind.  
12. Fill Symbols and Footprints column if Kicad symbols or footprints are available  
13. Save the file and then save as CSV UTF-8 (comma delimited)  

Install SQLite3 driver and database editing software  
Read and follow this link to install the driver and database editing software SQLiteStudio:  
https://github.com/SumantKhalate/KiCad-libdb/  
![odbc1](https://github.com/mryokai/Altium-IntLib-to-Kicad_dbl-conversion/assets/136013177/5a1f5f79-bb6a-46e2-9d57-b294f58350fa)  


Create the SQLite3 database  
1. Open SQLiteStudio  
2. Create a new database. I called it Part.sqlite
3. Import the csv file.  There may be a limit of rows that can be imported.  Break up the csv file if necessary.  
	 &ensp;Tools -> Import  
4. Change Data type to TEXT on all field except No BOM, Schematics Only, and Pin Count  
5. Set Part Number as Primary Key  

Setup Kicad library configuration file  
1. Download Part.kicad_dbl.  
2. Modify the user parameters (every Intlib/Schlib will have different user parameter).  The other system parameters should be linked already.  

Create the data source to the SQLite3 database.  See https://github.com/SumantKhalate/KiCad-libdb/  
![odbc2](https://github.com/mryokai/Altium-IntLib-to-Kicad_dbl-conversion/assets/136013177/e404b29b-861a-4a28-a887-eb8b034172c7)  

Setup Kicad db path  
1. Open Kicad 7.0  
2. Preference -> Manage Symbol Libraries  
3. Add a library with a nickname and link it to the Part.kicad_dbl.  

Setup Kicad Symbols and Footprints  (this is the most tedious task)  
1. Open SQLiteStudio  
2. Fill the field "Symbols" and "Footprins" to Kicad symbols and footprints.  
3. Open or restart Kicad to reload the database.   
4. Verify the modification made in Step 2 by placing a new part in a schematics. e.g. pressing A  


Reference 1: https://docs.kicad.org/master/en/eeschema/eeschema_advanced.html  
Reference 2: https://github.com/SumantKhalate/KiCad-libdb/  


