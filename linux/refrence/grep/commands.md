search for string in a 
```console
grep **PATTERN**  [FILENAME]
```
case insensitive
```console
grep -i **PATTERN** [FILENAME]
```

search recursivly(files and dir)
```console
grep [-r|-R] **PATTERN** [DIRECTORY] 
```

search recursivly, with diff end-of-line char
```console
grep -d **DELIMITER** **PATTERN** [FILENAME]
```
note: use `--exclude-dirs=**PATTERN**`

search file for fullword only
```console
grep -w **PATTERN** [FILENAME]
```

search for two different words
```console
egrep -w "PATTERN1|PATTERN2" [FILENAME] 
```

show line numbers
```console
grep -n **PATTERN** [FILENAME]
```
note: `-b` will give POSITION`

invert match
```console
grep -v **PATTERN** [FILENAME]
```

show file names of files that DO NOT MATCH
```console
grep -L **PATTERN** [FILENAME]
```


list only file names in results
```console
grep -l **PATTERN** [FILENAME]
```

multiple search patterns
```console
grep -e **PATTERN1** -e **PATTERN2** [FILENAME]
```

limit results
```console
grep **PATTERN** -m# [FILENAME]
```

exact line match only
```console
grep -x **PATTERN** [FILENAME]
```

quiet
```console
grep -q **PATTERN** [FILENAME]
```

supress error messages
```console
grep -s **PATTERN** [FILENAME]
```

include # of lines [A]bove [B]below and [C]enter
```console
grep -[A|B|C] # [PATTERN] [FILENAME]
```

only print matched string
```console
grep -o [PATTERN] [FILENAME]
```

#grep #terminal #linux #file #reference #commands 