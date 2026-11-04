# Day 2: Apex Data Types and Variables

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Primitive and non-primitive Apex types
- Variable declaration and assignment
- String, Integer, Decimal, Boolean, Date, Datetime and Id
- sObjects and collections

## Concept in very simple words

A data type tells Apex what kind of value a variable can store. Choosing the correct type prevents invalid operations and makes the code easier to understand.

## Mental model

Think of **Apex Data Types and Variables** as one part of a larger Salesforce application. First understand what responsibility it owns, then connect it to data, transactions and users.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// A variable is a named box that stores one value of a declared type.
String customerName = 'Acme';        // Text value
Integer employeeCount = 250;         // Whole number
Decimal annualRevenue = 1250000.50;  // Money-like value with decimal precision
Boolean active = true;               // Only true or false
Date reviewDate = Date.today();       // Today's date

// An sObject variable can hold one Salesforce record in memory.
Account accountRecord = new Account();
accountRecord.Name = customerName;    // Put the text into the Account Name field
accountRecord.AnnualRevenue = annualRevenue;

System.debug(accountRecord);          // Print the in-memory record to the debug log
```

## How to read the example

- **Comment:** A variable is a named box that stores one value of a declared type.
- **Comment:** An sObject variable can hold one Salesforce record in memory.
- **Object creation:** Allocates a new class instance, collection or sObject in memory.

## Complete Apex data-type reference

### Primitive types

| Type | Stores | Example | Beginner note |
|---|---|---|---|
| `Boolean` | `true`, `false`, or `null` | `Boolean approved = true;` | Use for yes/no state. |
| `Integer` | 32-bit whole number | `Integer quantity = 10;` | No decimal part. |
| `Long` | Larger 64-bit whole number | `Long population = 8000000000L;` | Add `L` when needed. |
| `Decimal` | Base-10 number | `Decimal price = 99.95;` | Prefer for money and percentages. |
| `Double` | Floating-point number | `Double distance = 12.345;` | Useful for approximate scientific calculations. |
| `String` | Text | `String name = 'Acme';` | Strings use single quotes in Apex. |
| `Date` | Calendar date | `Date todayValue = Date.today();` | No time or time zone. |
| `Time` | Time of day | `Time opening = Time.newInstance(9, 0, 0, 0);` | Hour, minute, second, millisecond. |
| `Datetime` | Date and time | `Datetime nowValue = System.now();` | Stored in UTC and displayed using user context. |
| `Id` | Salesforce record identifier | `Id accountId = accountRecord.Id;` | Can reveal its sObject type. |
| `Blob` | Binary data | `Blob body = Blob.valueOf('Hello');` | Used for file content and cryptography. |
| `Object` | Generic runtime value | `Object value = 'Acme';` | Cast before type-specific use. |

### Salesforce-specific and composite types

| Type | Purpose | Example |
|---|---|---|
| `sObject` | Generic Salesforce record | `sObject recordValue = new Account(Name='Acme');` |
| Concrete sObject | Strongly typed record | `Account accountRecord = new Account();` |
| Enum | Fixed set of named constants | `Trigger.operationType` |
| List | Ordered collection with duplicates | `List<String> names = new List<String>();` |
| Set | Unique collection | `Set<Id> ids = new Set<Id>();` |
| Map | Key-value lookup | `Map<Id, Account> byId = new Map<Id, Account>();` |
| Apex class | Custom object in memory | `InvoiceService service = new InvoiceService();` |

### Conversions and casting

```apex
String quantityText = '25';
Integer quantity = Integer.valueOf(quantityText); // Convert text to Integer
String backToText = String.valueOf(quantity);     // Convert Integer to text

SObject genericRecord = new Account(Name = 'Acme');
Account typedAccount = (Account) genericRecord;   // Cast after knowing the real type

Id recordId = typedAccount.Id;
if (recordId != null && recordId.getSObjectType() == Account.SObjectType) {
    System.debug('This Id belongs to an Account');
}
```

### Null, default values, and precision

```apex
Integer countValue;                 // Unassigned variables are null, not zero
countValue = countValue ?? 0;       // Provide zero only when null

Decimal unitPrice = 10.25;
Integer units = 3;
Decimal total = unitPrice * units;  // Decimal is suitable for money

Date dueDate = Date.today().addDays(30);
Datetime auditTime = System.now();
```

**Why this style is used:** explicit types help Apex catch mistakes during compilation. Null handling prevents runtime failures. `Decimal` avoids many binary floating-point surprises in business calculations.

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

### Q1. What is Apex Data Types and Variables?

**Answer:** A data type tells Apex what kind of value a variable can store.

### Q2. Why is Apex Data Types and Variables important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Apex Data Types and Variables.

**Answer:** Use it when a business requirement directly needs the Day 2 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Apex Data Types and Variables?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Apex Data Types and Variables solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What is the difference between a primitive value and a collection or object?

**Answer:** A primitive represents one basic value such as an Integer or String. An object can contain multiple fields and behavior, while a collection groups multiple values or objects for processing.

### Q7. How do null values affect this topic?

**Answer:** Apex variables can be null. Check required values before calling methods, using indexes, performing arithmetic, or dereferencing an object. Use a fallback only when a default is valid for the business rule.
