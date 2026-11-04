# Day 53: SeeAllData and Test Data Utility Classes

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- SeeAllData=false
- Test data factories
- Environment-independent tests
- Preparing records for query filters

## Concept in very simple words

Tests should not depend on organization data. A data factory creates deterministic records that work in every environment.

## Mental model

Think of **SeeAllData and Test Data Utility Classes** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
@IsTest
public class TestDataFactory {
    public static Account account(String name) {
        return new Account(Name=name, Industry='Technology');
    }

    public static Contact contact(Id accountId, String email) {
        return new Contact(
            LastName='Tester',
            AccountId=accountId,
            Email=email
        );
    }
}

// Every test creates its own predictable records; no production data is required.
Account accountRecord = TestDataFactory.account('Acme');
insert accountRecord;
```

## How to read the example

- **Class declaration:** Defines a reusable blueprint or base type.
- **Object creation:** Allocates a new class instance, collection or sObject in memory.
- **Comment:** Every test creates its own predictable records; no production data is required.
- **Insert DML:** Saves new records to Salesforce in one transaction.

## More examples for Day 53

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 53 Example');

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
    accounts.add(new Account(Name = 'Day 53 Example ' + index));
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

### Q1. What is SeeAllData and Test Data Utility Classes?

**Answer:** Tests should not depend on organization data.

### Q2. Why is SeeAllData and Test Data Utility Classes important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for SeeAllData and Test Data Utility Classes.

**Answer:** Use it when a business requirement directly needs the Day 53 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with SeeAllData and Test Data Utility Classes?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a SeeAllData and Test Data Utility Classes solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. How do you test this topic?

**Answer:** Create deterministic test data, execute the real entry point, assert the business outcome, include negative and bulk scenarios, and wrap asynchronous execution with Test.startTest and Test.stopTest when appropriate.

### Q7. What is the difference between all-or-none and partial success?

**Answer:** Normal DML commonly treats the submitted operation atomically. Database methods with allOrNone=false can return per-record SaveResult values so valid records can succeed while failures are captured.
