新建Menu.php

```php
<?php
header('Access-Control-Allow-Origin:*');  
header('Access-Control-Allow-Methods:GET,POST');  
header('Access-Control-Allow-Headers:x-requested-with,content-type'); 

class Menu {
	// 菜单ID，这些都是从数据库取
	public $id = 0;
	// 菜单名
	public $name = '';
	// 连接地址
	public $link = '';
	// 图标
	public $icon = '';
	// 子菜单，是一个列表,必须初始化为一个空数组，否则前端会出错
	public $subMenu = []; 

	// 构造函数
	public function __construct($id, $n, $l, $icon = '') {
		$this->id   = $id;
		$this->name = $n;
		$this->link = $l;
		$this->icon = $icon;
	}
}

// 菜单项目
$userMang = new Menu('1000', '用户管理', '', 'user');
$userAdd  = new Menu('1001', '用户新增', '/user/add');
$userList = new Menu('1002', '用户列表', '/user/list');
$userMang->subMenu = [$userAdd, $userList];

// 站内信管理
$msgMang = new Menu('1010', '站内信管理', '', 'laptop');
$msgAdd  = new Menu('1011', '发布站内信', '/msg/add');
$msgList = new Menu('1012', '站内信列表', '/msg/list');
$msgMang->subMenu = [$msgAdd, $msgList];

header('Content-type:application/json');
exit(json_encode([$userMang,$msgMang]));
```



