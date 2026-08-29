# Lab 6.1

### Step 1: Creating Your First Shell Script

**File:** `script1`

```bash
#!/bin/bash
echo "You are $USER"
echo "Your home directory is $HOME"
echo "Your home directory consists of $(du -sH ~)"
```



<figure><img src="../.gitbook/assets/image (589).png" alt=""><figcaption></figcaption></figure>

**Yêu cầu mở rộng – ghi ra file `info.txt`:**

```bash
#!/bin/bash
{
  echo "You are $USER"
  echo "Your home directory is $HOME"
  echo "Your home directory consists of $(du -sH ~)"
} > info.txt
```

<figure><img src="../.gitbook/assets/image (590).png" alt=""><figcaption></figcaption></figure>

***

### Step 2: Getting User Input

**File:** `script2`

```bash
#!/bin/bash
echo "What is your name?"
read NAME
echo "What is your username?"
read USERNAME

echo "Hello $NAME, your home directory contents and size:"
ls /home/$USERNAME
du -sH /home/$USERNAME
```

<figure><img src="../.gitbook/assets/image (591).png" alt=""><figcaption></figcaption></figure>



***

### Step 3: Using Parameters

**File:** `script3`

```bash
#!/bin/bash
NAME=$1
USERNAME=$2

echo "Hello $NAME, your home directory contents and size:"
ls /home/$USERNAME
du -sH /home/$USERNAME
```

<figure><img src="../.gitbook/assets/image (592).png" alt=""><figcaption></figcaption></figure>

***

### Step 4: Using Conditional Statements

**File:** `script4`

```bash
#!/bin/bash
if [ $# -ne 2 ]; then
  echo "Illegal input"
elif [ $1 -gt $2 ]; then
  echo "$1 is greater"
elif [ $1 -lt $2 ]; then
  echo "$2 is greater"
else
  echo "Both numbers are equal"
fi
```

**Giải thích:**\
`[ $# -ne 2 ]` nghĩa là “số lượng tham số khác 2”. Dùng để kiểm tra người dùng có nhập đúng hai tham số không.

<figure><img src="../.gitbook/assets/image (593).png" alt=""><figcaption></figcaption></figure>

***

### Step 5: Using Loops

**File:** `script5`

```bash
#!/bin/bash
read -p "Enter the number you seek: " NUM
COUNT=0
for VALUE in "$@"; do
  if [ "$VALUE" -eq "$NUM" ]; then
    COUNT=$((COUNT+1))
  fi
done
echo "$NUM appeared $COUNT times"
```

<figure><img src="../.gitbook/assets/image (594).png" alt=""><figcaption></figcaption></figure>

**Yêu cầu mở rộng – đếm số phần tử nằm giữa hai giá trị nhập vào:**

**File:** `script5b`

```bash
#!/bin/bash
read -p "Enter lower bound: " LOW
read -p "Enter upper bound: " HIGH
COUNT=0
for VALUE in "$@"; do
  if [ "$VALUE" -ge "$LOW" ] && [ "$VALUE" -le "$HIGH" ]; then
    COUNT=$((COUNT+1))
  fi
done
echo "There are $COUNT numbers between $LOW and $HIGH"
```

<figure><img src="../.gitbook/assets/image (595).png" alt=""><figcaption></figcaption></figure>

***

### Step 6: Finding the Largest and Smallest

**File:** `script6`

```bash
#!/bin/bash
if [ $# -eq 0 ]; then
  echo "Error: No parameters supplied."
  exit 1
fi

LARGEST=$1
SMALLEST=$1

for VALUE in "$@"; do
  if [ "$VALUE" -gt "$LARGEST" ]; then
    LARGEST=$VALUE
  fi
  if [ "$VALUE" -lt "$SMALLEST" ]; then
    SMALLEST=$VALUE
  fi
done

echo "Largest: $LARGEST"
echo "Smallest: $SMALLEST"
```

<figure><img src="../.gitbook/assets/image (597).png" alt=""><figcaption></figcaption></figure>

***

### Step 7: Checking File Permissions

**File:** `script7`

```bash
#!/bin/bash
if [ $# -eq 0 ]; then
  echo "Error: No files supplied."
  exit 1
fi

count=0
sum=0

for file in "$@"; do

  if [[ -r $file && -w $file && -x $file ]]; then
    size=$(stat -c%s "$file")
    sum=$((sum+size))
    count=$((count+1))
  fi
done

if [ $count -eq 0 ]; then
  echo "No files have r/w/x permissions."
else
  avg=$((sum/count))
  echo "Of the $# files entered, $count were r/w/x"
  echo "Total size: $sum bytes"
  echo "Average size: $avg bytes"
fi
```

<figure><img src="../.gitbook/assets/image (600).png" alt=""><figcaption></figcaption></figure>

***

### Step 8: Counting File Types

**File:** `script8`

```bash
#!/bin/bash
if [ $# -ne 1 ]; then
  echo "Usage: $0 <directory>"
  exit 1
fi

DIR=$1
if [ ! -d "$DIR" ]; then
  echo "Error: $DIR is not a directory."
  exit 1
fi

count_files=0
count_dirs=0
count_links=0

for item in "$DIR"/*; do
  if [ -f "$item" ]; then
    count_files=$((count_files+1))
  elif [ -d "$item" ]; then
    count_dirs=$((count_dirs+1))
  elif [ -L "$item" ]; then
    count_links=$((count_links+1))
  fi
done

total=$((count_files+count_dirs+count_links))
echo "In $DIR:"
echo "Regular files: $count_files"
echo "Directories: $count_dirs"
echo "Links: $count_links"
echo "Total items: $total"
```

<figure><img src="../.gitbook/assets/image (601).png" alt=""><figcaption></figcaption></figure>

