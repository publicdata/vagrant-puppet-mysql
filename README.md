# Vagrant Puppet MySQL
Puppet manifests to install and configure MySQL on our default Ubuntu Precise Vagrant development server.

## Usage
### Install MySQL
To install the MySQL client and server add one of the following to your manifest:

- `class { 'mysql': }`
- `include 'mysql'`

This will also set the `root` password (see below for further details), remove any anonymous user accounts, and configure MySQL to use the `UTC` timezone.

#### Specify the `root` password
By default, the `root` password will be set to 'root'. You can specify an alternative password using the `root_password` parameter:

~~~~~ruby
# Install MySQL, and set the root password
class { 'mysql':
  root_password => 'secure_password',
}
~~~~~

### Create a database
To create a new database, pass the database name to the `mysql::db::create` helper:

~~~~~ruby
# Creates the 'devdb' database
mysql::db::create { 'devdb': }
~~~~~

#### Create multiple databases
To create multiple database using a single statement, pass an array to the `mysql::db::create` helper:

~~~~~ruby
# Creates three databases
mysql::db::create { ['devdb', 'stagingdb', 'productiondb']: }
~~~~~

### Create a user
To create a new user, pass the username, password, and host to the `mysql::user::create` helper:

~~~~~ruby
# Creates the user jimbob@localhost, with the password 'banjo'
mysql::user::create { 'jimbob@localhost':
  user     => 'jimbob',
  host     => 'localhost',
  password => 'banjo',
}

# 'user' parameter inferred from title
mysql::user::create { 'jimbob':
  host     => 'localhost',
  password => 'banjo',
}
~~~~~

### Drop a user
To drop a user, pass the username and host to the `mysql::user::drop` helper:

~~~~~ruby
# Drops the user marylou@192.168.0.2
mysql::user::drop {
  user => 'marylou',
  host => '192.168.0.2',
}

# 'user' parameter inferred from title
mysql::user::drop { 'marylou':
  host => '192.168.0.2',
}
~~~~~

### Grant privileges to a user
To grant database privileges to a user, pass the username, host, and database to the `mysql::user::grant` helper.

~~~~~ruby
# Grants ALL privileges on the 'deliverance' database to jimbob@localhost
mysql::user::grant {
  user     => 'jimbob',
  host     => 'localhost',
  database => 'deliverance',
}

# 'user' parameter inferred from title
mysql::user::grant { 'jimbob':
  host     => 'localhost',
  database => 'deliverance',
}
~~~~~

For greater control, you may also pass the table and privileges you wish to set.

~~~~~ruby
# Grants ALL privileges on the 'deliverance.squeal' table to
# jimbob@localhost
mysql::user::grant { 'jimbob':
  host     => 'localhost',
  database => 'deliverance',
  table    => 'squeal',
}

# Grants CREATE privileges on the 'deliverance' database to
# jimbob@localhost
mysql::user::grant { 'jimbob':
  host       => 'localhost',
  database   => 'deliverance',
  privileges => 'CREATE',
}
~~~~~