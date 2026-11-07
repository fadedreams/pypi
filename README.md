# PyPI Mirror Setup

If `pip install` hangs or times out (for example, `pypi.org` is slow or blocked on your network), use a nearby PyPI mirror.

## 1. Test which mirror works fastest

Run this in your terminal (bash or zsh). It downloads a real package from each mirror and prints the time taken.

```bash
for url in \
  https://mirrors.aliyun.com/pypi/simple/ \
  https://pypi.mirrors.ustc.edu.cn/simple/ \
  https://mirrors.cloud.tencent.com/pypi/simple \
  https://repo.huaweicloud.com/repository/pypi/simple/ \
  https://pypi.doubanio.com/simple/
do
  s=$(date +%s.%N)
  if pip download cryptography -d /tmp/piptest --no-deps -i "$url" --default-timeout=10 --retries 1 -q >/dev/null 2>&1; then
    e=$(date +%s.%N)
    printf "OK   %.2fs  %s\n" "$(echo "$e - $s" | bc)" "$url"
  else
    echo "FAIL        $url"
  fi
  rm -rf /tmp/piptest
done
```

Reading the output:

- `OK 1.23s` means the mirror works and that is how long the download took.
- `FAIL` means it timed out or errored, so skip that mirror.

Notes:

- Requires `bc` for the timing math.
- Make sure proxy variables are unset (`env | grep -i proxy`), or the test measures your proxy instead of your direct connection.
- Run it a couple of times, since speeds vary.

## 2. Set the fastest mirror as default

Example using Aliyun:

```bash
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
```

## 3. Verify

```bash
pip config list
```

Expected output:

```
global.index-url='https://mirrors.aliyun.com/pypi/simple/'
```

## Extras

Use a different mirror for a single install without changing config:

```bash
pip install -i https://repo.huaweicloud.com/repository/pypi/simple/ <package>
```

Remove the setting and go back to the official index:

```bash
pip config unset global.index-url
```

See which config files pip is reading:

```bash
pip config debug
```
