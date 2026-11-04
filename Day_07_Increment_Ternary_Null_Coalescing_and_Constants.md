# Day 7: Increment, Ternary, Null Coalescing and Constants

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Pre/post increment
- Compound assignment
- Ternary operator
- Null coalescing operator
- final constants

## Concept in very simple words

These operators make small decisions concise, but readable code is more important than using the shortest syntax.

## Mental model

Think of **Increment, Ternary, Null Coalescing and Constants** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
Integer attempt = 0;
attempt++; // Same as attempt = attempt + 1

Integer age = 20;
// Ternary chooses one of two values based on a Boolean condition.
String category = age >= 18 ? 'Adult' : 'Minor';

String suppliedName; // null because no value was assigned
// ?? provides a fallback only when the left value is null.
String displayName = suppliedName ?? 'Unknown User';

public class AppConstants {
    // final prevents another assignment after initialization.
    public static final Integer MAX_RETRY_COUNT = 3;
}
```

## How to read the example

- **Comment:** Ternary chooses one of two values based on a Boolean condition.
- **Comment:** ?? provides a fallback only when the left value is null.
- **Class declaration:** Defines a reusable blueprint or base type.
- **Comment:** final prevents another assignment after initialization.

## More examples for Day 7

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 7 Example');

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
    accounts.add(new Account(Name = 'Day 7 Example ' + index));
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

### Q1. What is Increment, Ternary, Null Coalescing and Constants?

**Answer:** These operators make small decisions concise, but readable code is more important than using the shortest syntax.

### Q2. Why is Increment, Ternary, Null Coalescing and Constants important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Increment, Ternary, Null Coalescing and Constants.

**Answer:** Use it when a business requirement directly needs the Day 7 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Increment, Ternary, Null Coalescing and Constants?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Increment, Ternary, Null Coalescing and Constants solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What is the difference between a primitive value and a collection or object?

**Answer:** A primitive represents one basic value such as an Integer or String. An object can contain multiple fields and behavior, while a collection groups multiple values or objects for processing.

### Q7. How do null values affect this topic?

**Answer:** Apex variables can be null. Check required values before calling methods, using indexes, performing arithmetic, or dereferencing an object. Use a fallback only when a default is valid for the business rule.
