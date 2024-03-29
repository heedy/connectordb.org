<!-- THIS FILE IS AUTO-GENERATED. Edits can be made at https://github.com/connectordb/pipescript/tree/master/resources/docs/transforms -->

# map
*Splits the script by the first argument's value, creating new instances of the second argument's script.*

The map transform is an example of a transform which hijacks its second argument. Please note that it is only lightly related to the standard map function in most programming languages. It splits datapoints along its first argument, and runs independent scripts on each value:


Suppose your data stream is the following:
```json
[{
  "steps": 14,
  "activity": "walking"
},{
  "steps": 10,
  "activity": "running"
},{
  "steps": 12,
  "activity": "walking"
},{
  "steps": 5,
  "activity": "running"
}]
```


Mapping by activity:
```
map($("activity"), $("steps"):sum )
```

gives you
```json
[{
  "walking": 26,
  "running": 15
}]
```

The map transform split the dataset by its first argument (`$("activity")`), and performed the second transform (`$("steps"):sum`) on each subset independently. This allows directly returning the sum of datapoints where you were walking and running.

The map transform is frequently used in conjunction with the `reduce` transform for quick analysis.

Very common use for the map transform is splitting by time periods. For example, to get the total number of steps taken per weekday (suppose steps is a number stream, where the data value is number of steps taken)

```
map(weekday,sum)
```

One issue with the above transform is that it gives a total sum of all datapoints. We
might want to compute the *average* for a weekday

```
map(weekday, imap(day,sum):reduce(mean))
```

Note the imap transform within the map. It functions in exactly the same way as map, but returns all intermediate values (which allows it to be embedded in map). The above transform first splits by weekday, then further splits by day. It sums up the steps taken per day and finds their mean. That leaves with daily averages per weekday.

Another way to perform the same calculation is:

```
while(day==next:day,sum) | map(weekday,mean)
```

This sums up datapoints for each day, and only maps the summed datapoints. This is the recommended method for such computations.


---

#### Transform Details
<table class='pipescriptargs'><thead><tr><th>One-To-One</th><th>Stateless</th><th>Peek</th><th>Input Schema</th><th>Output Schema</th></tr></thead><tr><td>False</td><td>False</td><td>False</td><td></td><td></td></tr></table>

### Arguments
<table class='pipescriptargs'><thead><tr><th>#</th><th>Description</th><th>Optional</th><th>Constant</th><th>Hijacked</th><th>Default</th></tr></thead><tr><td>1</td><td>The value to split on. This must be something that can be converted to string.</td><td>False</td><td>False</td><td>False</td><td></td></tr><tr><td>2</td><td>The script to instantiate for each different value of the first argument.</td><td>False</td><td>False</td><td>True</td><td></td></tr></table>
