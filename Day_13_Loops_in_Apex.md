# Day 13: Loops in Apex

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Traditional for loop
- Enhanced for loop
- SOQL for loop
- while and do-while
- Iterating Map keys

## Concept in very simple words

A loop repeats work. The correct loop depends on whether you need an index, each element, a condition, or query-result chunking.

## Mental model

Think of **Loops in Apex** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
List<String> fruits = new List<String>{'Apple', 'Mango', 'Banana'};

// Use an index when position matters.
for (Integer index = 0; index < fruits.size(); index++) {
    System.debug(index + ': ' + fruits[index]);
}

// Use enhanced for when only the value matters.
for (String fruit : fruits) {
    System.debug(fruit);
}

// Avoid forgetting the increment in a while loop.
Integer indexValue = 0;
while (indexValue < fruits.size()) {
    System.debug(fruits[indexValue]);
    indexValue++;
}
```

## How to read the example

- **Object creation:** Allocates a new class instance, collection or sObject in memory.
- **Comment:** Use an index when position matters.
- **Loop:** Processes every value in a collection instead of assuming one record.
- **Comment:** Use enhanced for when only the value matters.
- **Comment:** Avoid forgetting the increment in a while loop.

## More examples for Day 13

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 13 Example');

// Act: run one clear operation so its effect is easy to observe.
insert accountRecord;

// Observe: query only the fields needed to verify the result.
Account saved = [SELECT Id, Name FROM Account WHERE Id = :accountRecord.Id];
System.debug(saved);
```

### Example 2: Convert the same idea to collection-safe code

```apex
List<Account> accounts = new List<Account>();
for (Integer index = 1; index <= 3; index++) {
    // Build records in memory inside the loop. This does not use a database operation.
    accounts.add(new Account(Name = 'Day 13 Example ' + index));
}

// One DML statement saves the complete collection after the loop.
insert accounts;
```

### Why these examples are written this way

- The first example isolates one concept and is easier for a beginner to debug.
- The second example introduces the production habit of processing collections.
- The query selects only fields required for verification.
- DML remains outside the loop to protect the transaction from governor-limit failures.

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

### Q1. What is Loops in Apex?

**Answer:** A loop repeats work.

### Q2. Why is Loops in Apex important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Loops in Apex.

**Answer:** Use it when a business requirement directly needs the Day 13 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Loops in Apex?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Loops in Apex solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What is the difference between a primitive value and a collection or object?

**Answer:** A primitive represents one basic value such as an Integer or String. An object can contain multiple fields and behavior, while a collection groups multiple values or objects for processing.

### Q7. How do null values affect this topic?

**Answer:** Apex variables can be null. Check required values before calling methods, using indexes, performing arithmetic, or dereferencing an object. Use a fallback only when a default is valid for the business rule.
