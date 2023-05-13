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

## Server setup

Go to the PaperMCServerDocker folder and read the README.
