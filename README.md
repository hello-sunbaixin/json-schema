## 文档贡献
-  [json-schema](http://json-schema.org/) json-schema官网
- [understanding-json-schema](https://spacetelescope.github.io/understanding-json-schema/about.html) **该官网详细列举每种数据类型如何定义**
- [生成jsonschema的工具](https://jsonschema.net/#/home)
- [jsonschema lint](https://jsonschemalint.com/#/version/draft-06/markup/json)
## 什么是JSON Schema？
JSON Schema 定义了如何基于 JSON 格式描述 JSON 数据结构的规范，进而提供数据校验、文档生成和接口数据交互控制等一系列能力
它的特性和用途，可以大致归纳为以下几点：
-  用于描述数据结构
-  用于构建人机可读的文档
-  用于生成模拟数据
-  用于校验数据，实现自动化测试

## JSON Schema可以解决下列有关一致性验证的问题。
1、  值的数据类型是否正确：可以具体规定一个值是数字、字符串等类型；  
2、  是否包含所需的数据：可以规定哪些数据是需要的，哪些是不需要的；  
3、  值的形式是不是我需要的：可以指定范围、最小值和最大值。  
## 如何定义一个json schema
字段解释：

### 基础字段
关键字 | 描述| 示例
---|---|---
$schema |关键字，表示遵循的草稿标准，不是必须字段|http://json-schema.org/draft-07/schema#
title | 题目|"json schema"
description| 描述 |"这是一段json-schema"
type | 字段类型|"object"
properties | 定义属性 |{}
required| 必须属性|true

### [object](https://spacetelescope.github.io/understanding-json-schema/reference/object.html)
关键字|	描述|	示例
---|---|---|
type|	类型|	.
properties|	定义属性|	
required|	必需属性	
maxProperties|	最大属性个数	
minProperties|	最小属性个数	
additionalProperties|是否允许有额外属性	|[示例](https://spacetelescope.github.io/understanding-json-schema/reference/object.html?highlight=patternproperties#properties)
Dependencies|	属性依赖|	[用法](https://spacetelescope.github.io/understanding-json-schema/reference/object.html?highlight=patternproperties#dependencies)
patternProperties|正则表达属性的格式|[用法](https://spacetelescope.github.io/understanding-json-schema/reference/object.html?highlight=patternproperties#pattern-properties)


### [array](https://spacetelescope.github.io/understanding-json-schema/reference/array.html)
关键字|	描述|示例
---|---|---
items|	array 每个元素的类型	.
minItems|	约束属性，数组最小的元素个数	
maxItems|	约束属性，数组最大的元素个数	
uniqueItems|	约束属性，每个元素都不相同	
additionalItems|是否可以有额外条目|[示例](https://spacetelescope.github.io/understanding-json-schema/reference/array.html#uniqueness)

### string
关键字|	描述|	示例
---|---|---
maxLength|	定义字符串的最大长度，>=0	.
minLength|	定义字符串的最小长度，>=0	
pattern|	用正则表达式约束字符串


### integer
关键字|	描述|	示例
---|---|---
minimum	|最小值	.
exclusiveMinimum|	如果存在 "exclusiveMinimum" 并且具有布尔值 true，如果它严格意义上大于 "minimum" 的值则实例有效。	
maximum|	约束属性，最大值	
exclusiveMaximum|	如果存在 "exclusiveMinimum" 并且具有布尔值 true，如果它严格意义上小于 "maximum" 的值则实例有效。	
multipleOf|	是某数的倍数，必须大于0的整数

### number
关键字|	描述|	示例
---|---|---
minimum|	最小值	.
exclusiveMinimum|	如果存在 "exclusiveMinimum" 并且具有布尔值 true，如果它严格意义上大于 "minimum" 的值则实例有效。	
maximum|	约束属性，最大值	
exclusiveMaximum|	如果存在 "exclusiveMinimum" 并且具有布尔值 true，如果它严格意义上小于 "maximum" 的值则实例有效。

### boolean

```
true or false
```
### null
### enum

```
{
  "type": "object",
  "properties": {
    "number":      { "type": "number" },
    "street_name": { "type": "string" },
    "street_type": { "type": "string",
                     "enum": ["Street", "Avenue", "Boulevard"]
                   }
  }
}

```
了解了上面的各个类型的定义及约定条件，就可以满足大部分情况了。但为了写出更好的json schema,我们再学习几个关键字
## [进阶](https://spacetelescope.github.io/understanding-json-schema/reference/combining.html)
### [$ref id](https://spacetelescope.github.io/understanding-json-schema/structuring.html?highlight=ref)
$ref 用来引用其它schema
### [definitions](https://spacetelescope.github.io/understanding-json-schema/structuring.html?highlight=definitions)
当一个schema写的很大的时候，可能需要创建内部结构体，再使用$ref进行引用
### allOf
意思是展示全部属性，建议用requires替代
### anyof
意思是展示任意属性，建议用requires替代和minProperties替代
### oneOf
属性之一
### not
不是某些属性

## 综合demo
```
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "http://openweb.baidu.com:3000/dist/schemas/page.json",
  "description": "OpenWeb Page 渲染器",
  
  "type": "object",
  "definitions":
  {
    "stringOrNull":
    {
      "description": "字符或者 null",
      "oneOf": [
      {
        "type": "string"
      },
      {
        "type": "null"
      }]
    },
     "string": {
            "description": "字符串格式",
            "type": "string"
        },
    "container":
    {
      "oneOf": [
      {
        "$ref": "#/definitions/string",
        "description": "当值为字符串时会被当做模板来处理，要指定其他渲染类型请使用对象定义如：\n{\n\n\t  \"type\": \"form\" \n } \n 可通过 $xxx 或者 ${xxx} 取值，或者直接用 lodash.template 模板引擎。\n\n"
      },
      {
        "$ref": "#/definitions/subRenderer"
      },
      {
        "items":
        {
          "$ref": "#/definitions/container"
        },
        "type": "array"
      }]
    },
     "subRenderer": {
        "oneOf":[{
               "$ref":"http://openweb.baidu.com:3000/dist/schemas/form.json" 
                }]
     }
  },
  "properties":
  {
    "$schema":
    {
      "description": "绑定 JSON 格式说明，可忽略！",
      "format": "uri",
      "type": "string"
    },
    "body":
        {
            "items":
                {
                    "$ref": "#/definitions/container"
                },
            "type": "array",
            "title": "页面内容区域"
        },
    "title":
    {
      "$ref": "#/definitions/stringOrNull",
      "description": "页面标题"
    }
  }
}

```
