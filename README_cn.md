## 百度、阿里云翻译和免费的谷歌翻译驱动程序包，开箱即用
[**English 🇺🇸**](README.md)
### 翻译驱动程序包使用教程
#### 环境要求
- `PHP` >= 8.0

## 安装
```
composer require carlin/laravel-translate-drivers
```
## 发布配置文件
```php
php artisan vendor:publish --provider="Carlin\LaravelTranslateDrivers\TranslateDriversServiceProvider"
```
## 使用示例
### 百度

```php
use Carlin\LaravelTranslateDrivers\Facades\TranslateManager;

$query = '我喜欢你的冷态度 :test';
$res = TranslateManager::baidu()->translate($query, LangCode::EN);

//自定义配置参数
$res = TranslateManager::baidu(['app_id'=>'', 'app_key'=>''])->translate($query, LangCode::EN);
```


### Google

```php
use Carlin\LaravelTranslateDrivers\Facades\TranslateManager;

$query = '我喜欢你的冷态度 :test';
$res = TranslateManager::google()->translate($query, LangCode::EN);
```

### 阿里云翻译

```php
use Carlin\LaravelTranslateDrivers\Facades\TranslateManager;

$query = '我喜欢你的冷态度 :test';
$res = TranslateManager::alibabaCloud()->translate($query, LangCode::EN);
$res->getDst(); //翻译后的值
$res->getSrc(); //翻译前的值
$res->getOriginal(); //原始值

//自定义配置参数
$res = TranslateManager::alibabaCloud()->translate($query, LangCode::EN);
```

## 自定义驱动
```php
use Carlin\TranslateDrivers\Providers\AbstractProvider;
use Carlin\LaravelTranslateDrivers\Facades\TranslateManager;
class MyTranslateDriver extends AbstractProvider
{
    public function __construct(?string $app_id = null, ?string $app_key = null, array $config = [])
    {
        parent::__construct($app_id, $app_key, $config);
        
        //you code
    }

    protected function handlerTranslate(string $query, string $to = LangCode::EN, string $from = LangCode::AUTO): Translate
    {
        //you translation code
        return new Translate([
            'src'=>'',
            'dst'=>'',
        ]);
    }
    protected function mapTranslateResult(array $translateResult): array
    {
        //you translate Result code
        return [

        ];
    }
}

$query = '我喜欢你的冷态度 :test';
$res = TranslateManager::extend('my_driver', function ($allConfig) {
    $config = $allConfig['drivers']['my_driver'] ?? [];
    //you configuration code
    return new MyTranslateDriver(config:$config);
})->driver('my_driver')->translate($query);
```

## 保留翻译占位参数

```preserveParameters()``` 方法允许您在执行翻译时保留字符串中的某些参数。这在处理需要从翻译中排除特定占位符的本地化文件或模板引擎时特别有用。

默认正则表达式是 ```/:(\w+)/``` ，它涵盖以 : 开头的参数。对于翻译 Laravel 和其他框架的语言文件很有用。您还可以传递自定义正则表达式来修改参数语法。
```php
use Carlin\LaravelTranslateDrivers\Facades\TranslateManager;
use Carlin\TranslateDrivers\Supports\Provider;
use Carlin\TranslateDrivers\Supports\LangCode;

$query = '我喜欢你的冷态度 :test';
$res = TranslateManager::google()->preserveParameters()->translate($query, LangCode::EN); //I like your cold attitude :test
```

或者使用自定义正则表达式:

```php
use Carlin\LaravelTranslateDrivers\Facades\TranslateManager;
use Carlin\TranslateDrivers\Supports\Provider;
use Carlin\TranslateDrivers\Supports\LangCode;

$query = '我喜欢你的冷态度 {{test}}';
$res = TranslateManager::google()->preserveParameters('/\{\{([^}]+)\}\}/')->translate($query, LangCode::EN); //I like your cold attitude :test
```


## 如果您有更好的翻译驱动，欢迎提交 PR
