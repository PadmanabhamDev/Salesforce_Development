# Day 64: with sharing, without sharing and inherited sharing

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- with sharing
- without sharing
- inherited sharing
- Caller/callee behavior
- Record access vs CRUD/FLS

## Concept in very simple words

Sharing keywords control record-level sharing behavior for a class. They do not automatically enforce object or field permissions.

## Mental model

Think of security as a building with several checkpoints: entry to the building, entry to a room, access to a cabinet, and permission to read a specific document.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// Record-level sharing is enforced inside this class.
public with sharing class AccountSearchService {
    public static List<Account> search(String nameValue) {
        return [SELECT Id, Name FROM Account WHERE Name=:nameValue];
    }
}

// inherited sharing makes the caller's sharing mode explicit.
public inherited sharing class AccountFormatter {
    public static String label(Account accountRecord) {
        return accountRecord.Name;
    }
}

// Sharing keywords do not replace CRUD/FLS enforcement such as USER_MODE.
```

## How to read the example

- **Comment:** Record-level sharing is enforced inside this class.
- **SOQL:** Requests only the Salesforce data needed by the logic.
- **Comment:** inherited sharing makes the caller's sharing mode explicit.
- **Return:** Sends a result back to the caller and ends the method.
- **Comment:** Sharing keywords do not replace CRUD/FLS enforcement such as USER_MODE.

## More examples for Day 64

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 64 Example');

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
    accounts.add(new Account(Name = 'Day 64 Example ' + index));
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

### Q1. What is with sharing, without sharing and inherited sharing?

**Answer:** Sharing keywords control record-level sharing behavior for a class.

### Q2. Why is with sharing, without sharing and inherited sharing important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for with sharing, without sharing and inherited sharing.

**Answer:** Use it when a business requirement directly needs the Day 64 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with with sharing, without sharing and inherited sharing?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a with sharing, without sharing and inherited sharing solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What production risk is most important for this topic?

**Answer:** The main risks are silent failures, unintended access, excessive resource consumption, repeated automation, and behavior that works only at small data volume. Address these explicitly in design and tests.

### Q7. How would you debug a failure?

**Answer:** Reproduce with the smallest representative data set, identify the transaction entry point, inspect logs and exceptions, track SOQL/DML/CPU usage, verify user permissions, and isolate which automation changed the record or caused re-entry.
