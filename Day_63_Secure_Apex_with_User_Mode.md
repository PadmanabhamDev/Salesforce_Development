# Day 63: Secure Apex with User Mode

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- User-mode DML
- WITH USER_MODE
- Database.queryWithBinds
- Security.stripInaccessible
- CRUD/FLS and sharing

## Concept in very simple words

Secure Apex must intentionally enforce object, field and record access. User-mode query and DML are clear tools for user-facing operations.

## Mental model

Think of security as a building with several checkpoints: entry to the building, entry to a room, access to a cabinet, and permission to read a specific document.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
public with sharing class CourseService {
    public static List<Course__c> findPublishedCourses() {
        // USER_MODE enforces object, field and record access for this query.
        return [
            SELECT Id, Name, Price__c, Status__c
            FROM Course__c
            WHERE Status__c = 'Published'
            WITH USER_MODE
        ];
    }

    public static Database.SaveResult createCourse(Course__c courseRecord) {
        // User-mode DML respects the current user's create and field permissions.
        return Database.insert(courseRecord, false, AccessLevel.USER_MODE);
    }
}
```

## How to read the example

- **Comment:** USER_MODE enforces object, field and record access for this query.
- **Return:** Sends a result back to the caller and ends the method.
- **SOQL:** Requests only the Salesforce data needed by the logic.
- **Comment:** User-mode DML respects the current user's create and field permissions.

## More examples for Day 63

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 63 Example');

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
    accounts.add(new Account(Name = 'Day 63 Example ' + index));
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

### Q1. What is Secure Apex with User Mode?

**Answer:** Secure Apex must intentionally enforce object, field and record access.

### Q2. Why is Secure Apex with User Mode important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Secure Apex with User Mode.

**Answer:** Use it when a business requirement directly needs the Day 63 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Secure Apex with User Mode?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Secure Apex with User Mode solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What production risk is most important for this topic?

**Answer:** The main risks are silent failures, unintended access, excessive resource consumption, repeated automation, and behavior that works only at small data volume. Address these explicitly in design and tests.

### Q7. How would you debug a failure?

**Answer:** Reproduce with the smallest representative data set, identify the transaction entry point, inspect logs and exceptions, track SOQL/DML/CPU usage, verify user permissions, and isolate which automation changed the record or caused re-entry.
