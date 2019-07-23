---
parent: Query
grand_parent: Core ORM
nav_order: 7
---
# Filters 

**Neuron**'s query results might get narrowed by the filters. 

The `github.com/neuronlabs/neuron/query/filters` package contains all the necessary structures and methods that allows to specify what results we would like to obtain from the query.

The filtering system used by **Neuron** is based on the model's **collection**, **field** (optionally **subfield**), **operator** and the **values**. 

* [String Query Filter](#string-query-filters)
* [Filter Fields](#filterfield)
* [Operators](#operators)

## String Query Filters

The filter seperates the logic with the `[`, `]` brackets.
It consists of the collection, field, (optionally subfield), the [operator](#operators) and the values.

The form of the string filter:

```
filter[collection][field][$operator]=values
filter[collection][relationship-field][field][$operator]=values
filter[collection][relationship-field][related-field][field][$operator]=values
...
```


The filter for the **_cars_** collection on field **_id_** with the value that is **equal** to **_42_** would look as follows:

```
filter[cars][id][$eq]=42
```

In order to filter the field over it's relationship values the relationship field must have it's subfield defined within the filter

i.e. in order to filter the **_cars_** collection over it's relationship field **_driver_** - where the driver **_name_** is equal to **_Mark_** the following filter have to be applied: 

```
filter[cars][driver][name][$eq]=Mark
```

In order to add the filter to the given scope use the `Filter` method to the query.Scope:

```go
if err = s.Filter("filter[cars][driver][name][$eq]", "Mark"); err != nil {
    // error occurs if the field doesn't exists
}

```


## FilterField

The second way to create a filter is to create the `*filters.FilterField`  using the [ModelStruct](model.md#ModelStruct), it's [StructField](model.md#StructField), the `*filters.Operator` and the specific values.

The package `github.com/neuronlabs/neuron/query/filters` contains the `NewFilter` function that is used to create the FilterField

I.e. we want to create the filter to the `cars` collection query scope, that will filter the `doors` where it's numbers is greater than or equal to 4 

Then we should create it in a following way:

* Create the scope for the Cars field

```go
var c *Controller 
// Get the controller 
// we assume it is already created with the `Car` model

var cars []*Car

s, err := query.NewC(c, &cars)
if err != nil {
    panic(err)
}
```

* Get the scope's [ModelStruct](model.md#modelstruct) and the [StructField](model.md#structfield) we want in the filter.
 
```go
doorField, ok := s.Struct().Attr("doors")
if !ok {
    panic("No doors field found")
}
```

* Create and add new filter field

```go
// import "github.com/neuronlabs/neuron/query/filters"

err = s.FilterField(filters.NewFilter(doorField, filters.OpGreaterEqual, 4))
if err != nil {
    // the error might occur if the field is not found within the model's 
    // struct field
    panic(err)
}
```


## Operators

Neuron implements the following basic filter operators:

| ID | Name | Query String Value | Variable Name | Description |
| --: | :---- | :----------- | :------------- | :----------- |
| 1 | Equal | *$eq* | _*filters.OpEqual_ | Matches values that are equal to  the specified value |
| 2 | In | *$in* | _*filters.OpIn_ | Matches any values that are in the specified range of values` |
| 3 | Not Equal | *$ne* | _*filters.OpNotEqual_ | Matches values that are not euqal to the specified value | 
| 4 | Not In | *$nin* | _*filters.OpNotIn_ | Matches any values that are not in the specified range of values |
| 5 | Greater Than | *$gt* | _*filters.OpGreaterThan_ | Matches any value that  is greater than the specified value |
| 6 | Greater Than Equal | *$ge* | _*filters.OpGreaterEqual_ | Matches any value that  is greater than or equal to the specified value |
| 7 | Less Than | *$lt* | _*filters.OpLessThan_ | Matches any value that is smaller than the specified value |
| 8 | Less Than Equal | *$le* | _*filters.OpLessEqual_ | Matches any value that is smaller than or equal to the specified value |
| 9 | Contains | *$contains* | _*filters.Contains_ | The operator used for the string variables, matches any values that contains the specified string value |
| 10 | Starts With | *$startswith* | _*filters.StartsWith_ | Used for the string variables, matches any values that starts with the specified string value | 
| 11 | Ends With | *$endsswith* | _*filters.EndsWith_ | Used for the string variables, matches any values that ends with the specified string value |
| 12 | Is Null | *$isnull* | _*filters.IsNull_ | Matches all objects for which given field value is null |
| 13 | Not Null | *$notnull* | _*filters.NotNull_ | Matches all objects for which given field value is not null |

It is also allowed to have the custom operator, which might be used within the hooks and changed into the basic filters, or used in pair with the required handler for the specific repository.

### Custom Operators

Neuron allows to use the custom operators. In order to use it 
The operator should be registered in the `github.com/neuronlabs/neuron/query/filters` package as well as in the repository on which it would be used.

The custom operator should be created in the following way:

* Create new `*filters.Operator` object:

```go
myOperator := &filters.Operator{Name: "MyOperator", Raw:"$my"}
```

* The operator should have unique `Name` nad `Raw` fields. The `Raw` field should start with the '$' dollar sign.
* Then the operator should be registered in the query filters:

```go
if err = filters.RegisterOperator(myOperator); err != nil {
    panic(err)
}
```

* If we want to use this operator in the specified repostiory we should register it there as well (if the implementation allows it) - probably with the handler
* Otherwise the operator might be used within hooks and then replaced into the basic operators





