Sometimes you need to store data that can't be stored in existing tables or existing tables are not optimal for the required data. In that case, you should create a database migration and a new manager that cares about saving/retrieving/deleting the data in that domain.

# Database setup

If you want to add a table for your manager to an existing data domain, then read the article about [adding new tables](../infrastructure/schema-migrations.md#add-new-tables).

!!! tip
    For more information about the available database schema methods, have a look into the documentation of the [Upscheme](https:://upscheme.org) project.

To create the new table in the database afterwards, you have to execute the setup tasks:

Laravel
: **php artisan aimeos:setup**

TYPO3
: **php vendor/bin/typo3 aimeos:setup** (or via the update script in the extension manager)

## Extend existing domain

For example, let's add a table to the **product** domain named *mshop_product_test*. Create a new `./<yourext>/setup/default/schema/product.php` file to your extension that creates the *mshop_product_test* table:

```php
return array(
    'table' => array(
        'mshop_product_test' => function( \Aimeos\Upscheme\Schema\Table $table ) {
            $table->engine = 'InnoDB';

            $table->id()->primary( 'pk_msprote_id' );
            $table->int( 'parentid' );
            $table->string( 'siteid' );
            $table->string( 'label' )->default( '' );
            $table->int( 'position' )->default( 0 );
            $table->smallint( 'status' )->default( 1 );
            $table->meta();

            $table->index( ['status', 'siteid', 'position'], 'idx_msprote_status_sid_pos' );
            $table->index( ['label', 'siteid'], 'idx_msprote_label_sid' );

			$table->foreign( 'parentid', 'mshop_product', 'id', 'fk_msprote_pid' );
        },
    ),
);
```

!!! note
    All columns beside *id* and *siteid* must have a default value if they are not required explicitely like *parentid*!

## New data domain

Adding a table for a totally new data domain, e.g. for the fictive **test** data domain requires creating a setup task `./<yourext>/setup/Test.php`. It creates the new *mshop_test* table:

```php
namespace Aimeos\Upscheme\Task;

class Test extends Base
{
    public function up()
    {
        $this->info( 'Creating test schema', 'v' );

        $this->db( 'db-test' )->table( $name, function( \Aimeos\Upscheme\Schema\Table $table ) {
        $table->engine = 'InnoDB';

        $table->id()->primary( 'pk_mstes_id' );
        $table->string( 'siteid' );
        $table->string( 'label' )->default( '' );
        $table->int( 'position' )->default( 0 );
        $table->smallint( 'status' )->default( 1 );
        $table->meta();

        $table->index( ['status', 'siteid', 'position'], 'idx_mstes_status_sid_pos' );
        $table->index( ['label', 'siteid'], 'idx_mstes_label_sid' );
    } );
    }
}
```

!!! note
    All columns beside *id* and *siteid* must have a default value if they are not required explicitely like *parentid*!

# Create manager

After the table has been created, you can implement the corresponding manager.

## Existing domain

Create a new file named `./<yourext>/src/MShop/Product/Manager/Test/Standard.php` with a class like this one:

```php
namespace Aimeos\MShop\Product\Manager\Test;

class Standard
	extends \Aimeos\MShop\Common\Manager\Base
	implements \Aimeos\MShop\Common\Manager\Iface
{
	public function getSaveAttributes() : array
	{
		return $this->createAttributes( [
			'parentid' => [
				'type' => 'int',
				'public' => false
			],
			'label' => [
			],
			'status' => [
				'type' => 'int',
			],
			'position' => [
				'type' => 'int',
				'label' => 'Position for sorting'
			],
		] );
	}
```

You must name the class *Standard.php* because it's the name the factories will use by default. Otherwise, you need to set the name of the manager using the *mshop/product/manager/test/name* configuration. Also extend your class from the *\Aimeos\MShop\Common\Manager\Base* class and implement the *\Aimeos\MShop\Common\Manager\Iface*!

## New domain

Create a new file named `./<yourext>/src/MShop/Test/Manager/Standard.php` with a class like this one:

```php
namespace Aimeos\MShop\Test\Manager;

class Standard
	extends \Aimeos\MShop\Common\Manager\Base
	implements \Aimeos\MShop\Common\Manager\Iface
{
	public function getSaveAttributes() : array
	{
		return $this->createAttributes( [
			'label' => [
			],
			'status' => [
				'type' => 'int',
			],
			'position' => [
				'type' => 'int',
				'label' => 'Position for sorting'
			],
		] );
	}
```

By default, you only have to implement the *getSaveAttributes()* method which must return the list of properties that can be managed by the class and stored in the corresponding table. The *id*, *siteid*, *ctime*, *mtime* and *editor* properties are added by default.

## Define the properties

The array returned by *getSaveAttributes()* must contain the column name as key, all properties in the array assigned to the key are optional and the **default type** of the column is assumed to be **string**. Available array properties for each key are:

* *label* : Label used in the search box of the admin backend
* *public* : Property is shown in the admin backend and assigned in the *fromArray()* method of the item
* *internalcode* : String that will be used instead of the key name as column name
* *type* : Type of the database column which can be:
    * bool
    * date
    * datetime
    * decimal
    * float
    * int
    * json
    * string

If one of your column names is a reserved word in the database, you must put the column name into quotes, e.g.:

```php
'key' => [
    'internalcode' => '"key"',
],
```

## Different table name

If you need a different table name, implement the *getTable()* method to return your custom table name:

```php
protected function getTable() : string
{
    return 'myapp_test';
}
```

## Find by code

Provided your new table contains rows with unique code values, you can also implement the *find()* method to retrieve this items by their code. Your class should implement the *Find* interface in that case too:

```php
class Standard
	extends \Aimeos\MShop\Common\Manager\Base
	implements \Aimeos\MShop\Common\Manager\Iface, \Aimeos\MShop\Common\Manager\Find\Iface
{
	public function getSaveAttributes() : array
	{
        // see above
    }


    public function find( string $code, array $ref = [], string $domain = null, string $type = null,
        ?bool $default = false ) : \Aimeos\MShop\Common\Item\Iface
    {
        return $this->findBase( ['code' => $code], $ref, $default );
    }
}
```

The *domain* and *type* arguments are only used if the *code* alone isn't unique but the combination with one or both of the other arguments are. The attribute manager is currently the only one which requires *domain* and *type* arguments and they are passed within the first argument of *findBase()* as additional filter arguments:

```php
public function find( string $code, array $ref = [], string $domain = null, string $type = null,
    ?bool $default = false ) : \Aimeos\MShop\Common\Item\Iface
{
    return $this->findBase( ['code' => $code, 'domain' => $domain, 'type' => $type], $ref, $default );
}
```

The keys of the array passed as first arguement are the same as the keys you've defined in *getSaveAttributes()*.

## Using the manager

Afterwards, you can create your new manager using the MShop factory and use all methods provided by managers like *create()*, *cursor()*, *delete()*, *filter()*, *get()*, *iterate()*, *save()* and *search()*

```php
// sub-manager of existing domain
$manager = \Aimeos\MShop::create( $this->context(), 'product/test' );
// new data domain
$manager = \Aimeos\MShop::create( $this->context(), 'test' )

$item = $manager->create()
    ->set( 'label', 'test label' )
    ->set( 'position', 2 )
    ->set( 'status', 1 );

$item = $manager->save( $item );
$item = $manager->get( $item->getId() );

$label = $item->label;
// or using get() with default value
$label = $item->get( 'label', 'default value' );

// standard methods
$id = $item->getId();
$siteid = $item->getSiteId();
$ctime = $item->getTimeCreated();
$mtime = $item->getTimeModified();
$editor = $item->getEditor();

$items = $manager->search( $manager->filter() );

$manager->delete( $items );
```
