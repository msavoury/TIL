# TIL
Collection of Useful Things Learned

- Install ```rlwrap``` to get history for readlines that don't support it (mit-scheme)

Python Simple Server:
```
python -m SimpleHTTPServer
```

Ruby Simple Server:
```
ruby -run -e httpd . -p 9090
```
## MySQL
- use the ```mysqlimport``` utility to import data from flat files
### MySQL dump specified tables
mysqldump -u... -p... mydb t1 t2 t3 > mydb_tables.sql

##Command line shortcuts
- Ctrl P : Previous entry in history
- Ctrl N : Next entry in history
- Ctrl B : Move back one char
- Ctrl F : Move forward one char
- Alt  F : Move forward one word
- Alt  B : Move backward one word
- Ctrl A : Move cursor to beginning of line
- Ctrl E : Move cursor to end of line
- Ctrl K : Delete from cursor to end of line
- Ctrl U : Delete from cursor to beginning of line
- Ctrl W : Delete word
- Ctrl [ : Escape
- Ctrl T : Transpose chars
- Ctrl C : Cancel line




