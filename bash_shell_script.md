
### Get current script absolute path

``` abs=`cd $(dirname $0); pwd -P` ```

### Check file is exists

```
if [ -f "$abs/filename" ]; then
  echo "File exists"
else # or [ ! -f "$abs/filename" ]
  echo "File not found"
fi
```

### Ping website

```
# Get status code
# -S show error when -s(silent) mode
status_code=$(curl -sfI website.com | head -n 1 | awk '{print $2}') 

```