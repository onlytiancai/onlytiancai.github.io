用nobody账户执行php脚本

```
# su -c "/usr/local/services/php-fpm-5.3.6/bin/php test.php" -s "/bin/bash" nobody

# cat test.php 
<?php
touch("temp");
?>
# ll temp 
-rw-r--r-- 1 nobody nobody 0 2016-03-10 21:58 temp
```
