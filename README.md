# jQuery DataTables SSP MultiTables - Класс Server Side Processing с возможностью выборки из нескольких 

Класс SSP (Server Side Processing) для работы с [DataTables](https://datatables.net/) поддерживающий выборку данных из нескольких таблиц. 

Оригинальный файл и примеры работы можно найти в [репозитории DataTables](https://github.com/DataTables/DataTables)

Одинарные кавычки ставить (`) в именах полей ставить не надо. Они будут проставлены автоматически. 

Пример работы server_processing.php: 

```php
<?php 

/*
 * Модифицированный скрипт DataTables server-side processing.
 * Добавлена возможность выборки из нескольких таблиц 
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

//Для одной таблицы работает родной синтаксис:
//$table = "main_table_name";

//Определеяем список таблиц для запроса
//Для таблицы указываем представление. Первая таблица должна быть главной. 
$table = array(
    array('table'=>'main_table_name',  'as'=>'mt'),    
    array('table'=>'join1_table_name', 'as'=>'jt1', 'join_type'=>'INNER', 'join_on'=>'mt.field  = jt1.field'),
    array('table'=>'join2_table_name', 'as'=>'jt2', 'join_type'=>'LEFT',  'join_on'=>'mt.field1 = jt2.field')
); 

//Состав колонок для вывода в таблице DataTables
// Для колонки лучше указать представление 'as'
// Колонка указывается с представлиением таблицы: таблица.колонка
// Одинарные кавычки ставить (`) в именах полей ставить не надо. Они будут проставлены при формировнии текста запроса автоматически.  
$columns = array(
    array( 'db' => 'mt.field1',  'dt' => 0 ),
    array( 'db' => 'mt.field2',  'dt' => 1 ),    
    array( 'db' => 'mt.name',    'as'=>'field3', 'dt' => 2 ),    
    array( 'db' => 'jt1.field1', 'as'=>'field4', 'dt' => 3 ),
    array( 'db' => 'jt2.field5', 'as'=>'field5', 'dt' => 4 )       
);

//Ключевое поле для определения количества записей
$primaryKey = 'mt.id';

//Параметры подключения к БД
$sql_details = array(
    'user' => $db_user,
    'pass' => $db_password,
    'db'   => $db_name,
    'host' => $db_host
);

//Дополнительные условия на результат запроса. 
$where = "mt.field1 > 10";

require('ssp.class.php' );

echo json_encode(
	// Все параметры вызова соответствуют оригинальным
    SSP::complex( $_GET, $sql_details, $table, $primaryKey, $columns , '', $where)
);

?>
```


