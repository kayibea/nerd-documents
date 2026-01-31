# AWK Cheatsheet

---

## Overview
`awk` is a **pattern scanning and processing language** for text and data files. It reads input line by line, splits it into fields, and allows actions based on patterns.

Basic syntax:
```bash
awk 'pattern { action }' file
```
- `pattern` optional: what lines to process
- `action` optional: what to do with matching lines
- `$0` → entire line
- `$1, $2, ...` → field 1, field 2, etc.

Default field separator is **whitespace**.

---

## Basic Usage

### Print Entire File
```bash
awk '{ print $0 }' file.txt
```

### Print Specific Field
```bash
awk '{ print $1, $3 }' file.txt
```

### Print Lines Matching a Pattern
```bash
awk '/error/ { print $0 }' file.txt
```

### Condition on Field
```bash
awk '$3 > 100 { print $1, $3 }' file.txt
```

### Using Field Separator
```bash
awk -F, '{ print $1, $2 }' file.csv
```

---

## Built-in Variables
- `NR` → current record (line) number
- `NF` → number of fields in current record
- `FS` → input field separator
- `OFS` → output field separator
- `RS` → input record separator (default \n)
- `ORS` → output record separator
- `FILENAME` → current filename being processed
- `FNR` → record number in current file

Example:
```bash
awk -F: '{ print NR, $1, $3 }' /etc/passwd
```

---

## String Operations

### Concatenation
```bash
awk '{ print $1 $2 }' file.txt
```

### Length of a Field
```bash
awk '{ print length($1) }' file.txt
```

### Substring
```bash
awk '{ print substr($1, 2, 4) }' file.txt
```

### Match / Replace
```bash
awk '{ gsub(/foo/, "bar", $1); print $1 }' file.txt
```
- `gsub` → global substitution
- `sub` → replace first occurrence

---

## Arithmetic
```bash
awk '{ sum += $3 } END { print sum }' file.txt
```
- `sum` accumulates values
- `END` block runs after all lines are processed

---

## Control Structures

### If / Else
```bash
awk '{ if ($3 > 50) print $1; else print $2 }' file.txt
```

### For Loop
```bash
awk '{ for(i=1; i<=NF; i++) print $i }' file.txt
```

### While Loop
```bash
awk '{ i=1; while(i<=NF) { print $i; i++ } }' file.txt
```

### Arrays
```bash
awk '{ count[$1]++ } END { for(word in count) print word, count[word] }' file.txt
```

### Functions
```bash
awk 'function square(x) { return x*x } { print square($2) }' file.txt
```

---

## Multi-file / FNR / NR
```bash
awk '{ print FNR, NR, FILENAME, $0 }' file1 file2
```
- `FNR` resets per file
- `NR` continues across all files

---

## BEGIN / END Blocks
```bash
awk 'BEGIN { print "Start" } { print $1 } END { print "Done" }' file.txt
```
- `BEGIN` runs before any input
- `END` runs after all input

---

## Examples

### Sum Column 3
```bash
awk '{ sum += $3 } END { print "Total:", sum }' file.txt
```

### Print Users with UID > 1000
```bash
awk -F: '$3 > 1000 { print $1, $3 }' /etc/passwd
```

### Print Unique Values
```bash
awk '!seen[$1]++ { print $1 }' file.txt
```

### Convert CSV to TSV
```bash
awk -F, 'BEGIN { OFS="\t" } { $1=$1; print }' file.csv
```

### Filter and Transform
```bash
awk '$2 ~ /error/ { gsub(/foo/, "bar"); print $1, $2 }' file.txt
```

### Complex Example — Mini Script in AWK
```bash
awk '
BEGIN {
    print "Processing file..."
    OFS = ","
}

# Count total sales per user
{
    sales[$1] += $3
    transactions[$1]++
}

END {
    printf "%s,%s,%s\n", "User", "Transactions", "Total Sales"
    for (user in sales) {
        printf "%s,%d,%.2f\n", user, transactions[user], sales[user]
    }
}' sales_data.csv
```
- Reads `sales_data.csv` (format: `User,Item,Amount`)
- Aggregates total sales and number of transactions per user
- Prints CSV header and output
- Shows how AWK can behave like a **small custom script language**

---

## Tips
- `awk` is Turing complete: you can do almost any text processing
- Use `-F` for CSV or custom separators
- Always quote scripts to prevent shell expansion
- Combine with `sort`, `uniq`, `grep` for pipelines

---

## Docs
```bash
man awk
info awk
```

