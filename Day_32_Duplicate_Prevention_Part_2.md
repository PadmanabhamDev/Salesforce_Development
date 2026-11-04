# Day 32: Duplicate Prevention - Part 2

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Excluding the current records
- Comparing old and new values
- Undelete handling
- Reusable duplicate-check method

## Concept in very simple words

Update and undelete add complexity because the current record must not match itself and only changed values should be evaluated.

## Mental model

Think of **Duplicate Prevention** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
for (Contact contactRecord : Trigger.new) {
    Contact oldRecord = Trigger.oldMap.get(contactRecord.Id);

    // Do not query or compare when the email did not change.
    if (contactRecord.Email == oldRecord.Email) continue;

    String normalizedEmail = contactRecord.Email?.trim().toLowerCase();
    if (normalizedEmail == null) {
        contactRecord.Email.addError('Email is required.');
    } else {
        changedEmails.add(normalizedEmail);
    }
}

// Query excludes incoming record IDs so a record does not match itself.
List<Contact> matches = [
    SELECT Id, Email
    FROM Contact
    WHERE Email IN :changedEmails
    AND Id NOT IN :Trigger.newMap.keySet()
];
```

## How to read the example

- **Loop:** Processes every value in a collection instead of assuming one record.
- **Comment:** Do not query or compare when the email did not change.
- **Condition:** Runs the enclosed code only when the business condition is true.
- **Comment:** Query excludes incoming record IDs so a record does not match itself.
- **SOQL:** Requests only the Salesforce data needed by the logic.

## More examples for Day 32

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 32 Example');

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
    accounts.add(new Account(Name = 'Day 32 Example ' + index));
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

### Q1. What is Duplicate Prevention - Part 2?

**Answer:** Update and undelete add complexity because the current record must not match itself and only changed values should be evaluated.

### Q2. Why is Duplicate Prevention - Part 2 important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Duplicate Prevention - Part 2.

**Answer:** Use it when a business requirement directly needs the Day 32 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Duplicate Prevention - Part 2?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Duplicate Prevention - Part 2 solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. Why must a trigger be bulkified?

**Answer:** Salesforce can invoke the trigger for many records in one transaction. Bulkification keeps query and DML usage nearly constant as record count grows.

### Q7. When should logic run in before vs after context?

**Answer:** Use before context to validate or modify the records being saved without extra DML. Use after context when record IDs or changes to related records are required.

### Q8. How do you prevent recursion safely?

**Answer:** Prefer idempotent logic. When a guard is necessary, track processed record IDs and operations rather than relying on one static Boolean for the whole transaction.
