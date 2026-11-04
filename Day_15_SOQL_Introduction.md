# Day 15: SOQL Introduction

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- SELECT and FROM
- WHERE and IN
- Logical filters
- Date filters
- Practice queries

## Concept in very simple words

SOQL retrieves Salesforce records. A good query selects only required fields and filters to the smallest useful record set.

## Mental model

Think of **SOQL Introduction** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// SELECT lists the fields that Apex needs.
// FROM identifies the Salesforce object.
// WHERE keeps only matching records.
List<Account> educationAccounts = [
    SELECT Id, Name, Industry, AnnualRevenue
    FROM Account
    WHERE Industry = 'Education'
    AND AnnualRevenue > 100000
];

// A list is returned even when the query finds zero records.
for (Account accountRecord : educationAccounts) {
    System.debug(accountRecord.Name);
}
```

## How to read the example

- **Comment:** SELECT lists the fields that Apex needs.
- **Comment:** FROM identifies the Salesforce object.
- **Comment:** WHERE keeps only matching records.
- **SOQL:** Requests only the Salesforce data needed by the logic.
- **Comment:** A list is returned even when the query finds zero records.
- **Loop:** Processes every value in a collection instead of assuming one record.

## More examples for Day 15

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 15 Example');

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
    accounts.add(new Account(Name = 'Day 15 Example ' + index));
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
- Selecting every field instead of only required fields.
- Assuming a query always returns a record.

## Interview questions

### Q1. What is SOQL Introduction?

**Answer:** SOQL retrieves Salesforce records.

### Q2. Why is SOQL Introduction important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for SOQL Introduction.

**Answer:** Use it when a business requirement directly needs the Day 15 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with SOQL Introduction?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a SOQL Introduction solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What makes a SOQL query efficient?

**Answer:** Select only required fields, filter as selectively as practical, bind values instead of constructing unsafe strings, and avoid running queries inside record loops.

### Q7. What should code do when a query returns no records?

**Answer:** For a List query, handle an empty List normally. Do not assume index zero exists. When assigning a single sObject from SOQL, understand that zero or multiple rows can throw a QueryException.

### Q8. How do bind variables improve SOQL?

**Answer:** They separate Apex values from query syntax, improve readability, and reduce injection risk compared with concatenating untrusted input into dynamic SOQL.
