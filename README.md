# Altium-IntLib-to-Kicad_dbl-conversion
How to convert Altium integrated library (or atomic schlib/pcblib) into Kicad database library

Reference 1: https://docs.kicad.org/master/en/eeschema/eeschema_advanced.html
Reference 2: https://github.com/SumantKhalate/KiCad-libdb/

Prepare the schlib and pcblib if not already done
1. Put the SchLibs and PcbLibs in an Integrated Library project.  
Libpath\mylib.LibPkg
2. Compile mylib.LibPkg.  An integrated library is created in a subfolder 
Libpath\Integrated Library\mylib.IntLib
3. Fix errors if necessary.  Compile again.
4. Suggestion: Remove special characters in the part name e.g. \/,;~

Create Access database
1. Create database library.
	File -> New -> Library.  Save the new library to a new folder dB and new filename mylib:
Libpath\db\mylib.DbLib
2. Tools-> Import from Integrated Library
	Select new Access database
	Set database location to a new file 
Libpath\db\mylib.MDB
	Select mylib.IntLib and convert
	Save mylib.DbLib

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
	=IF(J2="Standard (No BOM)",1,0)	where J2 is the Component Kind cell
9. In first cell of Schematics Only column, put formula:
  =IF(J2="Mechanical",1,0) 		where J2 is the Component Kind cell
10. Copy and paste to the rest of the 2 columns.
11. Manual check for Graphical and Net Tie component kind.
12. Fill Symbols and Footprints column if Kicad symbols or footprints are available
13. Save the file and then save as CSV UTF-8 (comma delimited)

Install SQLite3 drive and database editing software
Read and follow this link to install the driver and database editing software:
https://github.com/SumantKhalate/KiCad-libdb/

Create the SQLite3 database
1. Open SQLiteStudio
2. Create a new database.
3. Import the csv file.  There may be a limit of rows that can be imported.  Break up the csv file if necessary.
	Tools -> Import
4. Change Data type to TEXT on all field except No BOM, Schematics Only, and Pin Count
5. Set Part Number as Primary Key

Create the data source to the SQLite3 database
 ![image](https://github.com/mryokai/Altium-IntLib-to-Kicad_dbl-conversion/assets/136013177/619e6e1d-5bb8-4559-8c4d-fa884dcff2c3)




