# Day 1: Cloud Computing, Data Centers, Salesforce, Multi-Tenancy and OOP Basics

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Physical data centers and cloud resources
- Traditional computing vs cloud computing
- Salesforce as SaaS/PaaS
- Multi-tenant architecture
- Object, class, abstraction, inheritance, polymorphism and encapsulation

## Concept in very simple words

Cloud computing means renting computing resources instead of buying and maintaining every server yourself. Salesforce is a cloud platform where many customers share infrastructure, but each customer org remains logically separated.

## Mental model

Think of the cloud like electricity from a utility. You use the service when needed without building the power station yourself.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// No Apex is required to understand the cloud model.
// This pseudocode shows the responsibility boundary:
BusinessData data = userProvidesData();
SalesforcePlatform platform = cloudProviderManagesInfrastructure();
platform.storeAndProcess(data); // Customer uses the service; provider runs the platform
```

## How to read the example

- **Comment:** No Apex is required to understand the cloud model.
- **Comment:** This pseudocode shows the responsibility boundary:

## More examples for Day 1

### Example 1: Start from the smallest valid case

```apex
// Arrange: create only the data needed for this lesson.
Account accountRecord = new Account(Name = 'Day 1 Example');

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
    accounts.add(new Account(Name = 'Day 1 Example ' + index));
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

### Q1. What is Cloud Computing, Data Centers, Salesforce, Multi-Tenancy and OOP Basics?

**Answer:** Cloud computing means renting computing resources instead of buying and maintaining every server yourself.

### Q2. Why is Cloud Computing, Data Centers, Salesforce, Multi-Tenancy and OOP Basics important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Cloud Computing, Data Centers, Salesforce, Multi-Tenancy and OOP Basics.

**Answer:** Use it when a business requirement directly needs the Day 1 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Cloud Computing, Data Centers, Salesforce, Multi-Tenancy and OOP Basics?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Cloud Computing, Data Centers, Salesforce, Multi-Tenancy and OOP Basics solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.
