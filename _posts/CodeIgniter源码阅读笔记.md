title: CodeIgniter源码阅读笔记
categories: PHP##分类
tags: [PHP,CodeIgniter,源码]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,CodeIgniter,源码##文章关键词，多关键词格式为 keyword1,keywords2,...
description: CodeIgniter源码阅读分析
date: 2016/12/12 21:00:25
---

# 入口文件

## index.php

入口文件`index.php`中主要定义了一些全局路径变量如`BASEPATH`和`APPPATH`这种常用的变量
并且可以配置代码的部署环境，最后`require`真正的核心文件`CodeIgniter.php`

## CodeIgniter.php

位于`system/core/`目录下，该文件是最主要的核心文件，他负责引入全局需要用到的一些关键的类，比如`Common.php`和`Controller.php`，这些类处于`system/core`目录下
`CodeIgniter.php`有点策略模式和工厂模式的意味了，很好的解决了代码耦合的问题，代码可拓展性很高，这也是我喜欢CI的一个地方
CI的异常处理机制是利用PHP的error_handler和exception_handler进行处理，实现代码如下：

``` php
//全局的异常处理都在这里进行
set_error_handler('_error_handler');
set_exception_handler('_exception_handler');
register_shutdown_function('_shutdown_handler');
```
这里的error_handler和exception_handler都是利用CI的Loader进行Exception类的实例化并且进行异常处理，同时Loader加载Log类进行日志记录

# 核心类库

## Common.php

该文件定义了一些全局当中需要用到的函数，辅助框架运行，如`is_cli()`判断来判断是否cli运行环境


## DB_query_builder.php

分析ORM的语法，主要利用`explode`函数进行词语分割
这里我们看看select方法的实现
``` php
//解析select中的数据，放入数组，便于driver使用
public function select($select = '*', $escape = NULL)
{
	if (is_string($select))
	{
		$select = explode(',', $select);
	}

	// If the escape value was not set, we will base it on the global setting
	is_bool($escape) OR $escape = $this->_protect_identifiers;

	foreach ($select as $val)
	{
		$val = trim($val);

		if ($val !== '')
		{
			$this->qb_select[] = $val;
			$this->qb_no_escape[] = $escape;

			if ($this->qb_caching === TRUE)
			{
				$this->qb_cache_select[] = $val;
				$this->qb_cache_exists[] = 'select';
				$this->qb_cache_no_escape[] = $escape;
			}
		}
	}

	return $this;
}
```
通过explode分割select里面的参数，并放入数组当中，利用`qb_cache_exists`记录已有的操作类型，`qb_cache_select`记录需要select的数据，最后可以通过这些数据当中的数据进行不同数据库dialect下SQL语句的生成
另外query_builder中每个方法都返回一个`$this`指针，实现了CI中ORM"object->method()->method()"这样的链式操作。

在database目录下的都是主要的db driver，是一个一个的abstract类，用于被不同的数据库类型db drvier继承。

## Loader.php
Loader就有点单例模式和策略模式的意味在里面了
``` php
/**
 * Nesting level of the output buffering mechanism
 *
 * @var	int
 */
protected $_ci_ob_level;

/**
 * List of paths to load views from
 *
 * @var	array
 */
protected $_ci_view_paths =	array(VIEWPATH	=> TRUE);

/**
 * List of paths to load libraries from
 *
 * @var	array
 */
protected $_ci_library_paths =	array(APPPATH, BASEPATH);

/**
 * List of paths to load models from
 *
 * @var	array
 */
protected $_ci_model_paths =	array(APPPATH);

/**
 * List of paths to load helpers from
 *
 * @var	array
 */
protected $_ci_helper_paths =	array(APPPATH, BASEPATH);

/**
 * List of cached variables
 *
 * @var	array
 */
protected $_ci_cached_vars =	array();

/**
 * List of loaded classes
 *
 * @var	array
 */
protected $_ci_classes =	array();

/**
 * List of loaded models
 *
 * @var	array
 */
protected $_ci_models =	array();

/**
 * List of loaded helpers
 *
 * @var	array
 */
protected $_ci_helpers =	array();

/**
 * List of class name mappings
 *
 * @var	array
 */
protected $_ci_varmap =	array(
	'unit_test' => 'unit',
	'user_agent' => 'agent'
);
```
上面是Loader的属性，用来保存已经加载过的工具，使实例化出来的工具类或者方法保持单一性，不会重复生成造成浪费。
例如`$_ci_models`，在控制器中利用代码`$this->load->model('Model_name')`就是首先判断`$_ci_models`中是否存在Model_name这一model，如不存在则进行加载并保存到已加载model的数组当中，大大提高了性能和内存的利用率。
`$this->load`便是`Loader`类被保存在全局唯一的实例Controller当中的load属性。

我们可以看一下Loader中model方法的实现
``` php
/**
 * Model Loader
 *
 * Loads and instantiates models.
 *
 * @param	string	$model		Model name
 * @param	string	$name		An optional object name to assign to
 * @param	bool	$db_conn	An optional database connection configuration to initialize
 * @return	object
 */
public function model($model, $name = '', $db_conn = FALSE)
{
	if (empty($model))
	{
		return $this;
	}
	elseif (is_array($model))
	{
		foreach ($model as $key => $value)
		{
			is_int($key) ? $this->model($value, '', $db_conn) : $this->model($key, $value, $db_conn);
		}

		return $this;
	}

	$path = '';

	// Is the model in a sub-folder? If so, parse out the filename and path.
	//获取判断model是否存在子路径中
	if (($last_slash = strrpos($model, '/')) !== FALSE)
	{
		// The path is in front of the last slash
		$path = substr($model, 0, ++$last_slash);

		// And the model name behind it
		$model = substr($model, $last_slash);
	}

	if (empty($name))
	{
		$name = $model;
	}

	if (in_array($name, $this->_ci_models, TRUE))
	{
		return $this;
	}

	//单例模式
	$CI =& get_instance();
	if (isset($CI->$name))
	{
		throw new RuntimeException('The model name you are loading is the name of a resource that is already being used: '.$name);
	}

	//验证数据库连接
	if ($db_conn !== FALSE && ! class_exists('CI_DB', FALSE))
	{
		if ($db_conn === TRUE)
		{
			$db_conn = '';
		}

		$this->database($db_conn, FALSE, TRUE);
	}

	// Note: All of the code under this condition used to be just:
	//
	//       load_class('Model', 'core');
	//
	//       However, load_class() instantiates classes
	//       to cache them for later use and that prevents
	//       MY_Model from being an abstract class and is
	//       sub-optimal otherwise anyway.
	if ( ! class_exists('CI_Model', FALSE))
	{
		$app_path = APPPATH.'core'.DIRECTORY_SEPARATOR;
		if (file_exists($app_path.'Model.php'))
		{
			require_once($app_path.'Model.php');
			if ( ! class_exists('CI_Model', FALSE))
			{
				throw new RuntimeException($app_path."Model.php exists, but doesn't declare class CI_Model");
			}
		}
		elseif ( ! class_exists('CI_Model', FALSE))
		{
			require_once(BASEPATH.'core'.DIRECTORY_SEPARATOR.'Model.php');
		}

		$class = config_item('subclass_prefix').'Model';
		if (file_exists($app_path.$class.'.php'))
		{
			require_once($app_path.$class.'.php');
			if ( ! class_exists($class, FALSE))
			{
				throw new RuntimeException($app_path.$class.".php exists, but doesn't declare class ".$class);
			}
		}
	}

	$model = ucfirst($model);
	if ( ! class_exists($model, FALSE))
	{
		foreach ($this->_ci_model_paths as $mod_path)
		{
			if ( ! file_exists($mod_path.'models/'.$path.$model.'.php'))
			{
				continue;
			}

			require_once($mod_path.'models/'.$path.$model.'.php');
			if ( ! class_exists($model, FALSE))
			{
				throw new RuntimeException($mod_path."models/".$path.$model.".php exists, but doesn't declare class ".$model);
			}

			break;
		}

		if ( ! class_exists($model, FALSE))
		{
			throw new RuntimeException('Unable to locate the model you have specified: '.$model);
		}
	}
	elseif ( ! is_subclass_of($model, 'CI_Model'))
	{
		throw new RuntimeException("Class ".$model." already exists and doesn't extend CI_Model");
	}

	$this->_ci_models[] = $name;
	$CI->$name = new $model();
	return $this;
}
```

## Config.php

在Config类当中，最核心的部分就是Config类的两个属性，用于保存已加载过的配置文件，在CI中这种保存变量的方法随处可见，这种设计方式的好处是显而易见的，大大提高了内存的利用率，降低重复加载带来的性能开销。我们来看一下这两个属性的定义以及他的注释
``` php
/**
 * List of all loaded config values
 *
 * @var	array
 */
//保存加载过的配置
public $config = array();

/**
 * List of all loaded config files
 *
 * @var	array
 */
//已加载的配置文件
public $is_loaded =	array();
```
每一次加载新的配置的时候，Config类先对配置是否已加载然后再作出相应的操作，如果已加载则利用`array_merge()`函数来进行已加载配置数组的合并

## Controller.php

乍一看Controller类下没有写什么，就只有几十行代码，甚至大部分都是注释，但是Controller是整个CI的心脏，利用`单例模式`承担起整个应用的正常运行。
所有实例化出来的类都统一放到Controller当中，是系统架构中的核心部分。全局通过`get_instance()`这一个方法来获取Controller单例实体，保证各个工具类的单例性。
整个系统就是靠着Controller这一个枢纽运行起来的。


# 路由分发

整个应用的路由分发是由Router类来进行分发的，联合URI类，进行url的分析，在此就不在赘述了。
可以仔细阅读`system/core`下的`Router.php`和`URI.php`