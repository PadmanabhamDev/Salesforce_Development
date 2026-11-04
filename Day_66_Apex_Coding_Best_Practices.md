# Day 66: Apex Coding Best Practices

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- No SOQL/DML in loops
- One trigger per object
- Logic-less trigger
- Meaningful tests
- Naming and reusable code

## Concept in very simple words

Good Apex is bulk-safe, secure, testable, readable and organized into focused layers. Simplicity is preferable to unnecessary framework complexity.

## Mental model

Think of **Apex Coding Best Practices** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// Trigger: only delegates.
trigger OpportunityTrigger on Opportunity (before insert, before update) {
    OpportunityTriggerHandler.beforeSave(Trigger.new);
}

public class OpportunityTriggerHandler {
    public static void beforeSave(List<Opportunity> records) {
        // Method accepts a List and therefore works for one or 200 records.
        for (Opportunity opportunityRecord : records) {
            if (opportunityRecord.Amount != null && opportunityRecord.Amount < 0) {
                opportunityRecord.Amount.addError('Amount cannot be negative.');
            }
        }
    }
}
```

## How to read the example

- **Comment:** Trigger: only delegates.
- **Trigger declaration:** Registers Apex to run automatically for the listed object events.
- **Class declaration:** Defines a reusable blueprint or base type.
- **Comment:** Method accepts a List and therefore works for one or 200 records.
- **Loop:** Processes every value in a collection instead of assuming one record.
- **Condition:** Runs the enclosed code only when the business condition is true.

## More examples for Day 66

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 66 Example');

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
    accounts.add(new Account(Name = 'Day 66 Example ' + index));
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
- Confusing record sharing with CRUD and field-level security.

## Interview questions

### Q1. What is Apex Coding Best Practices?

**Answer:** Good Apex is bulk-safe, secure, testable, readable and organized into focused layers.

### Q2. Why is Apex Coding Best Practices important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Apex Coding Best Practices.

**Answer:** Use it when a business requirement directly needs the Day 66 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Apex Coding Best Practices?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Apex Coding Best Practices solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What production risk is most important for this topic?

**Answer:** The main risks are silent failures, unintended access, excessive resource consumption, repeated automation, and behavior that works only at small data volume. Address these explicitly in design and tests.

### Q7. How would you debug a failure?

**Answer:** Reproduce with the smallest representative data set, identify the transaction entry point, inspect logs and exceptions, track SOQL/DML/CPU usage, verify user permissions, and isolate which automation changed the record or caused re-entry.
