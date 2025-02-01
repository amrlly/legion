# Nvidia drivers: 530.41.03 

There is no way to change it programatically.

But, if you change laptop performance mode with `fn+q` and then restart the `nvidia-powerd` systemd service it will change
the power scaling in this order:

```
quite - max 80w
balanced - max 100w
performance - max 130w
```

Basically it works like in windows, but with a small hassle of restarting systemd service every time you change performance mode.

# Currently: January 2024

Everything works as expected
