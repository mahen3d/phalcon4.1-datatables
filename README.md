
![Build Status](https://img.shields.io/badge/branch-master-blue.svg) [![Build Status](https://travis-ci.org/m1ome/phalcon-datatables.svg?branch=master)](https://travis-ci.org/m1ome/phalcon-datatables) [![Coverage Status](https://coveralls.io/repos/m1ome/phalcon-datatables/badge.svg)](https://coveralls.io/r/m1ome/phalcon-datatables)

[![Total Downloads](https://poser.pugx.org/m1ome/phalcon-datatables/downloads.svg)](https://packagist.org/packages/m1ome/phalcon-datatables)  [![License](https://poser.pugx.org/m1ome/phalcon-datatables/license.svg)](https://packagist.org/packages/m1ome/phalcon-datatables)
[![Dependency Status](https://www.versioneye.com/user/projects/54de663d271c93aa12000002/badge.svg?style=flat)](https://www.versioneye.com/user/projects/54de663d271c93aa12000002)


# About
This is a [Phalcon Framework](http://phalconphp.com/) adapter for [DataTables](http://www.datatables.net/).
This version contains community fixes and multi model search support
# Support
### Currently supported
* QueryBuilder interface
* ResultSet interface
* Pagination
* Global search (by value)
* Ordering
* Multiple column ordering
* Column-based search
* Multi model search
* Export to Excel and PDF

# Installation
### Installation via Composer
* Install a composer
* Create `composer.json` file inside your project directory
* Paste into it
```json
{
    "require": {
        "sp4ike/phalcon4.1-datatables": "dev-master",
        "phpoffice/phpspreadsheet": "1.11.0",
        "mpdf/mpdf": "8.0.5"
    }
}
```
* Run `composer update`

# Example usage
It uses Phalcon [QueryBuilder](http://docs.phalconphp.com/en/latest/api/Phalcon_Mvc_Model_Query_Builder.html) for pagination in DataTables.

In example we have a stantart MVC application, with database enabled. Don't need to provide a normal bootstrap PHP file, for Phalcon documentation, visit official site.

### Controller (using QueryBuilder):
```php
<?php
use \DataTables\DataTable;

class TestController extends \Phalcon\Mvc\Controller {
    public function indexAction() {
        if ($this->request->isAjax()) {
          $builder = $this->modelsManager->createBuilder()
                          ->columns('id, name, email, balance')
                          ->from('Example\Models\User');

          $dataTables = new DataTable();
          $dataTables->fromBuilder($builder)->sendResponse();
        }
    }
}
```

### Controller (using ResultSet):
```php
<?php
use \DataTables\DataTable;

class TestController extends \Phalcon\Mvc\Controller {
    public function indexAction() {
        if ($this->request->isAjax()) {
          $resultset  = $this->modelsManager->createQuery("SELECT * FROM \Example\Models\User")
                             ->execute();

          $dataTables = new DataTable();
          $dataTables->fromResultSet($resultset)->sendResponse();
        }
    }
}
```

### Controller (using Array):
```php
<?php
use \DataTables\DataTable;

class TestController extends \Phalcon\Mvc\Controller {
    public function indexAction() {
        if ($this->request->isAjax()) {
          $array  = $this->modelsManager->createQuery("SELECT * FROM \Example\Models\User")
                             ->execute()->toArray();

          $dataTables = new DataTable();
          $dataTables->fromArray($array)->sendResponse();
        }
    }
}
```

### Controller (using multi models):
```php
<?php
use \DataTables\DataTable;

class TestController extends \Phalcon\Mvc\Controller {
    public function indexAction() {
        if ($this->request->isAjax()) {
          $builder = $this->modelsManager->createBuilder()
                          ->columns('u.id, u.name, u.email, u.name as role_name')
                          ->addFrom('Example\Models\User', 'u')
                          ->addFrom('Example\Models\Role', 'r')
                          ->where('u.role_id = r.id')
           
          $dataTables = new DataTable();
          $dataTables->fromBuilder($builder)->sendResponse();
          
           // or pass an array of columns to the builder
           $columns = ['u.id', 'u.name', 'u.email', ['u.name', 'alias' => 'role_name']];
           $dataTables = new DataTable();
           $dataTables->fromBuilder($builder, $columns)->sendResponse();
        }
    }
}
```

### Export Feature (using QueryBuilder):
```jquery
$(".exportBtn").on("click", function(e){
    e.preventDefault();
    // Get ajax params
    let params = $.param($('#tableID').DataTable().ajax.params());
    let _href = $(this).attr('href');
    // append params to url + export type (Excel/PDF)
    $(this).attr('href', _href + params + '/' + $(this).text());
    window.location.href = $(this).attr('href');
});
```

```php
<?php
use \DataTables\DataTable;

class TestController extends \Phalcon\Mvc\Controller {
    public function exportAction($filters, $type) {
          $builder = $this->modelsManager->createBuilder()
                          ->columns('id, name, email, balance')
                          ->from('Example\Models\User');

          $dataTables = new DataTable();
          $dataTables->fromBuilder($builder)->exportResponse($type);
    }
}
```

### Model:
```php
<?php
/**
* @property integer id
* @property string name
* @property string email
* @property float balance
* @property integer role_id
*/
class User extends \Phalcon\Mvc\Model {
}

/**
* @property integer id
* @property string name
*/
class Role extends \Phalcon\Mvc\Model {
}
```

### View:
```html
<html>
    <head>
        <title>Simple DataTables Application</title>
        <script type="text/javascript" language="javascript" src="//code.jquery.com/jquery-1.11.1.min.js"></script>
        <script type="text/javascript" language="javascript" src="//cdn.datatables.net/1.10.4/js/jquery.dataTables.min.js"></script>
        <script type="text/javascript">
            $(document).ready(function() {
                $('#example').DataTable({
                    serverSide: true,
                    ajax: {
                        url: '/test/index',
                        method: 'POST'
                    },
                    columns: [
                        {data: "id", searchable: false},
                        {data: "name"},
                        {data: "email"},
                        {data: "balance", searchable: false}
                    ]
                });
            });
        </script>
    </head>
    <body>
        <table id="example">
            <thead>
                <th>ID</th>
                <th>Username</th>
                <th>Email</th>
                <th>Balance</th>
            </thead>
            <tbody>
            </tbody>
        </table>
    </body>
</html>
```

# More examples
For more examples please search in `site` directory.
It contains basic *Phalcon* bootstrap page to show all Phalcon-DataTables functionality.
