## Installation & Config utilities

#### `db-connectivity`

| **PATH**        | `core\actions\test\db-connectivity.php`                      |
| --------------- | ------------------------------------------------------------ |
| **URL**         | `?do=test_db-connectivity`                                   |
| **CLI**         | `$ ./equal.run --do=test_db-connectivity`                    |
| **DESCRIPTION** | Tests connectivity to the DBMS server (check if we're able to establish a TCP/IP connection). |

#### `db-access`

| **PATH**        | `core\actions\test\db-access.php`                            |
| --------------- | ------------------------------------------------------------ |
| **URL**         | `?do=test_db-access`                                         |
| **CLI**         | `$ ./equal.run --do=test_db-access`                          |
| **DESCRIPTION** | Tests access to the database specified in the config file. This controller uses db-connectivity before trying to access the database. |

#### `fs-consistency`

| **PATH**        | `core\actions\test\fs-consistency.php`                       |
| --------------- | ------------------------------------------------------------ |
| **URL**         | `?do=test_fs-consistency`                                    |
| **CLI**         | `$ ./equal.run --do=test_fs-consistency`                     |
| **DESCRIPTION** | Checks current installation directories integrity. The controller checks if all mandatory directories are present, and if their permissions allow the apache process to read/write as required.|

#### `init_db`

|**PATH**|`core\actions\init\db.php`|
|-|-|
|**URL**|`?do=init_db`|
|**CLI**|`$ ./equal.run --do=init_db`|
|**DESCRIPTION**|Creates a database using the details provided in config file. This controllers calls db-connectivity and if connection can be established with the host, it requests the creation of the database, if it does not exist yet.|


