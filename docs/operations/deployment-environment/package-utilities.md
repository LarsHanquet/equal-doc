## Package utilities

#### `init_package`

|**PATH**|`core\actions\init\package.php`|
| --------------- | ------------------------------------------------------------ |
|**URL**|`?do=init_package&package=core`|
|**CLI**|`$ ./equal.run --do=init_package --package=core`|
|**DESCRIPTION**|Initialize database for given package. If no package is given, initialize core package. Compile the apps (`apps folder`) of the package and copy them in the public folder.|

#### `init_seed`
| **PATH**        | `core\actions\init\seed.php`                                                       |
|-----------------|------------------------------------------------------------------------------------|
| **URL**         | `?do=init_seed&package=core`                                                       |
| **CLI**         | `$ ./equal.run --do=init_seed --package=core`                                      |
| **DESCRIPTION** | Seed objects for package using json configuration files in "{package}/init/seed/". |

#### `init_anonymize`
| **PATH**        | `core\actions\init\anonymize.php`                                                |
|-----------------|----------------------------------------------------------------------------------|
| **URL**         | `?do=init_anonymized&package=core`                                               |
| **CLI**         | `$ ./equal.run --do=init_anonymized --package=core`                              |
| **DESCRIPTION** | Anonymize objects using json configuration files in "{package}/init/anonymize/". |

#### `package-consistency`

|**PATH**|`core\actions\test\package-consistency.php`|
| --------------- | ------------------------------------------------------------ |
|**URL**|`?do=test_package-consistency&package=core`|
|**CLI**|`$ ./equal.run --do=test_package-consistency --package=core --level=warn`|
|**DESCRIPTION**|Performs consistency checks between DB and class as well as syntax validation for classes (PHP), views and translation files (JSON).|

> The level property has 3 options : 
>
> - **'error'** (ex: `missing property 'entity' in file:  "packages\/lodging\/views\/sale\booking\InvoiceLine.form.default.json"`);
> - **'warn'** (ex: `WARN  - I18 - Unknown field 'object_class' referenced in file "packages\/core\/i18n\/en\/alert\MessageModel.json")`;
> - **\*** (error & warn).

#### `test_package`

|**PATH**|`core\actions\test\package.php`|
| --------------- | ------------------------------------------------------------ |
|**URL**|`?do=test_package&package=core`|
|**CLI**|`$ ./equal.run --do=test_package --package=core`|
|**DESCRIPTION**|This controller checks the presence of test units for a given package and runs them, if any. (page :['Testing'](./testing.md)).|
