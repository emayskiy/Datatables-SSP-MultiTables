# jQuery DataTables SSP MultiTables - Класс Server Side Processing с возможностью выборки из нескольких таблиц с группировкой данных

Класс SSP (Server Side Processing) для работы с [DataTables](https://datatables.net/) поддерживающий выборку данных из нескольких таблиц с группировкой данных

Оригинальный файл и примеры работы можно найти в [репозитории DataTables](https://github.com/DataTables/DataTables)

Пример работы server_processing.php: 

```php
<?php 
/*
 * Модифицированный скрипт DataTables server-side processing.
 * Добавлена возможность выборки из нескольких таблиц и группировки данных
 * 
 * Modified DataTables server-side processing script.
 * Added the ability to select from multiple tables and group data
 * 
 * Please note that this script is intentionally extremely simple to show how
 * server-side processing can be implemented, and probably shouldn't be used as
 * the basis for a large complex system. It is suitable for simple use cases as
 * for learning.
 *
 * See http://datatables.net/usage/server-side for full details on the server-
 * side processing requirements of DataTables.
 *
 * @license MIT - http://datatables.net/license_mit
 */

/*
* Для одной таблицы работает родной синтаксис с передачей таблицы в виде строки
*
* For one table, native syntax works with passing the table as a string
*/
//$table = "main_table_name";

/*
* Определеяем список таблиц для запроса
*
* Define a list of tables for the query
*/
$table = array(
    array('table'=>'main_table_name',  'as'=>'mt'),    
    array('table'=>'join1_table_name', 'as'=>'jt1', 'join_type'=>'INNER', 'join_on'=>'mt.field  = jt1.field'),
    array('table'=>'join2_table_name', 'as'=>'jt2', 'join_type'=>'LEFT',  'join_on'=>'mt.field1 = jt2.field')
); 

/*
* Состав колонок для вывода в таблице DataTables
* Для колонки лучше указать представление 'as'
* Колонка указывается с представлиением таблицы: таблица.колонка
* Одинарные кавычки ставить (`) в именах полей ставить не надо. Они будут проставлены при формировнии текста запроса автоматически.  
* Если в запросе используется группировка, то необходимо прописывать функцию группировки в group_func
*
* Composition of columns for output in the DataTables table
* For the column, it's better to specify the 'as' view
* The column is indicated with the table view: table.column
* You do not need to put single quotes (`) in field names. They will be affixed when forming the request text automatically.
* If grouping is used in the request, then it is necessary to register the grouping function in group_func
*/
$columns = array(
    array( 'db' => 'mt.field1',  'dt' => 0 ),
    array( 'db' => 'mt.field2',  'dt' => 1 ),    
    array( 'db' => 'mt.name',    'as'=>'field3', 'dt' => 2 ),    
    array( 'db' => 'jt1.field1', 'as'=>'field4', 'dt' => 3 ),
    array( 'db' => 'jt2.field5', 'as'=>'field5', 'group_func'=>'count', 'dt' => 4 )       
);

/*
* Ключевое поле для определения количества записей
*
* Key field for determining the number of records
*/
$primaryKey = 'mt.id';

/*
* Параметры подключения к БД
*
* Settings for connecting to the database
*/
$sql_details = array(
    'user' => $db_user,
    'pass' => $db_password,
    'db'   => $db_name,
    'host' => $db_host
);

/*
* Дополнительные условия на результат запроса. 
*
* Additional conditions for the result of the request.
*/
$where = "mt.field1 > 10";
$group_by = "mt.id";
$having = "mt.id < 25";

require('ssp.class.php' );

echo json_encode(
    /*
    * Добавлены параметры $group_by и $having. В параметре $table можно передавать массив. 
    *   Остальные параметры вызова соответствуют оригинальным. 
    *
    * Added $group_by and $having parameters. You can pass an array in the $table parameter.
    *   The rest of the call parameters correspond to the original.
    */
    SSP::complex( $_GET, $sql_details, $table, $primaryKey, $columns , '', $where, $group_by, $having)
);

?>
```


