# EasyTpl

[![License](https://img.shields.io/github/license/phppkg/easytpl.svg?style=flat-square)](LICENSE)
[![Php Version](https://img.shields.io/badge/php-%3E=8.0-brightgreen.svg?maxAge=2592000)](https://packagist.org/packages/phppkg/easytpl)
[![GitHub tag (latest SemVer)](https://img.shields.io/github/tag/phppkg/easytpl)](https://github.com/phppkg/easytpl)
[![Actions Status](https://github.com/phppkg/easytpl/workflows/Unit-Tests/badge.svg)](https://github.com/phppkg/easytpl/actions)

⚡️ Simple and fastly template engine for PHP

> **[中文说明](README.zh-CN.md)**

## Features

- It's simple, lightweight and fastly.
  - No learning costs, syntax like PHP template
  - It is simply processed and converted into native PHP syntax
  - Compatible with PHP native syntax
- support simple echo print syntax. eg: `{{= $var }}` `{{ $var }}` `{{ echo $var }}`
- support all control syntax. such as `if,elseif,else;foreach;for;switch`
- support chained access array value. eg: `{{ $arr.0 }}` `{{ $map.name }}` `{{ $map.user.name }}`
- More secure, the output will be processed automatically through `htmlspecialchars` by default
  - You can set to disable output filtering or manually use the `raw` filter
- support php builtin function as filters. eg: `{{ $var | ucfirst }}`
- support add custom filters.
  - default builtin filters: `upper` `lower` `nl`
- support add custom directive.
- support comments in templates. eg: `{{# comments ... #}}`

## Install

- Required PHP 8.0+

**composer**

```bash
composer require phppkg/easytpl
```

## Quick start

```php
use PhpPkg\EasyTpl\EasyTemplate;

$tplCode = <<<'CODE'
My name is {{ $name | strtoupper }},
My develop tags:

{{ foreach($tags as $tag) }}
- {{ $tag }}

{{ endforeach }}
CODE;

$t = new EasyTemplate();

$str = $t->renderString($tplCode, [
    'name' => 'inhere',
    'tags' => ['php', 'go', 'java'],
]);

echo $str;
```

**Output**:

```text
My name is INHERE,
My develop tags:

- php
- go
- java
```

### More usage

**config template**

```php
use PhpPkg\EasyTpl\EasyTemplate;

$t = EasyTemplate::new([
    'tplDir'   => 'path/to/templates',
    'allowExt' => ['.php', '.tpl'],
]);

// do something ...
```

更多设置：

```php
/** @var PhpPkg\EasyTpl\EasyTemplate $t */
$t->disableEchoFilter();
$t->addFilter($name, $filterFn);
$t->addFilters([]);
$t->addDirective($name, $handler);
```

**echo variable**

The following statements are the same, can be used to print out variable values

```php
{{ $name }}
{{= $name }}
{{ echo $name }}
```

> By default, the output result will be automatically processed through `htmlspecialchars`,
> unless disabled or manually used `raw` filter

- Set to disable output filtering `$t->disableEchoFilter()`
- Disable output filtering in the template `{{ $name | raw }}`

**chained access array**

Can use `.` to quick access array value.

```php
$arr = [
    'val0',
    'subKey' => 'val1',
];
```

Use in template:

```php
First value is: {{ $arr.0 }} // val0
'subKey' value is: {{ $arr.subKey }} // val1
```

**add comments**

```text
{{# comments ... #}}{{ $name }} // inhere
```

multi lines:

```text
{{#
 this
 comments
 block
#}}{{ $name }} // inhere
```

## Use Filters

Default built-in filters: 

- `upper` - equals `strtoupper`
- `lower` - equals `strtolower`
- `nl` - append newline `\n`

### Using the filters

You can use the filters in any of your templates.

**Regular usage**:

```php
{{ 'inhere' | ucfirst }} // Inhere 
{{ 'inhere' | upper }} // INHERE
```

**Chained usage**:

```php
{{ 'inhere' | ucfirst | substr:0,2 }} // In
{{ '1999-12-31' | date:'Y/m/d' }} // 1999/12/31
```

**Passing non-static values**:

```php
{{ $name | ucfirst | substr:0,1 }}
{{ $user['name'] | ucfirst | substr:0,1 }}
{{ $userObj->name | ucfirst | substr:0,1 }}
{{ $userObj->getName() | ucfirst | substr:0,1 }}
```

**Passing variables as filter parameters**:

```php
{{
    $suffix = '￥';
}}

{{ '12.75' | add_suffix:$suffix }} // 12.75￥
```

### Custom filters

```php
use PhpPkg\EasyTpl\EasyTemplate;

$tpl = EasyTemplate::new();
// use php built function
$tpl->addFilter('upper', 'strtoupper');

// add more
$tpl->addFilters([
    'last3chars' => function (string $str): string {
        return substr($str, -3);
    },
]);
```

Use in template:

```php
{{
  $name = 'inhere';
}}

{{ $name | upper }} // INHERE
{{ $name | last3chars }} // ere
{{ $name | last3chars | upper }} // ERE
```

## Custom directives

You can use the directives implement some special logic.

```php
$tpl = EasyTemplate::new();
$tpl->addDirective(
    'include',
    function (string $body, string $name) {
        /** will call {@see EasyTemplate::include()} */
        return '$this->' . $name . $body;
    }
);
```

Use in template:

```php

{{ include('part/header.tpl', ['title' => 'My world']) }}

```

## Dep packages

- [toolkit/fsutil](https://github.com/php-toolkit/fsutil)
- [toolkit/stdlib](https://github.com/php-toolkit/stdlib)

## License

[MIT](LICENSE)
