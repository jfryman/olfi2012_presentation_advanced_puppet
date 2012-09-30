!SLIDE
# Modularize it
![Lego Bricks](../images/legobricks.jpeg)

!SLIDE
# Why Modularize?
+ Allow multiple people to work in multiple parts of the module at the
same time
+ Abstract parts of the module and remove dependencies between
individual resources
  - One part of the module doesn't need to know about the other.

<!SLIDE>
# Module Autoloading

[Let's refresh on how Puppet automatically loads files]
<!SLIDE smaller>
# Autoloading Convention Rules:
+ Classes and definitions in `init.pp` are always imported as long as they are prefixed with the modulename.
+ Classes and definitions not in `init.pp` named `modulename::foo` can
be placed in a file `name.pp`
+ Classes and definitions not in `init.pp` named `modulename::foo::bar` can be
placed in `foo/bar.pp`
+ Additional namespaced classes and definitions repeat the directory and name pattern.

!SLIDE
# Rules for breaking up code

- one manifest, one classification of task.
- move File resource _source_ attributes into resource _content_ objects 
  templates for future modification. (where possible)
- create resource relationships in init file for class (think `void
main()`)


!SLIDE
    @@@ Ruby
    manifests/init.pp

    class ntp {
      class { 'ntp::package': }
      class { 'ntp::config':
        require => Class['ntp::package'],
        notify  => Class['ntp::service'],
      }
      class { 'ntp::service': } 
    }

<!SLIDE smaller>
     @@@ Ruby
      manifests/package.pp

      class ntp::package {
        package { 'ntp':
          ensure => present,
        }
      }

      manifests/config.pp

      file { '/etc/ntp.conf':
        owner   => 'root',
        group   => 'root',
        mode    => '0644',
        content => template('ntp/ntp.conf')
      }

      manifests/service.pp

      service { 'ntpd':
        ensure    => running,
        enable    => true,
      }

<!SLIDE>
Isn't this overkill?!

![Overkill](../images/overkill.jpeg)

<!SLIDE>
Only if you're working alone, and even then you don't want to maintain
giant 300 line manifests

<!SLIDE>
### yes, they grow that big

<!SLIDE>

Wire it up with vagrant (and vagrant-hitch)

    manifests/site.pp
    
    node default {
      class { 'ntp': }
    }

Note: The node name would be associated with the appropriate node in
site.pp

<!SLIDE>

If you are not using Vagrant, create the following file in your `tests`
directory with the content listed below:

    cat > tests/init.pp <EOF
    class { 'ntp': }
    EOF

and then execute 

`puppet apply (moduledir)/ntp/tests/init.pp --debug
--verbose`

<!SLIDE smaller>
# Exercise: Modularize your existing Apache Module

+ Refactor `init.pp` to include the package/file/service pattern
+ Move your code blocks for each section into a new file appropriately
named
  - e.g.: package into package.pp, config into config.pp, service into
service.pp
+ Convert your configuration file to use a template
+ Ensure Vagrant has been configured properly to instantiate the Apache
Module
+ Test your changes with Vagrant to see what changes

<!SLIDE>
Have you committed your code to Git recently?

![Git Logo](../images/git_logo.png)
