Tp5-Doc
===============
基于 thinkphp5 的doc文档扩展


[TOC]

## 说明
thinkphp5编写的自动生成文档功能；
 
 - 文档生成
 > 简洁，优雅，不需要额外的文档工具;
 
 
## 安装
- 如果想在你的TP5项目中使用,那么可以直接使用
```
composer require curselby/tp5-doc
```
- 如果是新项目先要创建tp5项目,然后再require

```
composer create-project topthink/think api  --prefer-dist
composer require curselby/tp5-doc
```
- 使用生成文档 需要在public/static/ 下 安装hadmin
```
cd /public/static/
git clone  https://github.com/curselby/hadmin.git
```

## 使用

1. 创建Doc文档显示控制器
    wiki 继承 Tp5Doc\doc\Doc;

2. 配置路由
```php
   'wiki'=>'demo/Wiki/index',//文档
```
 
3.配置文档显示目录

```php
    /**
     * 获取文档
     * @return mixed
     */
    public static function getApiDocList()
    {
        //todo 可以写配置文件或数据
        $apiList = Config::get('api_doc');
        return $apiList;
    }
```


>可以改写次方法以存储以无限级的方式，为了方便采用的是配置方式

tp5 增加额外配置  创建application/extra/api_doc.php 文件


```php
return [
    '1' => ['name' => '测试文档', 'id' => '1', 'parent' => '0', 'class'=>'','readme' =>''],//下面有子列表为一级目录
    '2' => ['name' => '获取权限', 'id' => '2', 'parent' => '1', 'class'=>'','readme' => '/doc/md/auth.md'],//没有接口的文档，加载markdown文档
    '3' => ['name' => '用户接口', 'id' => '3', 'parent' => '1', 'readme' => '','class'=>\app\test\controller\User::class],//User接口文档
];
```
![wiki](https://raw.githubusercontent.com/liushoukun/dawn-api-demo/master/public/doc/images/wiki.png)

|参数|必须|备注|作用|
|:---:|:---:|:---:|:---:|
|name|true|接口列表名称|显示列表名称|
|id|true|主键|生成列表所用|
|parent|true|生成列表所用|
|class|true|接口位置|用于生成具体接口文档|
|readme|true|markdown|可以生成没有接口的文档，比如一些说明 module和controller为空,readme填文档路径|


3.具体接口文档配置


- 接口描述部分(类文件的注释)

```php
/**
 * Class User
 * @title 用户接口
 * @url /v1/user
 * @desc  有关于用户的接口
 * @version 1.0
 * @readme /doc/md/user.md
 */
class User extends Base{}
```


|参数|必须|备注|作用|
|:---:|:---:|:---:|:---:|
|title|true|接口标题|显示列表名称|
|url|true|请求地址|用户显示|
|desc|true|接口描述|显示描述|
|version|false|版本号|版本号|
|readme|false|markdown文档|可以编写信息文档|


![ClassDoc](https://raw.githubusercontent.com/liushoukun/dawn-api-demo/master/public/doc/images/demo12.png)


- 具体接口文档

 1. 接口描述信息(注释填写)

```php
       /**
        * @title 获取用户信息
        * @desc 获取用户信息
        * @readme /doc/md/method.md
        */
      public function getResponse(\think\Request $request){}

```


|参数|必须|备注|作用|
|:---:|:---:|:---:|:---:|
|title|true|接口标题|显示列表名称|
|desc|true|接口描述|显示描述|
|readme|false|markdown文档|可以编写信息文档|

   2.请求参数
   
   
```php
    /**
     * 参数规则
     * @name 字段名称
     * @type 类型
     * @require 是否必须
     * @default 默认值
     * @desc 说明
     * @range 范围
     * @return array
     */
    public static function getRules()
    {
        $rules = [
                //共用参数
                'all'=>[
                    'time'=> ['name' => 'time', 'type' => 'int', 'require' => 'true', 'default' => '', 'desc' => '时间戳', 'range' => '',]
                ],

                'get'=>[
                    'id' => ['name' => 'id', 'type' => 'int', 'require' => 'true', 'default' => '', 'desc' => '用户id', 'range' => '',]
                ],
                'post'=>[
                    'username' => ['name' => 'username', 'type' => 'string', 'require' => 'true', 'default' => '', 'desc' => '用户名', 'range' => '',],
                    'age' => ['name' => 'age', 'type' => 'int', 'require' => 'true', 'default' => '18', 'desc' => '年龄', 'range' => '0-200',],
                ]
        ];
        //合并父级类参数
        return array_merge(parent::getRules(),$rules);
    }
```


![data](https://raw.githubusercontent.com/liushoukun/dawn-api-demo/master/public/doc/images/demo14.png)
  

   3. 返回参数(注释填写)
   
   
```php
     * @return int id ID
     * @return string username 错误信息
     * @return int age 年龄
```


|参数|必须|备注|
|:---:|:---:|:---:|
|第一个参数|true|类型|
|第二个参数|true|参数名|
|第三个参数|true|参数说明|


>类型填写规则


```php
'string'    => '字符串',
'int'       => '整型',
'float'     => '浮点型',
'boolean'   => '布尔型',
'date'      => '日期',
'array'     => '数组',
'fixed'     => '固定值',
'enum'      => '枚举类型',
'object'    => '对象',
```
![return](https://raw.githubusercontent.com/liushoukun/dawn-api-demo/master/public/doc/images/demo15.png)
  
整体效果
![all](https://raw.githubusercontent.com/liushoukun/dawn-api-demo/master/public/doc/images/demo16.png)
![all](https://raw.githubusercontent.com/liushoukun/dawn-api-demo/master/public/doc/images/demo17.png)
![all](https://raw.githubusercontent.com/liushoukun/dawn-api-demo/master/public/doc/images/demo18.png)
 

  



