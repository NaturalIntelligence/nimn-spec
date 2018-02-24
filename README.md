# spec
common standard for nimn (निम्न) form

## Object structure
Any field/key of the object can be broadly categoriized in the following

* object : consist other fields
* array : consist other repeated fields
* fixed value: fields with fixed set of data. Like boolean
* dynamic value : fields with any data
  * string
  * number
  * date

## About Transformed data

* It should not have field/key name.
* 2 conseucative dynamic value fields should have a boundary character in between

E.g.

```js
{projectDetails: [ {detail : {}, id : "gwl" }], name: "amit"}
```

Abov object can be transformed to

```
@gwl|amit
```

Following characters will be used to in transformed data and to explain any example

| ASCII  | char in examples  | meaning  |
|----|----|----|
| 186 |  | | boundry indeicator. To identify here a value is |
| 197 |  ^ | next field(set of fields) is/are repeated array item|
| 178 |  $ | empty value|
| 176 |  # | missing, null, undefined field|
| 177 |  @ | empty object or array|
| 0 |  ! | missing, null, undefined object or array|
| 217 |  Y | True|
| 218 |  N | False|


* Field level compression can also be used on user's choice.
