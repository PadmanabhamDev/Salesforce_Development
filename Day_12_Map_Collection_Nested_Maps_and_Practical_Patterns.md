# Day 12: Map Collection - Nested Maps and Practical Patterns

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- keySet and values
- Map of List pattern
- Building one-to-many in memory
- Account-to-Cases and country-to-states examples

## Concept in very simple words

A Map value can itself be a List, Set, Map or sObject. Nested collection patterns model one-to-many information in memory.

## Mental model

Think of collections as containers: a List is a numbered queue, a Set is a guest list with no duplicates, and a Map is a dictionary that finds a value from its key.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
Map<String, List<String>> statesByCountry = new Map<String, List<String>>();

String country = 'India';
String stateName = 'Bihar';

// The key might not exist yet, so create its List first.
if (!statesByCountry.containsKey(country)) {
    statesByCountry.put(country, new List<String>());
}

// get returns the same List stored inside the Map.
statesByCountry.get(country).add(stateName);
statesByCountry.get(country).add('Delhi');

System.debug(statesByCountry);
```

## How to read the example

- **Object creation:** Allocates a new class instance, collection or sObject in memory.
- **Comment:** The key might not exist yet, so create its List first.
- **Condition:** Runs the enclosed code only when the business condition is true.
- **Comment:** get returns the same List stored inside the Map.

## Advanced Map patterns with multiple examples

### Group children by parent

```apex
Map<Id, List<Contact>> contactsByAccountId = new Map<Id, List<Contact>>();

for (Contact contactRecord : contacts) {
    if (contactRecord.AccountId == null) continue;

    if (!contactsByAccountId.containsKey(contactRecord.AccountId)) {
        // Create the child List only for the first Contact of this Account.
        contactsByAccountId.put(contactRecord.AccountId, new List<Contact>());
    }
    contactsByAccountId.get(contactRecord.AccountId).add(contactRecord);
}
```

### Count values without an aggregate query

```apex
Map<String, Integer> countByIndustry = new Map<String, Integer>();
for (Account accountRecord : accounts) {
    String industry = accountRecord.Industry ?? 'Not Specified';
    Integer oldCount = countByIndustry.get(industry) ?? 0;
    countByIndustry.put(industry, oldCount + 1);
}
```

### Compound business key

```apex
private static String duplicateKey(String email, String company) {
    String cleanEmail = String.valueOf(email).trim().toLowerCase();
    String cleanCompany = String.valueOf(company).trim().toLowerCase();
    return cleanEmail + '|' + cleanCompany; // Separator prevents ambiguous joins
}

Map<String, Lead> leadByBusinessKey = new Map<String, Lead>();
for (Lead leadRecord : leads) {
    leadByBusinessKey.put(
        duplicateKey(leadRecord.Email, leadRecord.Company),
        leadRecord
    );
}
```

### Map-of-Map

```apex
Map<String, Map<String, Decimal>> priceByRegionAndProduct =
    new Map<String, Map<String, Decimal>>();

String region = 'North';
if (!priceByRegionAndProduct.containsKey(region)) {
    priceByRegionAndProduct.put(region, new Map<String, Decimal>());
}
priceByRegionAndProduct.get(region).put('Laptop', 75000);
```

Use nested Maps only when two independent keys are genuinely necessary. Otherwise create a wrapper class with named fields for readability.

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

### Q1. What is Map Collection - Nested Maps and Practical Patterns?

**Answer:** A Map value can itself be a List, Set, Map or sObject.

### Q2. Why is Map Collection - Nested Maps and Practical Patterns important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Map Collection - Nested Maps and Practical Patterns.

**Answer:** Use it when a business requirement directly needs the Day 12 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Map Collection - Nested Maps and Practical Patterns?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Map Collection - Nested Maps and Practical Patterns solution production-ready?

**Answer:** Use clear responsibilities, collection-safe processing, deliberate error handling, meaningful tests, minimal database operations, documented security enforcement, and observable logging for failures.

### Q6. What is the difference between a primitive value and a collection or object?

**Answer:** A primitive represents one basic value such as an Integer or String. An object can contain multiple fields and behavior, while a collection groups multiple values or objects for processing.

### Q7. How do null values affect this topic?

**Answer:** Apex variables can be null. Check required values before calling methods, using indexes, performing arithmetic, or dereferencing an object. Use a fallback only when a default is valid for the business rule.

### Q8. When would you choose List vs Set vs Map?

**Answer:** Choose List for ordered values and duplicates, Set for uniqueness and ID collection, and Map when you need fast lookup from a key to a value.

### Q9. How do collections help with governor limits?

**Answer:** Collections let code gather many IDs, issue one SOQL query, correlate results with Sets or Maps, and perform one bulk DML operation instead of repeating database work record by record.

### Q10. What is a common collection pattern in a trigger?

**Answer:** Collect unique parent IDs in a Set, query all parents once, construct a Map keyed by parent Id, then loop over Trigger.new and perform constant-time Map lookups.
