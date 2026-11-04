# Day 58: Batch Apex and Database.Stateful

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Database.Batchable
- start, execute and finish
- QueryLocator
- Batch size
- Database.Stateful and partial DML

## Concept in very simple words

Batch Apex processes very large data sets in separate scopes. Stateful preserves instance variables between scopes, with serialization overhead.

## Mental model

Think of asynchronous Apex as giving work to a background team. The user can finish the immediate action while the queued worker processes the longer task.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
public class ContactCleanupBatch
    implements Database.Batchable<SObject>, Database.Stateful {

    private Integer successCount = 0;
    private Integer failureCount = 0;

    public Database.QueryLocator start(Database.BatchableContext context) {
        return Database.getQueryLocator([
            SELECT Id, Description FROM Contact WHERE Description = null
        ]);
    }

    public void execute(Database.BatchableContext context, List<Contact> scope) {
        for (Contact contactRecord : scope) contactRecord.Description = 'Reviewed';
        for (Database.SaveResult result : Database.update(scope, false)) {
            if (result.isSuccess()) successCount++; else failureCount++;
        }
    }

    public void finish(Database.BatchableContext context) {
        System.debug('Success=' + successCount + ', Failure=' + failureCount);
    }
}
```

## How to read the example

- **Class declaration:** Defines a reusable blueprint or base type.
- **Return:** Sends a result back to the caller and ends the method.
- **SOQL:** Requests only the Salesforce data needed by the logic.
- **Loop:** Processes every value in a collection instead of assuming one record.
- **Condition:** Runs the enclosed code only when the business condition is true.

## More examples for Day 58

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 58 Example');

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
    accounts.add(new Account(Name = 'Day 58 Example ' + index));
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
- Asserting only that no exception occurred.
- Depending on existing org data.

## Interview questions

### Q1. What is Batch Apex and Database.Stateful?

**Answer:** Batch Apex processes very large data sets in separate scopes.

### Q2. Why is Batch Apex and Database.Stateful important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Batch Apex and Database.Stateful.

**Answer:** Use it when a business requirement directly needs the Day 58 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Batch Apex and Database.Stateful?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Batch Apex and Database.Stateful solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. How do you test this topic?

**Answer:** Create deterministic test data, execute the real entry point, assert the business outcome, include negative and bulk scenarios, and wrap asynchronous execution with Test.startTest and Test.stopTest when appropriate.

### Q7. What is the difference between all-or-none and partial success?

**Answer:** Normal DML commonly treats the submitted operation atomically. Database methods with allOrNone=false can return per-record SaveResult values so valid records can succeed while failures are captured.

### Q8. Which asynchronous tool would you choose?

**Answer:** Queueable is the general default for new asynchronous business logic, Batch handles very large data sets in scopes, Scheduled Apex starts work at a specified time, and Future is mainly appropriate for simple or legacy use cases.

### Q9. Why pass record IDs to asynchronous jobs?

**Answer:** The job runs later in a separate transaction. Passing IDs lets it query fresh record state and usually uses less serialized state than passing large object graphs.
