# Day 60: Exception Handling in Apex

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Exception types
- try/catch/finally
- Specific-before-general catch order
- Null prevention
- Exception methods

## Concept in very simple words

try, catch and finally describe deliberate failure paths. Catch only when the code can recover, translate, log or rethrow meaningfully.

## Mental model

Think of **Exception Handling in Apex** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
try {
    insert contacts;
} catch (DmlException exceptionValue) {
    // DML-specific catch comes before the general Exception catch.
    System.debug(exceptionValue.getMessage());
    System.debug(exceptionValue.getDmlFieldNames(0));
    throw exceptionValue; // Rethrow when this method cannot safely recover
} catch (Exception exceptionValue) {
    // Final fallback for unexpected exceptions.
    System.debug(exceptionValue.getStackTraceString());
} finally {
    // Runs whether the try succeeded or failed. Use only for real cleanup.
}
```

## How to read the example

- **Insert DML:** Saves new records to Salesforce in one transaction.
- **Comment:** DML-specific catch comes before the general Exception catch.
- **Comment:** Final fallback for unexpected exceptions.
- **Comment:** Runs whether the try succeeded or failed. Use only for real cleanup.

## More examples for Day 60

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 60 Example');

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
    accounts.add(new Account(Name = 'Day 60 Example ' + index));
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

### Q1. What is Exception Handling in Apex?

**Answer:** try, catch and finally describe deliberate failure paths.

### Q2. Why is Exception Handling in Apex important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Exception Handling in Apex.

**Answer:** Use it when a business requirement directly needs the Day 60 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Exception Handling in Apex?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Exception Handling in Apex solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What production risk is most important for this topic?

**Answer:** The main risks are silent failures, unintended access, excessive resource consumption, repeated automation, and behavior that works only at small data volume. Address these explicitly in design and tests.

### Q7. How would you debug a failure?

**Answer:** Reproduce with the smallest representative data set, identify the transaction entry point, inspect logs and exceptions, track SOQL/DML/CPU usage, verify user permissions, and isolate which automation changed the record or caused re-entry.
