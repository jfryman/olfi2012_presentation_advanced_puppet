!SLIDE
# Websites you'll use a lot!
### Keep these open while developing

- [Puppet Language
Guide](http://docs.puppetlabs.com/guides/language_guide.html)
- [Puppet Type
Reference](http://docs.puppetlabs.com/references/latest/type.html)
- [Defined Types](http://docs.puppetlabs.com/learning/definedtypes.html)

!SLIDE

# Revisiting Package/File/Service

!SLIDE
A typical system administration task usually consists of three steps to
setup a new application.

!SLIDE
+ [install packages]
+ [configure the application]
+ [start services]

<!SLIDE small>

    @@@ Ruby
    class ntp {
      package { 'ntp':
        ensure => present,
      }
      file { '/etc/ntp.conf':
        owner   => 'root',
        group   => 'root',
        mode    => '0644',
        source  => 'puppet:///modules/ntp/ntp.conf',
        require => Package['ntp'],
      }
      service { 'ntpd':
        ensure    => running,
        enable    => true,
        subscribe => File['/etc/ntp.conf'],
      }
    }

!SLIDE
# Steps to writing Scalable Puppet Modules

0. Use Git
1. Get it working out of the box
2. Modularize
3. Add a feature
4. Test with Vagrant
5. Repeat #3/#4
6. Clean up code and publish
