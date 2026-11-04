# Day 11: Map Collection - Fundamentals

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Map key-value model
- Map declaration
- put, get and containsKey
- Unique keys and duplicate values

## Concept in very simple words

A Map stores key-value pairs. It is the most important collection for connecting records efficiently without nested loops.

## Mental model

Think of collections as containers: a List is a numbered queue, a Set is a guest list with no duplicates, and a Map is a dictionary that finds a value from its key.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// Key = Account Id, Value = complete Account record.
Map<Id, Account> accountsById = new Map<Id, Account>([
    SELECT Id, Name, Industry
    FROM Account
    WHERE Id IN :accountIds
]);

for (Contact contactRecord : contacts) {
    // A Map lookup avoids scanning every Account for every Contact.
    Account parentAccount = accountsById.get(contactRecord.AccountId);
    if (parentAccount != null) {
        contactRecord.Description = parentAccount.Industry;
    }
}
```

## How to read the example

- **Comment:** Key = Account Id, Value = complete Account record.
- **Object creation:** Allocates a new class instance, collection or sObject in memory.
- **SOQL:** Requests only the Salesforce data needed by the logic.
- **Loop:** Processes every value in a collection instead of assuming one record.
- **Comment:** A Map lookup avoids scanning every Account for every Contact.
- **Condition:** Runs the enclosed code only when the business condition is true.

## Map methods: complete practical catalog

| Method | Purpose | Example |
|---|---|---|
| `put(key, value)` | Add or replace one entry | `byId.put(a.Id, a)` |
| `putAll(map/sObjectList)` | Add several entries | `byId.putAll(accounts)` |
| `get(key)` | Return value or null | `byId.get(accountId)` |
| `containsKey(key)` | Check key existence | `byId.containsKey(accountId)` |
| `keySet()` | Return Set of keys | `byId.keySet()` |
| `values()` | Return List of values | `byId.values()` |
| `remove(key)` | Remove and return value | `byId.remove(accountId)` |
| `clear()` | Remove all entries | `byId.clear()` |
| `clone()` | Shallow-copy the Map | `Map<Id,Account> copy=byId.clone()` |
| `deepClone(...)` | Deep-copy sObject values | `byId.deepClone(false,true,false)` |
| `isEmpty()` | Check whether empty | `byId.isEmpty()` |
| `size()` | Count entries | `byId.size()` |
| `equals(other)` | Compare entries | `first.equals(second)` |
| `hashCode()` | Hash representation | Usually framework use |
| `toString()` | Text representation | `String.valueOf(byId)` |

```apex
Map<String, Decimal> priceByProduct = new Map<String, Decimal>();
priceByProduct.put('Laptop', 75000);       // Add key/value
priceByProduct.put('Mouse', 1200);
priceByProduct.put('Mouse', 1000);         // Same key replaces old value

if (priceByProduct.containsKey('Laptop')) {
    Decimal laptopPrice = priceByProduct.get('Laptop');
}

for (String productName : priceByProduct.keySet()) {
    Decimal price = priceByProduct.get(productName);
    System.debug(productName + ': ' + price);
}

List<Decimal> allPrices = priceByProduct.values();
Decimal removedPrice = priceByProduct.remove('Mouse');
```

### Constructing a Map directly from SOQL

```apex
Map<Id, Account> accountsById = new Map<Id, Account>([
    SELECT Id, Name, Industry
    FROM Account
    WHERE Id IN :accountIds
]);
// Salesforce uses each returned Account Id as the Map key.
```

### Missing key vs key mapped to null

```apex
Map<String, String> values = new Map<String, String>();
values.put('presentButNull', null);

System.debug(values.get('missing'));                 // null
System.debug(values.get('presentButNull'));          // null
System.debug(values.containsKey('missing'));          // false
System.debug(values.containsKey('presentButNull'));   // true
```

Use `containsKey` when the difference matters.

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

### Q1. What is Map Collection - Fundamentals?

**Answer:** A Map stores key-value pairs.

### Q2. Why is Map Collection - Fundamentals important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Map Collection - Fundamentals.

**Answer:** Use it when a business requirement directly needs the Day 11 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Map Collection - Fundamentals?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Map Collection - Fundamentals solution production-ready?

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
