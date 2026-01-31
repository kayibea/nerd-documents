# Bash/Linux Text Manipulation Cheat Sheet

## 1. Filtering / Searching

| Command | Description | Common Tricks |
|---------|------------|---------------|
| `grep` | Search for lines matching a pattern | `grep 'error' file` (filter lines), `grep -v 'skip'` (exclude lines), `grep -i 'pattern'` (ignore case), `grep -E 'regex|regex2'` (extended regex) |
| `egrep` | `grep -E`, extended regex | Same as above but shorthand |
| `fgrep` | Fixed string search | Useful for literal strings with special characters |
| `awk` | Powerful field-based filtering | `awk '$3 > 1000' file` (filter rows by column 3), `awk '/pattern/ {print $1}'` |
| `sed` | Stream editor, can also filter | `sed -n '/pattern/p' file` (print only matching lines) |
| `cut` | Extract columns or fields | `cut -d':' -f1 /etc/passwd` (first field) |
| `head` / `tail` | Take first/last N lines | `tail -f logfile` (follow), `head -n 10` |
| `sort` | Sort lines | `sort file`, `sort -u` (unique), `sort -nr` (numeric descending) |
| `uniq` | Collapse duplicate lines | `uniq -c` (count duplicates), often after `sort` |

## 2. Replacing / Substituting

| Command | Description | Tricks |
|---------|------------|--------|
| `sed` | Find & replace in text | `sed 's/old/new/' file` (first match per line), `sed 's/old/new/g'` (all matches), `sed -i 's/old/new/g' file` (edit in place) |
| `awk` | Field-based replacement | `awk '{gsub("old","new"); print}' file` |
| `tr` | Translate or delete characters | `tr 'a-z' 'A-Z'` (uppercase), `tr -d '\n'` (delete newlines) |

## 3. Extracting / Splitting Text

| Command | Description | Tricks |
|---------|------------|--------|
| `cut` | Extract fields or columns | `cut -c1-5` (chars 1-5), `cut -d',' -f2-4` (columns 2-4) |
| `awk` | Flexible column extraction | `awk '{print $1,$3}' file` |
| `sed` | Regex extraction | `sed -n 's/.*user: \([a-z]*\).*/\1/p'` (capture group) |
| `grep -o` | Only print matching portion | `grep -o '[0-9]\+' file` (numbers only) |

## 4. Transforming / Cleaning

| Command | Description | Tricks |
|---------|------------|--------|
| `tr` | Translate/delete characters | `tr -dc 'A-Za-z0-9'` (remove everything except alphanumerics) |
| `awk` | Modify fields or content | `awk '{$1=toupper($1); print}'` |
| `sed` | Delete lines, replace text | `sed '/^#/d' file` (remove comments), `sed '/^$/d'` (remove empty lines) |
| `rev` | Reverse text | `echo hello | rev` → `olleh` |
| `fold` | Wrap long lines | `fold -w 80 file` |

## 5. Combining / Counting / Statistics

| Command | Description | Tricks |
|---------|------------|--------|
| `wc` | Word/line/byte count | `wc -l file` (lines), `wc -c` (bytes), `wc -w` (words) |
| `sort` | Sort lines for counting | `sort file | uniq -c` (count duplicates) |
| `awk` | Summarize data | `awk '{sum+=$2} END {print sum}' file` |
| `cut` | Column prep for stats | `cut -d',' -f2 file | sort | uniq -c` |
| `paste` | Combine columns | `paste file1 file2` |

## 6. Regular Expressions / Patterns

| Concept | Usage | Notes |
|---------|------|------|
| `.` | Any single character | `a.b` → matches `acb` |
| `*` | 0 or more of previous | `a*b` → `b`, `ab`, `aaab` |
| `+` | 1 or more (grep -E/egrep) | `a+b` → `ab`, `aaab` |
| `[]` | Character class | `[0-9]` matches a digit |
| `^` | Start of line | `^Error` matches lines starting with Error |
| `$` | End of line | `done$` |
| `\|` | OR (grep -E) | `cat|dog` |
| `\(...\)` | Group & capture (sed/awk) | `sed 's/\(foo\)/\1bar/'` |

## 7. Tips & Tricks / Common Patterns

- Remove blank lines: `sed '/^$/d' file`
- Remove duplicates: `sort file | uniq`
- Count occurrences: `sort file | uniq -c | sort -nr`
- Mask sensitive info: `sed -E 's/[0-9]{4}/****/g' file`
- Extract IPs: `grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' file`
- Column sum: `awk '{sum+=$3} END{print sum}' file`
- Replace only on certain lines: `sed '/pattern/s/old/new/' file`
- Chain commands efficiently:
```bash
grep 'ERROR' logfile | awk '{print $1,$5}' | sort | uniq -c | sort -nr
```