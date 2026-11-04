# Day 18: Relationship Queries and OFFSET

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- OFFSET pagination concept
- Child-to-parent relationships
- Parent-to-child subqueries
- Custom relationship names
- Semi-joins

## Concept in very simple words

Relationship queries retrieve related data. Child-to-parent uses dot notation; parent-to-child uses a subquery and the child relationship name.

## Mental model

Think of **Relationship Queries and OFFSET** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// Child-to-parent: move upward with dot notation.
List<Contact> contacts = [
    SELECT Id, LastName, Account.Name, Account.Industry
    FROM Contact
    WHERE AccountId != null
];

// Parent-to-child: use a subquery with the child relationship name.
List<Account> accounts = [
    SELECT Id, Name,
           (SELECT Id, LastName, Email FROM Contacts)
    FROM Account
];
```

## How to read the example

- **Comment:** Child-to-parent: move upward with dot notation.
- **SOQL:** Requests only the Salesforce data needed by the logic.
- **Comment:** Parent-to-child: use a subquery with the child relationship name.

## More examples for Day 18

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 18 Example');

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
    accounts.add(new Account(Name = 'Day 18 Example ' + index));
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

### Q1. What is Relationship Queries and OFFSET?

**Answer:** Relationship queries retrieve related data.

### Q2. Why is Relationship Queries and OFFSET important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Relationship Queries and OFFSET.

**Answer:** Use it when a business requirement directly needs the Day 18 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Relationship Queries and OFFSET?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Relationship Queries and OFFSET solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What makes a SOQL query efficient?

**Answer:** Select only required fields, filter as selectively as practical, bind values instead of constructing unsafe strings, and avoid running queries inside record loops.

### Q7. What should code do when a query returns no records?

**Answer:** For a List query, handle an empty List normally. Do not assume index zero exists. When assigning a single sObject from SOQL, understand that zero or multiple rows can throw a QueryException.

### Q8. How do bind variables improve SOQL?

**Answer:** They separate Apex values from query syntax, improve readability, and reduce injection risk compared with concatenating untrusted input into dynamic SOQL.
