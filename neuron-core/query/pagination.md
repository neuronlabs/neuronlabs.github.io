---
parent: Query
grand_parent: Core ORM
nav_order: 11
---
# Pagination

The neuron allows to reduce the number of the given list results. It provides the pagination system, on the `LimitOffset` or `Page` base.

## Limit Offset

The `LimitOffset` pagination type is well known from SQL. It limits the number of object with the `Limit` value and `Offset` says to skip as many objects from the current model's results.


```go
// Let's limit the results up to lenght of 10 starting from the fifth + 1
limit, offset := 10, 5

err = scope.Limit(limit, offset)
// an error might occur if the pagination is already set for the scope.
```


## Page

Page based pagination uses `PageSize` and `PageNumber` variable to define the result. Each page should have results of size: `PageSize`, and have it's `PageNumber` index.

`PageSize` defines the number of objects to return.
`PageNumber` is the index of current page. If each page contains: `PageSize` of objects, page number defines how many values should be skipped in the result.

```go
// Get results of page size '20' by skipping one page number (starting from 
// the '2' pageNumber.
pageNumber, pageSize := 2, 20

// an error might occur if the pagination is already set on the query's scope.
err = scope.Page(pageNumber, pageSize)
```




