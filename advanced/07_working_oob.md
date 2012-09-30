!SLIDE
# Get it working out of the box

!SLIDE

[Don't worry about getting everything working, just get the defaults
installing.]

![Under Construction](../images/Under_construction.jpeg)

<!SLIDE smaller>
+ Create a base module directory with appropriate directories
  - _modulename_/{templates,files,manifests,lib,tests}
+ Identify Packages that need to be installed
+ Make note of installation files that are deployed by package manager
  - Get familiar with your package manager.
  - Copy these files to temporary directory.
+ Make note of all services that are started (if any)

<!SLIDE>
    yum -y install ntp
    rpm -ql ntp
    ps ax | grep ntp

<!SLIDE>
    rpm -ql ntp

    ...
    /etc/ntp.conf
    /etc/ntp/crypto
    /etc/ntp/crypto/pw
    /etc/rc.d/init.d/ntpd
    ...

<!SLIDE smaller>
    cp /etc/ntp.conf _moduledir_/templates/ntp.conf.erb

<!SLIDE small>
# Exercise: Analyze Apache on Linux.

+ Create the basic module directory for Apache
+ Run _yum install_ to install apache
+ Analyze the RPM package to determine what files in /etc are created

## Hint: `httpd` is the package name for Apache in RPM distros
