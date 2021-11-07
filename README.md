# Linux Task

## script.sh

```
#!/bin/bash

echo $(top -b -n 1 -d1 | grep "Cpu(s)"| awk '{print $2}' && echo '   | ' && date "+%Y-%m-%d %H:%M:%S")  >> /home/ralboom/Desktop/cpuData

echo $(free | grep Mem | awk '{print $3," | ", $4}' && echo '  |  ' && date "+%Y-%m-%d %H:%M:%S")  >> /home/ralboom/Desktop/memoryData

echo $((df | awk '{print $3}'| tail -n 1 ) && (df | awk '{print "  |  ",$4}' | tail -n 1 && echo '  |  ' && date "+%Y-%m-%d %H:%M:%S")) >> /home/ralboom/Desktop/diskData

sh /home/ralboom/Desktop/cpu.sh > /var/www/html/cpu.html
sh /home/ralboom/Desktop/memory.sh > /var/www/html/memory.html
sh /home/ralboom/Desktop/disk.sh > /var/www/html/disk.html
```

-   The script above will collect disk usage and free disk, memory used and free memory, and cpu utilization and store them in files.

-   after collecting data, it will run multiple scripts for updating the content of required html pages.

## cpu.sh

```
#!/bin/bash
echo "<html>"
echo "<body>"
echo "<h1>CPU Usage Information</h1>"

awk -F "|" 'BEGIN {print "<table><tr><th>CPU Usage</th><th>Timestamp</th></tr>"}
{ print "<tr><td>" $1 "</td><td>" $2 "</td></tr>" }
END { print "</table>" }' /home/ralboom/Desktop/cpuData

echo "</body>"
echo "</html>"
```

-   The script above will loop over the collected cpu data and create the cpu usage html page.

## memory.sh

```
#!/bin/bash
echo "<html>"
echo "<body>"
echo "<h1>Memory  Usage Information</h1>"

awk -F "|" 'BEGIN {print "<table><tr><th>Memory Used</th><th>Memory Free</th><th>Timestamp</th></tr>"}
{ print "<tr><td>" $1 "</td><td>" $2 "</td><td>" $3 "</td></tr>" }
END { print "</table>" }' /home/ralboom/Desktop/memoryData

echo "</body>"
echo "</html>"
```

-   The script above will loop over the collected memory data and create the memory usage html page.

## disk.sh

```
#!/bin/bash
echo "<html>"
echo "<body>"
echo "<h1>Disk  Usage Information</h1>"

awk -F "|" 'BEGIN {print "<table><tr><th>Disk Used</th><th>Disk Free</th><th>Timestamp</th></tr>"}
{ print "<tr><td>" $1 "</td><td>" $2 "</td><td>" $3 "</td></tr>" }
END { print "</table>" }' /home/ralboom/Desktop/diskData

echo "</body>"
echo "</html>"
```

-   The script above will loop over the collected disk data and create the disk usage html page.

## First Cronjob

```
crontab -e
0 */1 * * *  /home/ralboom/Desktop/script.sh
```

-   the above cronjob it will execute the above script.sh every hour.

## Result of first cronjob

![CPU Usage](https://videos-storing.s3.ap-south-1.amazonaws.com/linux/cpu.PNG)

![Memory Usage](https://videos-storing.s3.ap-south-1.amazonaws.com/linux/mem.PNG)

![Disk Usage](https://videos-storing.s3.ap-south-1.amazonaws.com/linux/disk.PNG)

## avg.sh

```
#!/bin/bash

echo $(awk -F "|" '{ sum += $1 } END { if (NR > 0) print sum / NR }' /home/ralboom/Desktop/cpuData && echo ' | ' && date "+%Y-%m-%d %H:%M:%S") >> /home/ralboom/Desktop/cpuAvg

echo $(awk -F "|" '{ sum += $1 } END { if (NR > 0) print sum / NR }' /home/ralboom/Desktop/memoryData && echo ' | ' && date "+%Y-%m-%d %H:%M:%S") >> /home/ralboom/Desktop/memoryAvg

echo $(awk -F "|" '{ sum += $1 } END { if (NR > 0) print sum / NR }' /home/ralboom/Desktop/diskData && echo ' | ' && date "+%Y-%m-%d %H:%M:%S") >> /home/ralboom/Desktop/diskAvg


sh /home/ralboom/Desktop/cpuAvg.sh > /var/www/html/cpuAvg.html
sh /home/ralboom/Desktop/memoryAvg.sh > /var/www/html/memoryAvg.html
sh /home/ralboom/Desktop/diskAvg.sh > /var/www/html/diskAvg.html
```

-   The script above it will calculate avgs of all the data collected by the first cronjob and store them in files

-   after calculating avgs, it will run multiple scripts for updating the content of required html pages.

## cpuAvg.sh

```
#!/bin/bash
echo "<html>"
echo "<body>"
echo "<h1>CPU Average Usage Information</h1>"

awk -F "|" 'BEGIN {print "<table><tr><th>CPU Avg Usage</th><th>Timestamp</th></tr>"}
{ print "<tr><td>" $1 "</td><td>" $2 "</td></tr>" }
END { print "</table>" }' /home/ralboom/Desktop/cpuAvg

echo "</body>"
echo "</html>"
```

-   The script above will loop over the collected cpu avg data and create the cpu average usage html page.

## memoryAvg.sh

```
#!/bin/bash
echo "<html>"
echo "<body>"
echo "<h1>Memory Average Usage Information</h1>"

awk -F "|" 'BEGIN {print "<table><tr><th>Memory Avg Usage</th><th>Timestamp</th></tr>"}
{ print "<tr><td>" $1 "</td><td>" $2 "</td></tr>" }
END { print "</table>" }' /home/ralboom/Desktop/memoryAvg

echo "</body>"
echo "</html>"
```

-   The script above will loop over the collected memory avg data and create the memory average usage html page.

## diskAvg.sh

```
#!/bin/bash
echo "<html>"
echo "<body>"
echo "<h1>Disk Average Usage Information</h1>"

awk -F "|" 'BEGIN {print "<table><tr><th>Disk Avg Usage</th><th>Timestamp</th></tr>"}
{ print "<tr><td>" $1 "</td><td>" $2 "</td></tr>" }
END { print "</table>" }' /home/ralboom/Desktop/diskAvg

echo "</body>"
echo "</html>"
```

-   The script above will loop over the collected disk avg data and create the disk average usage html page.

## Second cronjob
```
crontab -e
0 */1 * * *  /home/ralboom/Desktop/script.sh
```
* the above cronjob it will execute the above avg.sh every hour.

## Result of second cronjob

![CPU Avg Usage](https://videos-storing.s3.ap-south-1.amazonaws.com/linux/cpuAvg.PNG)

![Memory Avg Usage](https://videos-storing.s3.ap-south-1.amazonaws.com/linux/memAvg.PNG)

![Disk Avg Usage](https://videos-storing.s3.ap-south-1.amazonaws.com/linux/diskAvg.PNG)
