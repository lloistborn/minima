---
layout: post
title: Java 8 Stream forEach, Filter, Map and Reduce
---

This post gives a trivial example about Java 8 streams feature.
This `Stream` API is completely different from `InputStream` and `OutputStream` eventhough
they are sounds similar. `Stream` API playing a big part on bringing functional programming into Java.

> In functional programming, a monad is a structure that represents computations defined as
> sequences of steps. A type with a monad structure defines what it means to chain operations,
> or nest functions of that type together.

The tutorial given below only provide how to use Stream using `forEach`, `filter`, `map` and `reduce`.

## How Streams Work
A stream represents a sequence of elements and supports different kind of operationss to perform computations upon those elements:

```java
List<String> myList = Arrays.asList("a1", "a2", "a3", "a4", "b1", "b2", "c1", "c3", "c2");

myList.stream()
	.filter(s -> s.starstWith("c"))
	.map(String::toUpperCase)
	.sorted()
	.forEach(System.out::println);
```
Output:
```
c1
c2
c3
```
Stream operations are either Intermediate (return stream so we can chain multiple operations) 
or Terminal (return void or non-stream result).

In above example `filter`, `map` and `sorted` are intermediate operations, while `forEach` is terminal operation.

Below examples will show how to loop a `List` and a `Map` with the new Java 8 API.

### forEach
Loop a `Map` with `forEach` and lambda expression.
```java
Map<String, Integer> items = new HashMap<>();

items.put("A", 10);
items.put("B", 10);
items.put("C", 10);
items.put("D", 70);
items.put("E", 100);

// stream() and filter()
items.stream()
	.filter((k, v) -> v > 0 && v < 50)
	.forEach((k, v)->{
		System.out.println("Item : " + k + " Count : " + v);
	});
```

Loop a `List` with `forEach` and lambda expression.
```java
List<String> items = new ArrayList<>();
items.add("Mohammed");
items.add("Yuri");
items.add("Cha");
items.add("Doe");

// Lambda
items.forEach(item -> System.out.println(item));
```
### Filter
Filter a `List` and `collect()` to convert a stream into a `List`.
```java
List<String> studentName = Arrays.asList("hector", "hana", "stone");

List<String> result = studentName.stream()	// Convert list to stream
	.filter(name -> !"hana".equals(name))	// remove "hana" hana from list
	.collect(Collectors.toList());			// collect the output and convert Stream to List

// print using method reference
result.forEach(System.out::println);
```

### Map
In Java 8, `Map` let us convert an object into something else.

Just for example, below using List of object using HashMap.
The use of Entity instead of HashMap is way better for more serious code.

Sample json object:
```json
[{"name": "koko", "age": 24 },
{ "name": "jahe", "age": 31 },
{ "name": "santi", "age": 44 }]
```
Java code to create that structure using `List of Map` is like this:
```java
List<HashMap<String, Object> listEmployee = new ArrayList<HashMap<String, Object>>();
HashMap<String, Object> employee = new HashMap<String, Object>();
employee.put("name", "koko");
employee.put("age", 24);
listEmployee.add(employee);

employee = new HashMap<String, Object>();
employee.put("name", "jahe");
employee.put("age", 31);
listEmployee.add(employee);

employee = new HashMap<String, Object>();
employee.put("name", "santi");
employee.put("age", 44);
listEmployee.add(employee);
```

We want to mutate attribute from my `Map` so beside having `name` and `age`,
We can put new attribute `grade`. Here is how we mutate them directly using `map()` + lambda expression.
```java
listEmployee.stream()
	.map(employee -> {
		HashMap<String, Object> mapEmployee = new HashMap<String, Object>((Map) employee) // Convert employee to HashMap
		mapEmployee.put("grade", "Manager"); // add new field 'grade' with value 'manager' for each object
		return mapEmployee;
	})
	.collect(Collectors.toList()); // Collect stream and convert to List
```

### Reduce
`stream()` `reduce()` method can perform many reducing task like 
get the sum of numbers stored in list, array, etc,
concatenate string stored. Unlike the `map`, `filter` and `forEach` methods, `reduce` method expects
two arguments: an identity element, and a lambda expression. You can think of the identity element as an element which does not alter the result of the reduction operation. For example, if you are trying to find the product of all the elements in a stream of numbers, the identity element would be the number 1.

The lambda expression you pass to the reduce method must be capable of handling two inputs: a partial result of the reduction operation, and the current element of the stream. If you are wondering what a partial result is, it’s the result obtained after processing all the elements of the stream that came before the current element.

The following is a sample code snippet which uses the reduce method to concatenate all the elements in an array of String objects:
```java
String[] myArray = { "this", "is", "a", "sentence" };
String result = Arrays.stream(myArray)
    .reduce("", (a,b) -> a + b);           
```

Here we will pass three arguments identity, accumulator and combiner in reduce() method. This method with these three arguments is used in parallel processing. Combiner works with parallel stream only, otherwise there is nothing to combine. 

`reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)`

```java
List<Integer> list2 = Arrays.asList(2, 3, 4);
// Here result will be 2*2 + 2*3 + 2*4 that is 18. 
int res = list2.parallelStream().reduce(2, (s1, s2) -> s1 * s2, (p, q) -> p + q);
System.out.println(res);

```

### Sample Case
Here in this example i want to add new field to the map and compare `List` of `HashMap` and get an object with minimum value of age;
```java
final Comparator<HashMap<String, Object>> comp = (l1, l2) -> {
    HashMap<String, Object> map1 = new HashMap<String, Object>((Map) l1);
    HashMap<String, Object> map2 = new HashMap<String, Object>((Map) l2);

    return map1.get("age").toString() == map2.get("age").toString();
};

HashMap<String, Object> result = (HashMap<String, Object>) listEmployee.stream()
    .map(s -> {
        HashMap<String, Object> mapEmployee = new HashMap<String, Object>((Map) s);
        mapEmployee.put("grade", "manager");
        return mapEmployee;
    })
    .min(comp)
    .get();
```

### Conclusion
If you have computationally intensive map operations, or if you expect your streams to be very large, you should consider using parallel streams instead. Fortunately, its very easy to convert any stream into its parallel equivalent. All you need to do is call its parallel method.

I’d also like to let you know that if you prefer not to use lambda expressions while working with the map, filter, and reduce methods, you can always use method references instead.

To learn more about the Streams API and the other methods it has to offer, you can refer to its documentation.

References:
- [winterbe.com/posts/2014/07/31/java8-stream-tutorial-examples] [winterbe]
- [http://www.baeldung.com/foreach-java] [foreach]
- [https://www.sitepoint.com/java-8-streams-filter-map-reduce] [sitepoint]

[winterbe]: winterbe.com/posts/2014/07/31/java8-stream-tutorial-examples
[foreach]: http://www.baeldung.com/foreach-java
[sitepoint]: https://www.sitepoint.com/java-8-streams-filter-map-reduce/