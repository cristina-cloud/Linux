# How to change baseurl to download packages in CentOS #

##### * This document will explain how to solve repository issues of downloading packages in CentOS 6.8 ##### 

#### 1. Trying to install a package i need ####
```
[centos@ip-10]$ sudo yum install perl-DateTime perl-CPAN perl-Net-SSLeay perl-IO-Socket-SSL perl-Digest-SHA gcc -y
Loaded plugins: fastestmirror, presto
Setting up Install Process
Determining fastest mirrors
YumRepo Error: All mirror URLs are not using ftp, http[s] or file.
 Eg. Invalid release/repo/arch combination/
removing mirrorlist with no valid mirrors: /var/cache/yum/x86_64/6/extras/mirrorlist.txt
Error: Cannot find a valid baseurl for repo: extras
```
-> Unable to download a package cause of invalid baseurl for repo

#### 2. Checking repolist ####
```
[centos@ip-10]$ sudo yum repolist
Loaded plugins: fastestmirror, presto
Determining fastest mirrors
YumRepo Error: All mirror URLs are not using ftp, http[s] or file.
 Eg. Invalid release/repo/arch combination/
removing mirrorlist with no valid mirrors: /var/cache/yum/x86_64/6/extras/mirrorlist.txt
base                                                                        | 3.7 kB     00:00
base/primary_db                                                             | 4.7 MB     00:03
YumRepo Error: All mirror URLs are not using ftp, http[s] or file.
 Eg. Invalid release/repo/arch combination/
removing mirrorlist with no valid mirrors: /var/cache/yum/x86_64/6/updates/mirrorlist.txt
repo id                                  repo name                                           status
base                                     CentOS-6 - Base                                     6,696
extras                                   CentOS-6 - Extras                                       0
updates                                  CentOS-6 - Updates                                      0
repolist: 6,696
```
-> There are no available pacakages in Extras and Updates repositories

#### 3. Checking repository lists ####
```
[centos@ip-10 ~]$ cd /etc/yum.repos.d

[centos@ip-10 yum.repos.d]$ ll
total 28
-rw-r--r--. 1 root root 2050 Jan 14 16:07 CentOS-Base.repo
-rw-r--r--. 1 root root  647 May 18  2016 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  289 May 18  2016 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 May 18  2016 CentOS-Media.repo
-rw-r--r--. 1 root root 6259 May 18  2016 CentOS-Vault.repo 
```

#### 4. Check ####
```
4. [centos@ip-10 yum.repos.d]$ vi CentOS-Base.repo
[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
enabled=1

#released updates
[updates]
name=CentOS-$releasever - Updates
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```
-> When trying to enter the mirrorlist, it shows a message "Invalid release/repo/arch combination"

#### 5. Changing a valid baseurl ####
```
[centos@ip-10 yum.repos.d]$ vi CentOS-Base.repo
[base]
name=CentOS-$releasever - Base
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
baseurl=https://vault.centos.org/6.8/os/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
enabled=1

#released updates
[updates]
name=CentOS-$releasever - Updates
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
baseurl=https://vault.centos.org/6.8/updates/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/extras/$basearch/
baseurl=https://vault.centos.org/6.8/extras/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```
-> Changed a valid baseurl to download a package 

#### 6. ####
```
yum clean all
```

#### 7. Check repolist after changing the invalid baseurl ####
```
[centos@ip-10 yum.repos.d]$ sudo yum repolist
Loaded plugins: fastestmirror, presto
Loading mirror speeds from cached hostfile
^C


 Current download cancelled, interrupt (ctrl-c) again within two seconds
to exit.



extras                                                                      | 3.4 kB     00:00
extras/primary_db                                                           |  37 kB     00:00
^C

updates                                                                     | 3.4 kB     00:00
updates/primary_db                                                          | 5.4 MB     00:03
repo id                                  repo name                                           status
base                                     CentOS-6 - Base                                     6,696
extras                                   CentOS-6 - Extras                                      64
updates                                  CentOS-6 - Updates                                    974
repolist: 7,734
```
-> Now Extras and Updates repositories have availble packages to download

#### 8. Download the package you wanted ####
```
[centos@ip-10 yum.repos.d]$ yum install perl-DateTime perl-CPAN perl-Net-SSLeay perl-IO-Socket-SSL perl-Digest-SHA gcc -y

Installed:
  perl-CPAN.x86_64 0:1.9402-141.el6_7.1           perl-DateTime.x86_64 1:0.5300-2.el6
  perl-Digest-SHA.x86_64 1:5.47-141.el6_7.1       perl-IO-Socket-SSL.noarch 0:1.31-3.el6_8.2
  perl-Net-SSLeay.x86_64 0:1.35-10.el6_8.1

Complete!
```
-> It is available to download package 



https://vault.centos.org
