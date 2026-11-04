# Day 43: Decisions, Loops and Quick-Action Flows

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Decision outcomes
- Loop and assignment
- recordId input variable
- Adding Flow as a quick action

## Concept in very simple words

Decisions branch, loops iterate collections, and quick actions provide record context through an input variable such as recordId.

## Mental model

Think of **Decisions, Loops and Quick-Action Flows** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Build it step by step

1. Create a `recordId` Text variable marked Available for input.
2. Get the current record and related records.
3. Use Decision outcomes for mutually exclusive paths.
4. Use Loop + Assignment to prepare a record collection.
5. Expose the Flow as a Lightning quick action and test from the record page.

## Why the Flow is designed this way

- Entry criteria reduce unnecessary interviews and CPU usage.
- Collection-first processing prevents one database operation per record.
- Fault paths make production failures visible and supportable.
- Clear inputs and outputs make the Flow reusable.

## Additional worked scenarios

### Scenario 1: Small, user-driven process

1. Start with the exact Day 43 Flow type or element.
2. Define one clear input, such as `recordId`.
3. Add entry conditions before database actions.
4. Connect the success path and every fault path.
5. Debug with valid input, blank input and a record the user cannot access.

### Scenario 2: Bulk or reusable process

1. Retrieve a collection with one Get Records element.
2. Use Assignment to prepare a second collection.
3. Perform one Create/Update/Delete Records operation outside the loop.
4. Move shared logic into a subflow only when it has a stable input/output contract.
5. Log `$Flow.FaultMessage`, the Flow API name and the record reference.

### Configuration reasoning

- **Why entry conditions?** They stop unnecessary Flow interviews.
- **Why collection DML?** It reduces database operations and limit risk.
- **Why fault connectors?** Silent failures are difficult to support.
- **Why explicit variables?** Future maintainers can understand what data enters and leaves the Flow.

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

### Q1. What is Decisions, Loops and Quick-Action Flows?

**Answer:** Decisions branch, loops iterate collections, and quick actions provide record context through an input variable such as recordId.

### Q2. Why is Decisions, Loops and Quick-Action Flows important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Decisions, Loops and Quick-Action Flows.

**Answer:** Use it when a business requirement directly needs the Day 43 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Decisions, Loops and Quick-Action Flows?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Decisions, Loops and Quick-Action Flows solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. How do you choose between Flow and Apex?

**Answer:** Use Flow for maintainable declarative orchestration when its elements fit the requirement. Use Apex for complex algorithms, advanced integration, transaction control, or reusable behavior that is clearer in code.

### Q7. How should errors be handled in Flow?

**Answer:** Connect fault paths from database and action elements, capture the fault message and useful context, log support details, and show users a safe message rather than raw internals.

### Q8. Why should database operations remain outside Flow loops?

**Answer:** A Flow interview shares platform limits with its transaction. Collection-based Get/Create/Update/Delete operations reduce the number of database operations and scale more safely.
