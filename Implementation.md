Here is an example which can help to implement nimn data format serializer and deserializer in various programming languages.

```js
var schema = {
    type : "list",
    detail : {
      type : "map",
      detail : [{
          name : "name",
          type : "string"
        },{
          name : "age",
          type : "number"
        },{
          name : "isHuman",
          type : "boolean"
        },{
          name : "address",
          type : "string"
        },{
          name : "hobbies",
          type : "list",
          detail : {
            type : "string"
          }
        },{
          name : "project",
          type : "map",
          detail: [{
              name: "title",
              type : "string"
            },{
              name: "description",
              type : "string"
            },{
              name: "status",
              type : "string"
            }
          ]
        }
      ]
    }
}

var jsObject = [{
        "name" : "somename",
        "isHuman" : true,
        "age": 32,
        "address" : "I'll not tell you",
        hobbies : [ 
            null
            , "not reading "+ parser.chars.missingPremitive +" book"
            , "watching \\"+ parser.chars.nilPremitive +" movie"
        ],
        project : {
            title : "nimn",
            //description : "it is 80% smaller",
            status : "rocking"
        }
    },{

    }
];

var nimnData = parser.chars.arrStart 
        + parser.chars.objStart 
            + "somename" + parser.chars.boundaryChar
            + "32" 
            + parser.chars.yes // Order of the keys should be maintained as per schema not the data
            + "I'll not tell you"
            + parser.chars.arrStart
                + parser.chars.nilPremitive
                + "not reading \\"+ parser.chars.missingPremitive +" book"  +  parser.chars.boundaryChar
                + "watching \\\\"+ parser.chars.nilPremitive +" movie"
            + parser.chars.arrEnd
            + parser.chars.objStart 
                    + "nimn" //No boundary char if next field is nimn char
                    +  parser.chars.missingPremitive
                    + "rocking" //last field can not have boundary char
            + parser.chars.objEnd
        + parser.chars.objEnd
        + parser.chars.emptyChar
    + parser.chars.arrEnd;
```
