## **Config Location:**  
> :~$ ls /etc/apache2/
apache2.conf  conf-available  conf-enabled  envvars  magic  mods-available  mods-enabled  ports.conf  sites-available  sites-enabled  

**Use above config to change port to 8085.** 

**Current url for apache2:**  http://localhost:8085/  
  
  
## **Get status of apache2 process running:**  
**Main PID: 8420 (apache2): shows pid for apache process**  
> :~$ sudo netstat -tulpn | grep 8085
tcp6       0      0 :::8085                 :::*                    LISTEN      8420/apache2  

> :~$ sudo systemctl status apache2
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2024-02-16 21:40:18 IST; 45s ago
       Docs: https://httpd.apache.org/docs/2.4/
    Process: 8416 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
   Main PID: 8420 (apache2)
      Tasks: 55 (limit: 11905)
     Memory: 5.1M
        CPU: 57ms
     CGroup: /system.slice/apache2.service
             ├─8420 /usr/sbin/apache2 -k start
             ├─8421 /usr/sbin/apache2 -k start
             └─8422 /usr/sbin/apache2 -k start
> Feb 16 21:40:18 abhishek-docker systemd[1]: Starting The Apache HTTP Server...
> Feb 16 21:40:18 abhishek-docker apachectl[8419]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' dire>
> Feb 16 21:40:18 abhishek-docker systemd[1]: Started The Apache HTTP Server.  


**Check whether 8085 is used by apache process as provided in config:**
> abhishek@abhishek-docker:~$ sudo netstat -tulpn | grep 8085
tcp6       0      0 :::8085                 :::*                    LISTEN      8420/apache2  

## How to Resolve ServerName Directory Issue Below :
> Feb 16 21:48:49 abhishek-docker apachectl[8757]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' dire>  

**Added below entry in apache2.conf as need to provide domain name:**  
> ServerName localhost

## Apache2 Log Locations:
> abhishek@abhishek-docker:~$ ls /var/log/apache2/
access.log  error.log  other_vhosts_access.log

## Backing Up Access Log by Timestamp using 'tar' Command:
> sudo tar -czvf "access_$(date +'%Y%m%d%H%M%S').tar.gz" access.log

## Clearing Access Log: 
Check file size of access file:  
> du -h access.log  

**Method 1: use truncate command:**
> sudo truncate -s 0 access.log  

**Method 2: using touch command:**
> sudo touch access.log

## Enabling Processing Time in Access File:
**Make below changes in apache2.conf:**  
> LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-agent}i\" %D" combined
> CustomLog ${APACHE_LOG_DIR}/access.log combined  

**Comment out other combined settings.**

## Using Apache Benchmark (ab):
> ab -n 1000 -c 10 -r -s 60 -q -k http://localhost:8085/  

**Explanation of the options used:**
- -n 1000: Perform a total of 1000 requests.
- -c 10: Use 10 concurrent connections.
- -r: Don't exit on socket receive errors.
- -s 60: Timeout in seconds for each connection.
- -q: Do not display progress status; only print the final results.
- -k: Enable the HTTP KeepAlive feature.

## Apache2 mods: 
**list all apache mod installed:**
> sudo apache2ctl -M  

directory to check config of installed mods:
> /etc/apache2/mods-enabled  
---