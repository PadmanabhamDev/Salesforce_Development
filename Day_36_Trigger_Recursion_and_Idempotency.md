# Day 36: Trigger Recursion and Idempotency

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Causes of recursion
- Static Boolean limitation
- Static Set guard
- Idempotent design
- Bulk chunks in one transaction

## Concept in very simple words

Recursion happens when automation performs DML that invokes the same automation again. Prefer idempotent logic and record-aware guards over one global Boolean.

## Mental model

Think of **Trigger Recursion and Idempotency** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
public class TriggerRunGuard {
    private static Set<Id> processedAccountIds = new Set<Id>();

    public static Set<Id> firstTimeOnly(Set<Id> candidateIds) {
        Set<Id> remaining = candidateIds.clone();
        remaining.removeAll(processedAccountIds);
        processedAccountIds.addAll(remaining);
        return remaining;
    }
}

// A Set is safer than one Boolean because it tracks exactly which records ran.
Set<Id> idsToProcess = TriggerRunGuard.firstTimeOnly(Trigger.newMap.keySet());
```

## How to read the example

- **Class declaration:** Defines a reusable blueprint or base type.
- **Object creation:** Allocates a new class instance, collection or sObject in memory.
- **Return:** Sends a result back to the caller and ends the method.
- **Comment:** A Set is safer than one Boolean because it tracks exactly which records ran.

## More examples for Day 36

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 36 Example');

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
    accounts.add(new Account(Name = 'Day 36 Example ' + index));
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
- Putting SOQL or DML inside a record loop.
- Using one static Boolean as a universal recursion solution.

## Interview questions

### Q1. What is Trigger Recursion and Idempotency?

**Answer:** Recursion happens when automation performs DML that invokes the same automation again.

### Q2. Why is Trigger Recursion and Idempotency important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Trigger Recursion and Idempotency.

**Answer:** Use it when a business requirement directly needs the Day 36 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Trigger Recursion and Idempotency?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Trigger Recursion and Idempotency solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. Why must a trigger be bulkified?

**Answer:** Salesforce can invoke the trigger for many records in one transaction. Bulkification keeps query and DML usage nearly constant as record count grows.

### Q7. When should logic run in before vs after context?

**Answer:** Use before context to validate or modify the records being saved without extra DML. Use after context when record IDs or changes to related records are required.

### Q8. How do you prevent recursion safely?

**Answer:** Prefer idempotent logic. When a guard is necessary, track processed record IDs and operations rather than relying on one static Boolean for the whole transaction.
