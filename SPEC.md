# Nimn (निम्न) Specification
Working copy of Nimn data format specification.

Nimn is an object serialization specification like JSON.

It represents more dense form of data. And can also be known as Schema Aware Compressed Data Form.

Nimn format can represent structured types (objects and arrays) having structured or primitive types (strings, numbers, booleans, and null), with data, missing (undefined), and empty values ([], {}, "").

An object (collection of zero or more name/value pairs) serialized in Nimn format doesn't contain keys. Hence the order of values is important. Information of keys, called schema or the structure of object, can be maintained separately and it is required at the time of deserialization. Any change in order or type of keys may result error or invalid application object.

Any key presents in object but not in schema (definition of object) will be ignored at the time of serialization. Any key presents in schema but not in object will be marked by missing character. Check [bundary characters](https://github.com/nimndata/spec/blob/master/SPEC.md#bundary-characters) for more detail.

Serialization is conversion from application object into Nimn data format. Data in serialized format or nimn format is separated by boundary of fixed value characters.

Deserialization is conversion from Nimn data format into application object.



Example:

**Application Object**

```js
{
    "name" : "Some Name [nick name]",
    "age" : 33,
    "address" : "Some long address"
}
```

**Nimn Format**
```
{Some Name \[nick name\]|33|Some long address
```

*Note* : Characters `{` , `[`, `]`, `|` in Nimn data format are used to represent ASCII char 198 , 204, 185, and 179 repectively. Check [bundary characters](https://github.com/nimndata/spec/blob/master/SPEC.md#bundary-characters) for more detail.

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
[{Some Name \[nick name\]|33|Some long address{Some Name|35|A-3:34 Some long address]
```
*Note* : Characters `{` , `[`, `]`, `|` in Nimn data format are used to represent ASCII char 198 , 204, 185, and 179 repectively.

### Bundary characters

* **Object start** : ASCII char 198
* **Array start**   : ASCII char 204
* **Array end**     : ASCII char 185
* **Dynamic Value separater** : ASCII char 179

### Fixed Value Characters

As per the Nimn data format `boolean` value can be represented as following;

* *TRUE*  : ASCII char 181
* *FALSE* : ASCII char 183

When a value is not present / missing / undefined:

* *Missing premitive* : ASCII char 200
* *Missing Object / Array / List* : ASCII char 201

When a value is null;

* *Null premitive* : ASCII char 175
* *Null Object / Array / List* : ASCII char 176

When a value is empty;

* *Empty premitive* : ASCII char 177
* *Empty Object / Array / List* : ASCII char 178


**Scenario 1** In case of empty, nil, or missing map (object) or list (array) their respective boundary characters should be omitted.

*Application object*
```js
[]
```

*Nimn Format*
```
!
```

*Note* - `!` is representing ASCII char 177 in above example.

**Scenario 2** Two consecutive fixed value characters or the combination of dynamic and fixed value characters are not required to be separated by value separator character ( `|` ).

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
{YNsome nameN
```

*Note* : `{`, `Y` and `N` in above example should be replaced by ASCII characters 198, 181 and 183. Above example is for understanding purpose only.

### Values

Nimn data must be the serialized form of an object, array or a fixed value character representing missing, null, or empty value.


## Security Consideration

Nimn data format in itself is not executable. Hence it is safe to use. However as there is no restriction on the data other than backslashing characters being used by Nimn data format itself to define the data boundaries, it may contain binary data, regular expressions, scripting code, SQL queries etc. So the user application should convert it first into the application object instead of direct evaluation if they use the same format to represent the executable code.

Example:

Applying Limitations of Nimn format and Security consideration, a Nimn data string passed from `eval()` in javascript should give error instead of successful evaluation.
 
```js
var str = "alert('valid json object')";

nimnEncoder(str);//Error: Invalid input

var obj = {
    "key" : "alert('valid json object')"
}
var nimnFormat = nimnEncoder(obj);//Æalert('valid json object')
eval(nimnFormat);//Uncaught ReferenceError: Æalert is not defined
```

*Note* : As Nimn serializer/Encoder is just stripping field name out from serialized format, it may or may not need schema information. It completely depends how it is implemented.

## Customization

The aim of the Nimn data form is to minimize object representation of data by keeping schema information apart. However the user can minimize it further by specifying special/unique characters for the set of fixed values, or by applying some compression algorithm.

Such customizations can be used within the application boundary and can't be the part of this specification.

