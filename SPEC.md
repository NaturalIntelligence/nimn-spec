# निम्न (Nimn) Specification
Working copy of Nimn data format specification.

## Table of contents
* [Introduction](https://github.com/nimndata/spec/blob/master/SPEC.md#introduction)
* [Conventions Used in This Document](https://github.com/nimndata/spec/blob/master/SPEC.md#conventions-used-in-this-document)
* [Grammar](https://github.com/nimndata/spec/blob/master/SPEC.md#grammar)
    * [Boundary characters](https://github.com/nimndata/spec/blob/master/SPEC.md#boundary-characters)
    * [Fixed value characters](https://github.com/nimndata/spec/blob/master/SPEC.md#fixed-value-characters)
    * [Values](https://github.com/nimndata/spec/blob/master/SPEC.md#values)
* [Parser](https://github.com/nimndata/spec/blob/master/SPEC.md#parser)
* [IANA considerations](https://github.com/nimndata/spec/blob/master/SPEC.md#iana-considerations)
* [Customization / Extension](https://github.com/nimndata/spec/blob/master/SPEC.md#customization) 


## Introduction
Nimn is an object serialization specification like JSON.

It represents more dense form of data. And can also be known as `Schema Aware Data Format` or `Schema Less Data`

Nimn format can represent structured types (objects and arrays) having structured or primitive types (strings, numbers, booleans, and null), with data, missing (undefined), and empty values ([], {}, "").

An object (collection of zero or more name/value pairs) serialized in Nimn format doesn't contain keys. Hence the order of values is important. Information of keys, called schema or the structure of the object, can be maintained separately and it is required at the time of deserialization. Any change in order or type of keys may result error or invalid application object.

Example:

**Application Object**

```js
{
    "name" : "Some Name [nick name]",
    "age" : 33,
    "address" : "Some long address"
}
```

**Nimn Data**
```
{Some Name \[nick name\]|33|Some long address}
```

*Note* : Characters `{` , `[`, `]`, `|`, `}` in above Nimn data are used to represent ASCII char 182 , 187, 185, 179, and 180 respectively. Check [boundary characters](https://github.com/nimndata/spec/blob/master/SPEC.md#boundary-characters) for more detail.

## Conventions Used in This Document

* *schema* : It defines the structure of *ordered* key/type pairs where the key defines the name of the field and type can be either primitive: string, boolean, number (custom types are not the part of this specification), or array (list of single type either premitive or schema), or another schema.

Example:

```js
{
    "name" : "string",
    "age" : "number",
    "address" : "string",
    "hobbies" : ["string"],
    "projects" : {
        "title" : "string",
        "description" : "string"
    }
}
```

* *array* : Array represents the list of similar type of elements.
* *object* : Object represents an instance of the schema given above. It should not contain any key which is not defined in the schema. But schema may have some keys which are missing in object.
* *Dynamic value fields* : Dynamic value fields are the fields which can have any value like string, number, etc.
* *Fixed value fields* : Fixed value fields are the fields which have fixed set of values like boolean. (or like Enum in Java)

## Grammar

Nimn data is the sequence of data values, boundary characters, and fixed value characters (used instead of actual data values). If data value consist any boundary or fixed value character then it should be backslashed. 

Example

*Application Object*
```js
[
    {
        "name" : "Some Name [nick name]",
        "age" : 33,
        "address" : "Some long address"
    },
    {
        "name" : "Some Name",
        "age" : 35,
        "address" : "A-3:34 Some long address"
    }
]
```
*Nimn Format*
```
[{Some Name \[nick name\]|33|Some long address}{Some Name|35|A-3:34 Some long address}]
```
*Note* : Characters `{` , `[`, `]`, `|`, `}` in above Nimn data are used to represent ASCII char 182 , 187, 185, 179, and 180 repectively.

### Boundary characters

* **Object start** : ASCII char 182
* **Object end** : ASCII char 180
* **Array start**   : ASCII char 187
* **Array end**     : ASCII char 185
* **Dynamic Value separater** : ASCII char 179

### Fixed Value Characters

As per the Nimn data format `boolean` value can be represented as following;

* *TRUE*  : ASCII char 181
* *FALSE* : ASCII char 183

When a value is not present / missing / undefined:

* *Missing premitive* : ASCII char 184
* *Missing Object / Array / List* : ASCII char 186

When a value is null;

* *Null premitive* : ASCII char 175
* *Null Object / Array / List* : ASCII char 176

When a value is empty;

* *Empty premitive* : ASCII char 177
* *Empty Object / Array / List* : ASCII char 178

### Values

Nimn data must be the serialized form of an object, array or a fixed value character representing missing, null, or empty value.

## Parser

### Serialization
 Serialization is the process of converting application object into Nimn data format which confirm Nimn [grammar](https://github.com/nimndata/spec/blob/master/SPEC.md#grammar).

Points to be considered at the time of serialization.
 
* In case of empty, nil, or missing map (object) or list (array) fixed value characters should be used. And respective boundary characters should be omitted.

*Application object*
```js
[]
```

*Nimn Format*
```
!
```

*Note* - `!` is representing ASCII char 177 in above example.

* Starting and ending of an object and array should be marked with appropriate boundary character.

*Application object*
```js
[{"msg": "This is awesome"}]
```

*Nimn Format*
```
[{This is awesome}]
```

*Note* : Characters `{` , `[`, `]`, `}` in Nimn data format are used to represent ASCII char 182 , 187, 185, and 180 respectively. Check [boundary characters](https://github.com/nimndata/spec/blob/master/SPEC.md#boundary-characters) for more detail.

* If data value consist any Nimn supported character (boundary or fixed value character) then it should be backslashed.

**Application Object**

```js
{
    "name" : "Some Name [nick name]"
}
```

**Nimn Data**
```
{Some Name \[nick name\]}
```

* Order of the keys in schema, which defines  object structure, should be same as order of values in serialized data.


**Schema**

```js
{
    "name" : "string",
    "age" : "number,
    "address" : "string"
}
```

**Application Object**

```js
{
    "age" : 33,
    "name" : "Some Name [nick name]",
    "address" : "Some long address"
}
```

**Nimn Data**
```
{Some Name \[nick name\]|33|Some long address}
```

* Any key presents in application object but not in schema (definition of object) must be ignored.


**Schema**

```js
{
    "name" : "string",
    "address" : "string"
}
```

**Application Object**

```js
{
    "name" : "Some Name [nick name]",
    "age" : 33,
    "address" : "Some long address"
}
```

**Nimn Data**
```
{Some Name \[nick name\]|Some long address}
```

* Any key presents in schema but not in object will be marked by [missing character](https://github.com/nimndata/spec/blob/master/SPEC.md#boundary-characters).


**Schema**

```js
{
    "name" : "string",
    "age" : "number",
    "address" : "string"
}
```

**Application Object**

```js
{
    "name" : "Some Name [nick name]",
    "address" : "Some long address"
}
```

**Nimn Data**
```
{Some Name \[nick name\]|*|Some long address}
```

*Note* : Characters `|` , `*` in above Nimn data are used to represent ASCII char 179, and 184respectively.

* A list can have single type of element which can be either any premitive value, an object or a list.
* Data root should be either an object or a list.
* Two consecutive fixed value characters or the combination of dynamic and fixed value characters are not required to be separated by value separator character ( `|` ).

Example

*Application object*
```js
{
    "Human" : true,
    "Asian" : false,
    "Name" : "some name",
    "Programmer" : false
}
```

*Nimn Format*
```
{YNsome nameN}
```

*Note* : `{`, `Y`, `N`, and `}` in above example should be replaced by ASCII characters 182, 181, 183, and 180. Above example is for understanding purpose only.

### Deserialization
Deserialization is the process of converting Nimn data into application object.

All the points covered in serialization should be cosidered at the time of deserialization. In addition,

* It should error when data type don't match as per schema.
* It should error when Nimn data can't be mapped to given schema.
* If the serialized data has more fields than defined in schema, they should be ignored if they appears in last.


**Schema**

```js
{
    "name" : "string",
    "age" : "number"
}
```


**Nimn Data**
```
{Some Name \[nick name\]|30|Some long address}
```

**Application Object**

```js
{
    "name" : "Some Name [nick name]",
    "age" : 30
}
```


## [IANA Considerations](https://www.iana.org/assignments/media-types/application/vnd.nimn)

### Encoding considerations
8bit if UTF-8; binary if UTF-16 or UTF-32

### Security Consideration

Nimn data format in itself is not executable. Data encoded to Nimn format may contain binary data, regular expressions, scripting code, SQL queries etc. So the user application should convert it first into the application object instead of direct evaluation if they use the same format to represent the executable code.

Structure of the application object is not bundled with encoded data. It needs to be provided externally. Hence if there is any modification in structure of encoded data like extra data field is inserted, existing data field is deleted, or if the sequence of data field is changed in encoded data then Nimn decoder may error out. However, as Nimn format doesn't enforce any limit or restriction on encoded data other than backslashing Nimn supported characters, user should ensure the integrity or confidentiality of encoded data externally. 

### Applications which use this media:
Any application can use Nimn data format to exchange data between same or two different applications.

### Restrictions on usage:
none

## Customization

The aim of the Nimn data form is to minimize object representation of data by keeping schema information apart. However the user can minimize it further by specifying special/unique characters for the set of fixed values, or by applying some compression algorithm.

Such customizations can be used within the application boundary and can't be the part of this specification.