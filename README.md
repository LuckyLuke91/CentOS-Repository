# Local CentOS Repository


## First Steps

````````````````````
sudo yum groupinstall "Einfacher Webserver"
sudo yum install yum-utils createrepo
sudo firewall-cmd --add-service http
sudo mkdir /var/www/html/repomirror
sudo reposync --gpgcheck -l --repoid=base --download_path=/var/www/html/repomirror --downloadcomps --download-metadata -n
sudo reposync --gpgcheck -l --repoid=centosplus --download_path=/var/www/html/repomirror --downloadcomps --download-metadata -n
sudo reposync --gpgcheck -l --repoid=updates --download_path=/var/www/html/repomirror --downloadcomps --download-metadata -n
sudo reposync --gpgcheck -l --repoid=extras --download_path=/var/www/html/repomirror --downloadcomps --download-metadata -n
sudo reposync --gpgcheck -l --repoid=epel --download_path=/var/www/html/repomirror --downloadcomps --download-metadata -n
sudo createrepo -v /var/www/html/repomirror/base/ -g comps.xml
sudo createrepo -v /var/www/html/repomirror/centosplus/
sudo createrepo -v /var/www/html/repomirror/updates/
sudo createrepo -v /var/www/html/repomirror/extras/
sudo createrepo -v /var/www/html/repomirror/epel/ -g comps.xml
sudo restorecon -R -v /var/www/html/*
sudo firewall-cmd --permanent --add-port=443/tcp
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
````````````````````


## Create Repo-File

sudo vi /var/www/html/Update.repo
```````````````````````````````````
[base]
name= CentOS-$releasever - Base (mxx)
baseurl=http://FQDN/repomirror/base/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[centosplus]
name= CentOS-$releasever - Plus (mxx)
baseurl=http://FQDN/repomirror/centosplus/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[updates]
name= CentOS-$releasever - Updates (mxx)
baseurl=http://FQDN/repomirror/updates/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[extras]
name= CentOS-$releasever - Extras (mxx)
baseurl=http://FQDN/repomirror/extras/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[epel]
name= Epel (mxx)
baseurl=http://FQDN/repomirror/epel/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
```````````````````````````````````


## Loading Repo-File from Client
```
sudo yum install yum-utils
sudo yum-config-manager --add-repo http://FQDN/repomirror/Update.repo
sudo rm /etc/yum.repos.d/CentOS-*
```


## Script for base Cron:

sudo vi /root/base-rep-sync.sh
````````````````````````
> #!/bin/bash
> #
> # Beschreibung: Skript zur Synchronisierung des RHEL-Repositories
> # auf dem Spiegelserver
> # Autor: Joerg Kastning <joerg(Punkt)kastning(aet)uni-bielefeld.de>

LOG="/var/log/base_reposync.log"
REPOID="base"
DOWNLOADPATH="repomirror"

echo \# `date +%Y-%m-%d` - START REPOSYNC \# > $LOG

reposync --gpgcheck -l --repoid=$REPOID --download_path=/var/www/html/$DOWNLOADPATH --downloadcomps --download-metadata -n >> $LOG

echo \# `date +%Y-%m-%d` - END REPOSYNC \# >> $LOG
echo \# `date +%Y-%m-%d` - START CREATEREPO \# >> $LOG

cd /var/www/html/$DOWNLOADPATH/$REPOID
createrepo -v /var/www/html/$DOWNLOADPATH/$REPOID -g comps.xml >> $LOG

echo \# `date +%Y-%m-%d` - END CREATEREPO \# >> $LOG

exit 0
````````````````````````


## Script for centosplus Cron:

sudo vi /root/centosplus-rep-sync.sh
````````````````````````
#!/bin/bash
#
# Beschreibung: Skript zur Synchronisierung des RHEL-Repositories
# auf dem Spiegelserver
# Autor: Joerg Kastning <joerg(Punkt)kastning(aet)uni-bielefeld.de>

LOG="/var/log/centosplus_reposync.log"
REPOID="centosplus"
DOWNLOADPATH="repomirror"

echo \# `date +%Y-%m-%d` - START REPOSYNC \# > $LOG

reposync --gpgcheck -l --repoid=$REPOID --download_path=/var/www/html/$DOWNLOADPATH --downloadcomps --download-metadata -n >> $LOG

echo \# `date +%Y-%m-%d` - END REPOSYNC \# >> $LOG
echo \# `date +%Y-%m-%d` - START CREATEREPO \# >> $LOG

cd /var/www/html/$DOWNLOADPATH/$REPOID
createrepo -v /var/www/html/$DOWNLOADPATH/$REPOID >> $LOG

echo \# `date +%Y-%m-%d` - END CREATEREPO \# >> $LOG

exit 0
````````````````````````


## Script for updates Cron:

sudo vi /root/updates-rep-sync.sh
````````````````````````
#!/bin/bash
#
# Beschreibung: Skript zur Synchronisierung des RHEL-Repositories
# auf dem Spiegelserver
# Autor: Joerg Kastning <joerg(Punkt)kastning(aet)uni-bielefeld.de>

LOG="/var/log/updates_reposync.log"
REPOID="updates"
DOWNLOADPATH="repomirror"

echo \# `date +%Y-%m-%d` - START REPOSYNC \# > $LOG

reposync --gpgcheck -l --repoid=$REPOID --download_path=/var/www/html/$DOWNLOADPATH --downloadcomps --download-metadata -n >> $LOG

echo \# `date +%Y-%m-%d` - END REPOSYNC \# >> $LOG
echo \# `date +%Y-%m-%d` - START CREATEREPO \# >> $LOG

cd /var/www/html/$DOWNLOADPATH/$REPOID
createrepo -v /var/www/html/$DOWNLOADPATH/$REPOID >> $LOG

echo \# `date +%Y-%m-%d` - END CREATEREPO \# >> $LOG

exit 0
````````````````````````


## Script for extras Cron:

sudo vi /root/extras-rep-sync.sh
````````````````````````
#!/bin/bash
#
# Beschreibung: Skript zur Synchronisierung des RHEL-Repositories
# auf dem Spiegelserver
# Autor: Joerg Kastning <joerg(Punkt)kastning(aet)uni-bielefeld.de>

LOG="/var/log/extras_reposync.log"
REPOID="extras"
DOWNLOADPATH="repomirror"

echo \# `date +%Y-%m-%d` - START REPOSYNC \# > $LOG

reposync --gpgcheck -l --repoid=$REPOID --download_path=/var/www/html/$DOWNLOADPATH --downloadcomps --download-metadata -n >> $LOG

echo \# `date +%Y-%m-%d` - END REPOSYNC \# >> $LOG
echo \# `date +%Y-%m-%d` - START CREATEREPO \# >> $LOG

cd /var/www/html/$DOWNLOADPATH/$REPOID
createrepo -v /var/www/html/$DOWNLOADPATH/$REPOID >> $LOG

echo \# `date +%Y-%m-%d` - END CREATEREPO \# >> $LOG

exit 0
````````````````````````


## Script for epel Cron:

sudo vi /root/epel-rep-sync.sh
````````````````````````
#!/bin/bash
#
# Beschreibung: Skript zur Synchronisierung des RHEL-Repositories
# auf dem Spiegelserver
# Autor: Joerg Kastning <joerg(Punkt)kastning(aet)uni-bielefeld.de>

LOG="/var/log/epel_reposync.log"
REPOID="epel"
DOWNLOADPATH="repomirror/"

echo \# `date +%Y-%m-%d` - START REPOSYNC \# > $LOG

reposync --gpgcheck -l --repoid=$REPOID --download_path=/var/www/html/$DOWNLOADPATH --downloadcomps --download-metadata -n >> $LOG

echo \# `date +%Y-%m-%d` - END REPOSYNC \# >> $LOG
echo \# `date +%Y-%m-%d` - START CREATEREPO \# >> $LOG

cd /var/www/html/$DOWNLOADPATH/$REPOID
createrepo -v /var/www/html/$DOWNLOADPATH/$REPOID -g comps.xml >> $LOG

echo \# `date +%Y-%m-%d` - END CREATEREPO \# >> $LOG

exit 0
````````````````````````


## CronJob for Repo-Update (Every hour a Repo Update for a Repo-ID):
``````
sudo crontab -e

0 * * * * /root/base-rep-sync.sh
10 * * * * /root/centosplus-rep-sync.sh
20 * * * * /root/updates-rep-sync.sh
30 * * * * /root/extras-rep-sync.sh
40 * * * * /root/epel-rep-sync.sh
``````
