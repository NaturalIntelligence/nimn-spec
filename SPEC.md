# Nimn (निम्न) Specification
Working copy of Nimn data format specification.

Nimn is an object serialization specification like JSON.

It represents more densed form of data. And can also be known as Schema Aware Compressed Data Form.

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

There are 3 concepts of Nimn format;

1. *Schema*                       : Helps to identify the field name of application object.
2. *Data*                            : Serialized densed form of application object.
3. *Boundary characters*   : Helps to separate field's value from Nimn data.
4. *Fixed Value characters*          : represents unique and fixed value for a field.

## Schema

A schema represents the structure of the application object. Though the structure can be drived from the instance of the object but there is a possibility that an instance don't consist all the fields. Hence there is the need to specify the schema separately.

Example

```js
{
    "name" : "string",
    "age" : "number",
    "address" : "string"    
}
```

There is no standard defined for schema. It is purely depends on the programming language or tool who is converting application object to nimn data format.

## Data

Data is the flat form of application object where all the values are separated by either boundary characters or value characters.

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

If the value of any field have the bundary or fixed value character as value, they should be backslashed as given in above example.

Empty / Null or Nil / Undefined / Missing values are repreneted by fixed value characters and can not be skipped while serializing from application object to nimn data format. See [Fixed Value Characters](#Fixed Value Characters) section for more detail.

## Bundary characters

* **Object start** : Curly/Middle opening bracket ( `{` ) is used to represent the starting of an object.
* **Array start**   : Square/Big opening bracket ( `[` ) is used to represent the starting of an array/list.
* **Array end**     : Square/Big closing bracket ( `]` ) is used to represent the end of an array/list.
* **Dynamic Value separater** : Pipe sign ( `|` ) is used to separate two consuecative dynamic fields.

*Note* :  Dynamic value fields are the fields which can have any value like address, age etc. Fixed value fields are the fields which have fixed set of values like boolean, state etc.

## Fixed Value Characters

As per the Nimn data format `boolean` value can be represented as following;

* *TRUE*  : ASCII char 217
* *FALSE* : ASCII char 218

When a value is not present / missing / undefined:

* *Missing premitive* : ASCII char 200
* *Missing Object / Array / List* : ASCII char 201

When a value is null;

* *Null premitive* : ASCII char 175
* *Null Object / Array / List* : ASCII char 176

When a value is empty;

* *Empty premitive* : ASCII char 177
* *Empty Object / Array / List* : ASCII char 178


