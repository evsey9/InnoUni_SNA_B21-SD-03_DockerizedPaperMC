# InnoUni_SNA_B21-SD-03_DockerizedPaperMC
A dockerized Paper MC server with advanced logging capabilities.

## Logging setup

Add this to `/etc/rsyslog.conf` to enable listening port 514 UDP, and define JSON log template:

```sh
# provides UDP syslog reception, uncomment the two lines below
$ModLoad imudp
$UDPServerRun 514
$UDPServerAddress 172.17.0.1

$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
# add the line below which provides json output
$template $template jsonRfc5424Template,"{\"type\":\"syslog\",\"host\":\"%HOSTNAME%\",\"message\":\"<%PRI%>1 %TIMESTAMP:::date-rfc3339% %HOSTNAME% %APP-NAME% %PROCID% %MSGID% %STRUCTURED-DATA% %msg:::json%\"}\n"
```

Create `/etc/rsyslog.d/30-mcserver.conf` and add the following lines to write any messages from `mcserver` to their own file:

```sh
if $programname == 'mcserver' or $syslogtag == 'mcserver' then /var/log/mcserver/mcserver.log
& stop
```

Create log directory, make sure port 514 is enabled on the firewall and restart rsyslog service:

```sh
$ sudo mkdir -p /var/log/mcserver
$ sudo chown syslog:syslog /var/log/mcserver
$ sudo chmod 755 /var/log/mcserver
$ sudo ufw allow 514/udp
$ sudo service rsyslog restart
```

## Log rotation setup

Create `mcserver` in `/etc/logrotate.d` with the following contents for log rotation:

```sh
/var/log/mcserver/mcserver.log
{
	rotate 7
	daily
	missingok
	notifempty
	nocompress
	sharedscripts
}
```

Restart logrotate service:

```sh
$ sudo service logrotate restart
```

## Setting up execution of scripts upon log event detection

Create a directory to store the scripts that will be executed by rsyslog upon detecting certain patterns in log events (can be any folder):

```sh
$ mkdir /home/evseyantonovich/mcserver_rsyslog_scripts
```

Create three basic scripts in the `mcserver_rsyslog_scripts` folder that will be used to showcase the script execution by rsyslog, `script1.sh`, `script2.sh` and `script3.sh`:

`script1.sh`:
```sh
#!/bin/bash
echo "Pattern 1 detected!"
```

`script2.sh`:
```sh  
#!/bin/bash
echo "Pattern 2 detected!"
```

`script3.sh`:
```sh  
#!/bin/bash
echo "Pattern 3 detected!"
```

These scripts are very simple, and are there only to demonstrate the functionality of using rsyslog to execute external scripts upon passing a certain condition while receiving a logging event, simulating tripping some sort of security alarm, based on a pattern.

Give everyone read/execute permissions for the scripts (change folder and script names as applicable):

```sh
$ cd /home/evseyantonovich/mcserver_rsyslog_scripts
$ chmod 755 script1.sh
$ chmod 755 script2.sh
$ chmod 755 script3.sh
```

## Server setup

Go to the PaperMCServerDocker folder and read the README.
