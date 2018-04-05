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

There are 4 concepts in Nimn format;

1. *Schema*                     : Helps to map the field name of the application object with values in Nimn data.
2. *Data*                       : Serialized dense form of the application object.
3. *Boundary characters*        : Helps to identify boundary of Map (Object), List ( Array) and dynamic values in Nimn data.
4. *Fixed Value characters*     : Represents unique and fixed value for a field.

### Schema

A schema represents the structure of the application object. Though the structure can be derived from the instance of the object, but there is a possibility that an instance doesn't consist all the fields. Hence there is the need to specify the schema separately.

Example

```js
{
    "name" : "string",
    "age" : "number",
    "address" : "string"    
}
```

There is no standard definition of schema. It is purely depends on the programming language or serializer who is converting the application object to nimn data format.

### Data

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

If the value of any field have the boundary or fixed value character as value, they should be backslashed as given in the above example.

Empty / Null or Nil / Undefined / Missing values are represented by fixed value characters and can not be skipped while serializing from application object to nimn data format. See [Fixed Value Characters](https://github.com/nimndata/spec/blob/master/SPEC.md#fixed-value-characters) section for more detail.

### Bundary characters

* **Object start** : Curly/Middle opening bracket ( `{` ) is used to represent the starting of an object.
* **Array start**   : Square/Big opening bracket ( `[` ) is used to represent the starting of an array/list.
* **Array end**     : Square/Big closing bracket ( `]` ) is used to represent the end of an array/list.
* **Dynamic Value separater** : Pipe sign ( `|` ) is used to separate two consecutive dynamic fields.

*Note* :  Dynamic value fields are the fields which can have any value like address, age, etc. Fixed value fields are the fields which have fixed set of values like boolean, state etc.

### Fixed Value Characters

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

## Implementation Guidelines
**Scenario 1** In case of empty, nil, or missing map (object) or list (array) their repective boundary characters should be omitted.

Example

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

*Note* : `Y` and `N` in above example should be replaces by ASCII characters 217 and 218. Above example is for understanding purpose only.

## Customization

The aim of the Nimn data form is to minimize object representation of data by keeping schema information apart. However the user can minimize it further by specifying special/unique characters for the set of fixed values, or by applying some compression algorithm.

Such customizations can be used within the application boundary and can't be the part of this specification.

