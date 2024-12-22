
# Awk snippets 

Cool snippets i've found online. Most of which i've gathered from ChatGPT 


* Format drive info 

```bash
lsblk |awk 'NR==1{print $0" DEVICE-ID(S)"}NR>1{dev=$1;printf $0" ";system("find /dev/disk/by-id -lname \"*"dev"\" -printf \" %p\"");print "";}'|grep -v -E 'part|lvm'
```

* Find pattern in file 

```bash
awk '/pattern/ { count++ } END { print count }' file.txt
```

* Sum all values of a column 

```bash
awk '{ sum += $2 } END { print sum }' file.txt
``` 

* Print a specific column 

```bash
awk '{ print $3 }' file.txt
```

* Filter and print specific fields 

```bash
awk '$1 ~ /ERROR/ { print $1, $2, $5 }' log.txt
```

* Field Delimiters for CSV 

```bash
awk -F, '{ print $1, $3 }' data.csv
```

* Print Non-Matching lines 

```bash
awk '!/pattern/' file.txt 
```

* Remove duplicate lines 

```bash
awk '!seen[$0]++' file.txt 
```

* Print lines with Line number and Field Count 

```bash
awk '{ print NR ": (" NF " fields) -> " $0}' file.txt 
```

* Convert Lines into a Single Comma-Separated Line 

```bash
awk '{ 
  if (NR == 1) {
    # For the first line, just print
    printf "%s", $0
  } else {
    # For subsequent lines, prepend a comma
    printf ",%s", $0
  }
} END { printf "\n" }' file.txt

```

> Note : You'd be better off using a short sed snippet like this instead 
> `paste -sd ',' file.txt`


* Print a range of lines between two patterns 

```bash
awk '/BEGIN_PATTERN/,/END_PATTERN/' file.txt 
```

```bash
```
