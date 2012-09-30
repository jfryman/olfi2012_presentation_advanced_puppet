!SLIDE
# Add a feature

## Feature 1: Change Configuration File

!SLIDE
Variables can be passed into templates via attributes specified in the
class definition, and become variables within that class scope. 

    class foo(
      $bar,
      $foobar
    ) {
      notify { "${bar}": }.
    }

!SLIDE [smaller]
Templates bound to a specific class will automatically have any class
defined parametrs.

    $string = template('/tmp/awesome.erb')

Templates can be used to set the value of content in a file resource:

    file { '/etc/motd':
      ensure  => present,
      content => template('/tmp/awesome.erb'),
    }

The variables `$string` will be available inside the _/tmp/awesome.erb_
file as the variable `string`

!SLIDE

    @@@ Ruby
    manifests/config.pp

    file { '/etc/ntp.conf':
      owner   => 'root',
      group   => 'root',
      mode    => '0644',
      content => template('ntp/ntp.conf')
    }

!SLIDE
   
    ntp.conf
 
    ...
      listen *
      server nist1-atl.ustiming.org
    ...
 
!SLIDE
#Return the value of a variable:

    @@@ Ruby
    <%= somevariable %>

+ Notice that variable names are no longer preceded by a $. 
+ Puppet variables and arrays are passed into templates as ruby variables and arrays.
+ Trailing hyphen prevents blank lines in ERB output

!SLIDE
# Syntax to Iterate over an Array:

    @@@ Ruby
    <% array.each do |value| -%>
      Array Value: <%= value %>
    <% end -%>

!SLIDE
# Templates and Conditionals

    @@@ Ruby
    <% if architecture != 'x86_64' %>
      <%= architecture %> is not 64-bit
    <% end -%>


!SLIDE

# Add as variables

    templates/ntp.conf.erb 

    listen <%= ntp_listen %> 
    <% ntp_servers.each do |ntp_server| %>
      server <%= ntp_server %>
    <% end %>

<!SLIDE small>

# Data Code Organization

+ Params  = Model
+ Manifests = Controller
+ Templates = View

<!SLIDE>
What is this params?

params.pp acts as a central store for data that is to be stored within a
module. Also can act as a focal point for generating default values with
the Ruby DSL.

<!SLIDE>
# Setting Default Values

Attributes declared in a class can have a default values applied to
them. 

Use a trick to inherit values from the `params.pp` class and use these
values to set defaults in the event the user does not supply them.

<!SLIDE smaller>
# Pass in variables to template
    @@@ Ruby
    class ntp (
      $servers = $ntp::params::default_servers
    ) inherits ntp::params {
        ...
        class { 'ntp::config': 
          servers    => $servers,
        ...
    }

    class ntp::config(
      $servers 
    ) {
      ...
    }

    class ntp::params {
      default_servers = ['pool.ntp.org']
    }
<!SLIDE small>
# Exercise: Modify Apache

+ Edit the Template for `/etc/httpd/conf/httpd.conf` and change the
values for `MaxKeepAliveRequests` and `KeepAliveTimeout` to be
variables.
+ Setup a params class for the apache module and set default values for 
the `MaxKeepAliveRequests` and `KeepAliveTimeout`
+ Setup default values in a `params.pp` class
+ Test the changes by passing in different parameters to the class
declaration.

Hint: Default value for `MaxKeepAliveRequests` is 100 and `KeepAliveTimeout` is 10

<!SLIDE>
Cool! 

This helps with singleton objects (like configuration files),
but what about repeatable chunks?

<!SLIDE>

Enter Defined Types

<!SLIDE>

A defined resource type is a custom type that behaves like a custom
resource type. Unlike classes, defined resource types can be used
multiple times. Also accepts metaparameters. 

<!SLIDE small>
# Example

    @@@ Ruby
    apache/manifests/vhost.pp

    define apache::vhost(
      $port, 
      $path = '/etc/httpd/conf.d'
    ) {
      file { "${path}/${name}.conf":
        ensure  => present,
        owner   => 'apache',
        group   => 'apache',
        mode    => '0644',
        content => template('apache/vhost.erb'),
        require => Class['apache::package'],
        notify  => Class['apache::service'],    
      }
    }

<!SLIDE small>
# Using a defined Resource Type

    @@@ Ruby
    apache::vhost { 'test.com':
      port => '8080',
    }

<!SLIDE>
# Exercise: Apache VHosts

+ Create a defined resource type `vhost` in your `apache` module.
+ Use this defined type to parametrize the creation of VHost directives
+ Include a parameter that allows the user to specify the port the vhost
runs on
+ Include a parameter that allows the user to specify the Virtual Host
name
+ Include a parameter that specifies the document root of the website

Hint: An example of a simple VHost can be found on the subsequent slide

<!SLIDE smaller>
    NameVirtualHost *:80
    <VirtualHost *:80>
      ServerName www.frymanet.com
      DocumentRoot /opt/websites/www.frymanet.com
      <Directory /opt/websites/www.frymanet.com>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride None
        Order allow,deny
        allow from all
      </Directory>
      ErrorLog /var/log/apache2/www.frymanet.com_error.log
      LogLevel warn
      CustomLog /var/log/apache2/www.frymanet.com_access.log combined
      ServerSignature Off
    </VirtualHost>
