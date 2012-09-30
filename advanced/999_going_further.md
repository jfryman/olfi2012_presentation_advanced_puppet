<!SLIDE smaller>

# Things to add to your explorations going forward.

+ Error checking
  - Puppet Labs `stdlib` has several functions that check for validity
of values within the catalog. Never trust user data, even if they're
admins! 
+ Testing
  - Things like `rspec-puppet` allow for refactoring to be done with
more confidence as it will test that things are happening as they are
supposed to.
+ Intra-node communication
  - Virtual Resources
  - [puppet-message](https://github.com/fup/puppet-message)
