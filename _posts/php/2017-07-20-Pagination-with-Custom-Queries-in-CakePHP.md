---
layout: default
title: 在CakePHP中自定义查询分页
meta: 在CakePHP中如何实现自定义查询的分页功能在你的控制器使用。
source: http://www.blinno.net
category: php
---
<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


首先引用
```
use Cake\Datasource\ConnectionManager;
```
数据库配置
```
ConnectionManager::config('cloudcon-api', array(
    'url' => 'mysql://root:root@localhost/test?encoding=utf8&timezone=UTC&cacheMetadata=true',
));

$this->connection = ConnectionManager::get('cloudcon-api');
```
分页函数
```
public function getListByPage($query, $order, $current_page = 1, $not_page = false, $limit = 20) {
    $sql = $query;

    if ($order) {
        $sql .= ' order by '.$order;
    }

    //要返回的数组
    $results = array();

    if ($not_page) {
        $results['data'] = $this->connection->execute($sql)->fetchAll('assoc');

        return $results;
    }

    $count = $this->connection->execute($sql)->rowCount();

    // 获取总页数
    $page_count = ceil($count / $limit);

    // 验证当前请求页码是否大于总页数
    if ($current_page > $page_count) {
        return $results;
    }

    // Adding LIMIT Clause
    if ($limit) {
        $sql .= ' limit '.(($current_page - 1) * $limit).', '.$limit;
    }

    $data = $this->connection->execute($sql)->fetchAll('assoc');

    if ($data) {
        $results['success'] = true;
        $results['data'] = $data;
    }

    $has_next_page = false;
    if ($page_count - $current_page) {
        $has_next_page = true;
    }

    $has_prev_page = false;
    if ($current_page > 1) {
        $has_prev_page = true;
    }

    $results['pagination'] = array(
        'page_count' => $page_count,
        'current_page' => $current_page,
        'has_next_page' => $has_next_page,
        'has_prev_page' => $has_prev_page,
        'count' => $count,
    );

    return $results;
}
```

调用

```
public function index() {
    $params = $this->request->data;
    $order = $params['order'] ? $params['order'] : '';
    $current_page = $params['current_page'] ? $params['current_page'] : 1;
    $results = $this->getListByPage('select * from `auditlog`', 'auditid desc', $order, $current_page);
    $this->set(compact('results'));
    $this->set('_serialize', array('results'));
}
```
