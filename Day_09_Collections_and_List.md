# Day 9: Collections and List

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Collection purpose
- List declaration and initialization
- Indexes
- Common List methods
- When to use List

## Concept in very simple words

A collection stores many values in one variable. A List keeps order, allows duplicates and supports access by index.

## Mental model

Think of collections as containers: a List is a numbered queue, a Set is a guest list with no duplicates, and a Map is a dictionary that finds a value from its key.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// A List keeps insertion order and can contain duplicates.
List<String> certifications = new List<String>();
certifications.add('Platform Developer I');
certifications.add('JavaScript Developer I');
certifications.add('Platform Developer I'); // Duplicate is allowed

System.debug(certifications[0]); // Indexes begin at zero
System.debug(certifications.size());

for (String certification : certifications) {
    System.debug(certification); // Process every element once
}
```

## How to read the example

- **Comment:** A List keeps insertion order and can contain duplicates.
- **Object creation:** Allocates a new class instance, collection or sObject in memory.
- **Loop:** Processes every value in a collection instead of assuming one record.

## List methods: complete practical catalog

| Method/property | Purpose | Example |
|---|---|---|
| `add(value)` | Append one item | `names.add('Riya');` |
| `add(index, value)` | Insert at a position | `names.add(0, 'First');` |
| `addAll(collection)` | Append many items | `names.addAll(otherNames);` |
| `clear()` | Remove every item | `names.clear();` |
| `clone()` | Shallow-copy the List | `List<Account> copy = accounts.clone();` |
| `deepClone(...)` | Clone sObjects and optionally relationships/IDs | `accounts.deepClone(false, true, false);` |
| `contains(value)` | Check membership | `names.contains('Riya')` |
| `equals(other)` | Compare List equality/order | `first.equals(second)` |
| `get(index)` | Read by zero-based index | `names.get(0)` |
| `[index]` | Short form of `get`/`set` | `names[0]` |
| `hashCode()` | Hash representation | Normally used by collections/framework code |
| `indexOf(value)` | First matching index or `-1` | `names.indexOf('Riya')` |
| `isEmpty()` | Check whether size is zero | `accounts.isEmpty()` |
| `iterator()` | Obtain an iterator | `Iterator<String> it = names.iterator();` |
| `lastIndexOf(value)` | Last matching index or `-1` | `names.lastIndexOf('Riya')` |
| `remove(index)` | Remove and return indexed item | `names.remove(0);` |
| `set(index, value)` | Replace indexed item | `names.set(0, 'Tanisha');` |
| `size()` | Number of items | `names.size()` |
| `sort()` | Sort comparable values in place | `names.sort();` |
| `toString()` | Text representation | `String.valueOf(names)` |

```apex
List<String> skills = new List<String>{'Apex', 'Flow'};
skills.add('LWC');                         // ['Apex', 'Flow', 'LWC']
skills.add(1, 'SOQL');                     // Insert at index 1
skills.addAll(new List<String>{'Git','CI'});// Append several values

Boolean hasApex = skills.contains('Apex'); // true
Integer flowPosition = skills.indexOf('Flow');
String firstSkill = skills.get(0);         // Same as skills[0]
skills.set(0, 'Advanced Apex');            // Replace the first item
String removed = skills.remove(1);         // Remove by index and return it
skills.sort();                             // Changes this List's order

if (!skills.isEmpty()) {
    System.debug('Count=' + skills.size());
}
```

### Shallow clone vs deep clone

```apex
List<Account> original = new List<Account>{new Account(Name='Acme')};
List<Account> shallow = original.clone();
shallow[0].Name = 'Changed';
// The same Account object is referenced, so original[0].Name also changes.

List<Account> deep = original.deepClone(false, true, false);
deep[0].Name = 'Independent';
// A new Account object was created, so the original object is not changed.
```

### List safety rules

- Check `isEmpty()` before reading index `0`.
- Valid indexes are `0` through `size() - 1`.
- Do not remove items from a List inside an enhanced `for` loop; use an index loop or prepare another List.
- For DML, pass the complete `List<sObject>` once rather than executing DML per item.

## Beginner checklist

1. Identify which Salesforce object or class owns the data.
2. Identify whether the work reads data, changes data, or only calculates a value.
3. Assume that the input may contain many records, not only one.
4. Keep queries and DML outside loops unless the platform feature explicitly behaves differently.
5. Decide how errors will be shown or logged.
6. Decide which user security must be enforced.

## Common mistakes

- Writing code before confirming the business rule and edge cases.
- Testing only one record and forgetting bulk execution.
- Printing values with `System.debug` but not asserting or handling the outcome.
- Using a complex pattern when a simpler Flow, formula or validation rule would be clearer.

## Interview questions

### Q1. What is Collections and List?

**Answer:** A collection stores many values in one variable.

### Q2. Why is Collections and List important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Collections and List.

**Answer:** Use it when a business requirement directly needs the Day 9 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Collections and List?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Collections and List solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What is the difference between a primitive value and a collection or object?

**Answer:** A primitive represents one basic value such as an Integer or String. An object can contain multiple fields and behavior, while a collection groups multiple values or objects for processing.

### Q7. How do null values affect this topic?

**Answer:** Apex variables can be null. Check required values before calling methods, using indexes, performing arithmetic, or dereferencing an object. Use a fallback only when a default is valid for the business rule.

### Q8. When would you choose List vs Set vs Map?

**Answer:** Choose List for ordered values and duplicates, Set for uniqueness and ID collection, and Map when you need fast lookup from a key to a value.

### Q9. How do collections help with governor limits?

**Answer:** Collections let code gather many IDs, issue one SOQL query, correlate results with Sets or Maps, and perform one bulk DML operation instead of repeating database work record by record.

### Q10. What is a common collection pattern in a trigger?

**Answer:** Collect unique parent IDs in a Set, query all parents once, construct a Map keyed by parent Id, then loop over Trigger.new and perform constant-time Map lookups.
