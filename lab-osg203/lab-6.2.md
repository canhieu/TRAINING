# Lab 6.2

### Step 1: Download the Files

```bash
wget www.nku.edu/~foxr/CIT371/employees.txt
wget www.nku.edu/~foxr/CIT371/students.txt
```

<figure><img src="../.gitbook/assets/image (602).png" alt=""><figcaption></figcaption></figure>

***

### Step 2: Computing Payroll Information

**File:** `script10`

```bash
#!/bin/bash
# Compute payroll from employees.txt

total_pay=0
count=0

while read lname hours wage
do
  if [ $hours -le 40 ]; then
    pay=$((hours * wage))
  else
    pay=$((40 * wage + 2 * (hours - 40) * wage))
  fi
  echo "$lname earns $pay"
  total_pay=$((total_pay + pay))
  count=$((count + 1))
done < employees.txt

if [ $count -gt 0 ]; then
  avg=$((total_pay / count))
  echo "Processed $count employees"
  echo "Average pay: $avg"
else
  echo "No data found"
fi
```

<figure><img src="../.gitbook/assets/image (604).png" alt=""><figcaption></figcaption></figure>



***

### Step 3: Finding Students by Major

**File:** `script11`

```bash
#!/bin/bash
# Find students by major

if [ $# -ne 1 ]; then
  echo "Usage: $0 <MAJOR>"
  exit 1
fi

major=$1
count=0

while read lname fname stu_major
do
  if [ "$stu_major" == "$major" ]; then
    echo "$lname, $fname - $stu_major"
    count=$((count + 1))
  fi
done < students.txt

echo "Found $count students in major $major"
```

<figure><img src="../.gitbook/assets/image (605).png" alt=""><figcaption></figcaption></figure>

***

### Step 4: Assigning Letter Grades

**File:** `script12`

```bash
#!/bin/bash
# Compute average score and assign letter grade

pass=0
fail=0

while read lname fname major t1 t2 t3 t4 t5
do
  total=$((t1 + t2 + t3 + t4 + t5 + t5))  # t5 counted twice
  avg=$((total / 6))

  if [ $avg -ge 90 ]; then
    grade="A"
  elif [ $avg -ge 80 ]; then
    grade="B"
  elif [ $avg -ge 70 ]; then
    grade="C"
  elif [ $avg -ge 60 ]; then
    grade="D"
  else
    grade="F"
  fi

  echo "$lname ($major): $grade"

  if [ "$grade" == "F" ]; then
    fail=$((fail + 1))
  else
    pass=$((pass + 1))
  fi
done < students.txt

echo "Passed: $pass"
echo "Failed: $fail"
```

<figure><img src="../.gitbook/assets/image (606).png" alt=""><figcaption></figcaption></figure>

***

### Step 5: Converting Permissions to Numeric Equivalents

**File:** `script13`

```bash
#!/bin/bash
# Convert permissions to numeric equivalent

for FILE in "$@"; do
  permissions=$(ls -l "$FILE" | awk '{print $1}')
  first=0
  second=0
  third=0

  # User
  [ "${permissions:1:1}" == "r" ] && first=$((first + 4))
  [ "${permissions:2:1}" == "w" ] && first=$((first + 2))
  [ "${permissions:3:1}" == "x" ] && first=$((first + 1))
  # Group
  [ "${permissions:4:1}" == "r" ] && second=$((second + 4))
  [ "${permissions:5:1}" == "w" ] && second=$((second + 2))
  [ "${permissions:6:1}" == "x" ] && second=$((second + 1))
  # Others
  [ "${permissions:7:1}" == "r" ] && third=$((third + 4))
  [ "${permissions:8:1}" == "w" ] && third=$((third + 2))
  [ "${permissions:9:1}" == "x" ] && third=$((third + 1))

  echo "$FILE: $first$second$third"
done
```

<figure><img src="../.gitbook/assets/image (607).png" alt=""><figcaption></figcaption></figure>

***

### Step 6: Determining Palindromes

**File:** `script14`

```bash
#!/bin/bash
# Determine if input is a palindrome

if [ $# -ne 1 ]; then
  echo "Usage: $0 <string>"
  exit 1
fi

word=$1
len=${#word}
is_palindrome=1

for ((i=0; i<len/2; i++)); do
  if [ "${word:i:1}" != "${word:len-i-1:1}" ]; then
    is_palindrome=0
    break
  fi
done

if [ $is_palindrome -eq 1 ]; then
  echo "$word is a palindrome"
else
  echo "$word is not a palindrome"
fi
```

<figure><img src="../.gitbook/assets/image (608).png" alt=""><figcaption></figcaption></figure>

***

### Step 7: Creating a Palindrome Function

**File:** `script15`

```bash
#!/bin/bash
# Palindrome function and array test

is_palindrome() {
  local word=$1
  local len=${#word}
  for ((i=0; i<len/2; i++)); do
    if [ "${word:i:1}" != "${word:len-i-1:1}" ]; then
      echo "$word is not a palindrome"
      return
    fi
  done
  echo "$word is a palindrome"
}

# Define array
words=("level" "hello" "madam" "bash" "civic" "world")

for w in "${words[@]}"; do
  is_palindrome "$w"
done
```

<figure><img src="../.gitbook/assets/image (609).png" alt=""><figcaption></figcaption></figure>
