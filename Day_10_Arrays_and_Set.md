# Day 10: Arrays and Set

> Beginner level: read this lesson even if you have never written code before.

## What you will learn

- Array-style syntax
- Set uniqueness
- Set methods
- Using Set for record IDs and unique business values

## Concept in very simple words

A Set stores only unique values and is ideal for collecting record IDs before a query. Apex array syntax is largely an alternative syntax for List.

## Mental model

Think of collections as containers: a List is a numbered queue, a Set is a guest list with no duplicates, and a Map is a dictionary that finds a value from its key.

## Why this matters in Salesforce

Salesforce runs business automation inside shared, limited transactions. This topic helps you write solutions that are understandable, bulk-safe, secure and easier to test.

## Commented example

```apex
// A Set automatically removes duplicates.
Set<String> emails = new Set<String>();
emails.add('user@example.com');
emails.add('user@example.com'); // The second copy is ignored

System.debug(emails.size()); // Prints 1
System.debug(emails.contains('user@example.com'));

// The most common Salesforce use is collecting unique parent IDs.
Set<Id> accountIds = new Set<Id>();
for (Contact contactRecord : contacts) {
    if (contactRecord.AccountId != null) {
        accountIds.add(contactRecord.AccountId);
    }
}
```

## How to read the example

- **Comment:** A Set automatically removes duplicates.
- **Object creation:** Allocates a new class instance, collection or sObject in memory.
- **Comment:** The most common Salesforce use is collecting unique parent IDs.
- **Loop:** Processes every value in a collection instead of assuming one record.
- **Condition:** Runs the enclosed code only when the business condition is true.

## Set methods: complete practical catalog

| Method | Purpose | Example |
|---|---|---|
| `add(value)` | Add if not already present; returns Boolean | `ids.add(accountId)` |
| `addAll(collection)` | Add many unique values | `ids.addAll(moreIds)` |
| `clear()` | Remove all values | `ids.clear()` |
| `clone()` | Copy the Set | `Set<Id> copy = ids.clone()` |
| `contains(value)` | Test membership | `ids.contains(accountId)` |
| `containsAll(collection)` | Confirm every supplied value exists | `ids.containsAll(requiredIds)` |
| `equals(other)` | Compare membership | `first.equals(second)` |
| `hashCode()` | Hash representation | Usually framework use |
| `isEmpty()` | Test whether no values exist | `ids.isEmpty()` |
| `remove(value)` | Remove one value | `ids.remove(accountId)` |
| `removeAll(collection)` | Set difference, modifies current Set | `ids.removeAll(blockedIds)` |
| `retainAll(collection)` | Set intersection, keeps common values | `ids.retainAll(allowedIds)` |
| `size()` | Number of unique items | `ids.size()` |
| `toString()` | Text representation | `String.valueOf(ids)` |

```apex
Set<String> requested = new Set<String>{'Apex','Flow','LWC'};
Set<String> completed = new Set<String>{'Apex','Admin'};

Set<String> outstanding = requested.clone(); // Keep original Set unchanged
outstanding.removeAll(completed);             // {'Flow','LWC'}

Set<String> common = requested.clone();
common.retainAll(completed);                   // {'Apex'}

Boolean allDone = completed.containsAll(requested); // false
```

### Set-to-List conversion

```apex
Set<Id> uniqueAccountIds = new Set<Id>{accountId1, accountId2};
List<Id> accountIdList = new List<Id>(uniqueAccountIds);
// Convert when an API requires a List or when index/order operations are needed.
```

**Important:** a Set does not promise business ordering. If order matters, convert to a List and sort it.

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

### Q1. What is Arrays and Set?

**Answer:** A Set stores only unique values and is ideal for collecting record IDs before a query.

### Q2. Why is Arrays and Set important in Salesforce?

**Answer:** It affects how code or automation behaves inside Salesforce transactions. A strong answer should connect the concept to maintainability, bulk processing, governor limits, security, and testability where those concerns apply.

### Q3. Give a practical use case for Arrays and Set.

**Answer:** Use it when a business requirement directly needs the Day 10 capability. Start with the smallest valid case, then verify how the design behaves with multiple records, missing values, failures, and user permissions.

### Q4. What mistakes should you avoid with Arrays and Set?

**Answer:** Avoid assuming a single record, hiding errors, performing unnecessary database work, ignoring null values, and choosing a complex implementation when a simpler platform feature solves the requirement.

### Q5. How would you make a Arrays and Set solution production-ready?

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
