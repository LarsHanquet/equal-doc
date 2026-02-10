# Validation & Searching

## Validation

### Definition

The validate function of the **ObjectManager provider** checks if the values given by the User are matching the object fields requirements.

This function is used at different times :

- with `CREATE` & `UPDATE` Methods
- with Controller Calls

!!! note "Calling the scripts"
    eQual handles both Models and Controllers as entities. As such, their fields (repectively provided by `getColumns()` and `params`) are validated by following a same logic.

### Type and Usage

The validation of a given value is made by comparing it to the expected format for the field the value relates to.
Comparisons are made based on constraints. Constraints are either explicit ([see getConstraints()](#getconstraints)) or implicit, by using a specific Type or Usage in the field definition.  

* Constraints apply based on a Usage.
* There is always a Usage assigned to a field.
* If it is not in the definition, it is created by default based on the Type.
* Therefore, Usage is prioritized for field validation (the Type and the Usage must be compatible).

### Validate function 

#### Required property

The following section will look into the function's parts.

The first property checked by the function is the **required** property.

```php
<?php
foreach($schema as $field => $def) {
    // required fields must be provided and cannot be left/set to null
    if( isset($def['required']) && $def['required'] && (!isset($values[$field]) 
    || is_null($values[$field])) ) {
        $error_code = EQ_ERROR_INVALID_PARAM;
        $res[$field]['missing_mandatory'] = 'Missing mandatory value.'; 
        trigger_error("EQ_DEBUG_ORM::mandatory field {$field} is missing for 
        instance of {$class}", EQ_REPORT_WARNING);
    }
}
```

The function checks if the field is required (can't be *null*) and if it is, it must have a value, otherwise, an error is sent to the User.



#### Type & Usage properties

##### `type`

The function checks if the `type` property matches the value, this property gives information about the way the field is stored in the DB.

##### `usage`

The `usage` property is complementary to the `type` property and allows to refine the way the field should be handled by the ORM and the DBBMS, and how it should be rendered in the UI. 

**Example** :

```php
<?php
'params' 		=>	[
        'code' => [
            'description'   => 'Code for athenticating user.',
            'type'          => 'string',
            'usage'         => 'email',
            'required'      => true
        ]
    ],
```

In the above example, the `type` is a string, and the `usage` tells us that the string is expected to be an email.

The validate function will then call the **`getConstraintFromUsage()`** method from **`DataValidator.class.php `**:

```php
<?php
case 'email':
        return [
            'kind'  => 'function',
            'rule'  => function($a, $o) {return (bool) (preg_match('/^([_a-z0-9-]+)
            (\.[_a-z0-9+-]+)*@([a-z0-9-]+)(\.[a-z0-9-]+)*(\.[a-z]{2,13})$/', $a));}
        ];
```

A rule is then used to valide the usage property value.

Here, it checks if the field is a real email through regex and returns true or false. If it returns false, it will send an error to the User. 



#### getConstraints() 

Returns a map of constraint items associating fields with validation functions.

**Example** of validation function : 

```php
<?php
case 'string':
        $constraints[$field]['size_overflow'] = [
            'message'     => 'String length must be maximum 255 chars.',
            'function'    => function($val, $obj) {return (strlen($val) <= 255);}
        ];
        break;
```



#### getUnique()

Provides the list of unique rules (array of combinations of fields).

If the rules aren't followed, an error is sent.

```php
<?php
 trigger_error("EQ_DEBUG_ORM::field {$field} violates unique constraint with 
 object {$oid}", EQ_REPORT_WARNING);
```

## Searching

Searching is handled by the ORM, which offers a `search()` method, and can be performed either through a Collection or using the ObjectManager service. 

The `search` method returns a list of objects identifiers of the targeted class.

* that list of identifiers can then be used for reading values of the filtered objects;
* additional parameters (order, sort, start, limit) allow to refine the search behavior.

In order to search amongst existing objects, the `search` method  uses a  `domain` argument describing the search criteria.

<!---TODO update link-->
!!! note "About domains"
    To understand or learn more about domains, please refer to the  [`domain`](../architecture-concepts/domains.md)  section.

<img src="../../../_assets/img/searching.drawio.png">

### ORM search

The ObjectManager service offers a dedicated method for searching amongst Objects. 

#### `signature`

```php
<?php
/*
 * @param   string     $class       Class of the objects to search for.
 * @param   array      $domain      Domain (disjunction of conjunctions) defining the criteria the objects have to match.
 * @param   array      $sort        Associative array mapping fields and orders on which result have to be sorted.
 * @param   integer    $start       The offset at which to start the segment of the list of matching objects.
 * @param   string     $limit       The maximum number of results/identifiers to return.
 *
 * @return  integer|array   Returns an array of matching objects ids, or a negative integer in case of error.
 */
public function search($class, $domain=NULL, $sort=['id' => 'asc'], $start='0', $limit='0', $lang=DEFAULT_LANG)
```



#### `usage`

Example:

```php
<?php
$res = $orm->search('core\User', ['login', '=', $login]);
```



### Collection search

In controllers, searching can be invoked either by calling the ObjectManager service or through a collection. 

#### `signature`

```php
<?php
public function search(array $domain=[], array $params=[], $lang=DEFAULT_LANG)
```



#### `usage`
Example:

```php
<?php
use core\User;

$collection = User::search([
        ['login', 'like', '%john%'],
        ['validated', '=', 'true']				
    ]);
```


