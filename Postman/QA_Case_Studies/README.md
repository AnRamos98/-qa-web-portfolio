**Table of Contents:**

- [Introduction](#introduction)
- [Analysis: inconsistencies and errors](#analysis-inconsistencies-and-errors)
- [Questions for PO/Dev](#questions-for-podev)
- [Test Cases](#test-cases)
  - [✅ Positive Test Cases](#-positive-test-cases)
  - [❌ Negative Test Cases](#-negative-test-cases)
  - [🧪 Edge Test Cases](#-edge-test-cases)

## Introduction

This case studie will give you a real, hands-on exercise.

Below you can find a JSON response from an API endpoint.

This JSON contains several (actually, many 😅) issues, and our goal is to identify **all** the problems and inconsistencies. 

After that, we will document the questions we would ask to the PO/Dev to clarify ambiguous points, and finally, we will create test cases (positive, negative and of course edge cases!).

Ready to go? Yeahhh 🎉

The structure to consider:

**Category → Product → Variant → Stock → Price**

JSON example:

Endpoint: GET /product/{id}
```
{
  "id": "P1001",
  "name": "Nike Air Max",
  "category_id": 10,
  "description": "Running shoes for everyday use",
  "status": "Active",
  "variants": [
    {
      "id": 2001,
      "color": "Red",
      "size": "42",
      "stock": {
        "quantity": "50",
        "warehouse_id": 3
      },
      "price": {
        "currency": "EUR",
        "amount": "120.00",
        "discount": 10
      }
    },
    {
      "id": "2002",
      "color": "Blue",
      "size": 41,
      "stock": {
        "quantity": -5,
        "warehouse_id": "3"
      },
      "price": {
        "currency": "eur",
        "amount": 115,
        "discount": "5%"
      }
    },
    {
      "id": 2003,
      "color": null,
      "size": "44",
      "price": {
        "currency": "USD",
        "amount": null
      }
    }
  ],
  "tags": ["running", "men", 123, null],
  "created_at": "15/03/2025 10:00",
  "last_update": "2025-03-15T10:30:00Z",
  "metadata": {
    "rating": "4.7",
    "sort_order": 1,
    "extra": {}
  }
}
```

## Analysis: inconsistencies and errors

**IDs and data types:**
  - `id` is a string ("P1001"), but variant IDs are mixed types:
    - 2001 -> number
    - "2002" -> string
    - 2003 -> number
      - **ID types should be consistent across all entities**
  - `category_id` is a number, but in many systems IDs follow one consistent type (often strings).
      - Not necessarily wromg, but it should be clearly defined and consistent across the API.

**Variants / attributes:**
  - `size` appears as a string ("42") and also as a number (41).
    - Inconsistent data type.
  - `color` is null in one variant.
    - If this field is optional, the meaning of null must be clear (unknown, not applicable, error?).
  - One variant does not contain a stock object at all.
    - Is this valid? Does missing stock mean unavailable, out of stock, or invalid data?

**Stock**
  - In the first variant:
    ```
    "quantity": "50"
    ```
      - quantity is a string, but semantically it should be a number.

  - In the second variant:
    ```
    "quantity": -5
    ```
      - Negative quantity usually does not make sense for stock. This suggests either a bug or something is missing??
  - `warehouse_id` is:
    - 3(number) in one variant
    - "3"(string) in another
      - This is an inconsistent type.

**Price**
  - `currency` mixes uppercase and lowercase: "EUR" vs "eur"
      - Currency codes should follow ISO 4217 and be uppercase.
  - `amount` is a string in one variant and a number in another, and null in a third
      - Numeric values should be consistently typed and non-null when the variant is purchasable.
  - `discount` is inconsistent:
    - 10 (numeric)
    - "5%" (string with a syombol)
    - missing in the last variant
      - Discounts should follow one unified representation.
  
**Status**
  - The product `status` is "Active" (capitalized).
    - If this is a enum, the API should define case and allowed values.
    - No `status`exists for variants - is that intended?

**Tags**
   
   ```
    "tags": ["running", "men", 123, null]
   ```
 - Mixed types: strings, number, null.
   - Tags should typically be a string array.

**Dates**
  - `created_at`: "15/03/2025 10:00" (DD/MM/YYYY format, no timezone)
  - `last_update`: "2025-03-15T10:30:00Z" (ISO 8601 with timezone)

    - Two different date formats inside the same resource.
    - Missing timezone in one field.

**Metadata**
  - `rating` is a string, but should likely be a number (e.g., 4.7).
  - `sort_order`is numeric here (good!), but should be consistent if used across the system.
  - `extra` is present but empty:
    - What is it indended for? Optional? Dynamic?


## Questions for PO/Dev

The following questions help clarify ambiguous requirements and show critical thinking.

**IDs & Structure**

1. Should all IDs (product, variant, category, warehouse) use the same data type?
2. Is there any reason to mix numeric and string IDs?


**Variant Requirements**

3. Which fields are mandatory in a variant (color, size, stock, price)?
4. Can a variant exist without stock information?
5. what does `color:null`represent?

**Stock logic**

6. Can stock quantity be negative?
7. Should `quantity` always be an integer?
8. Should `warehouse_id` be a number or string?

**Pricing Rules**

9. Is it valid to have different currencies in variants of the same product?
10. Should `currency` always be uppercase (ISO standard)?
11. Can `amount` be null? Does that mean "not for sale"?
12. Should `discount`always be a number? Is it always a percentage?
13. What happens when `discount`is missing?

**Status**

14. What are the allowed values for `status`?
15. Is `status` case-sensitive?
16. Should variants also have a status field?

**Tags & Metadata**

17. Should tags always be strings?
18. What is the expected type and range for `rating`?
19. What goes inside `extra`? Free-form data? 

**Dates**

20. What is the official date/time format for the API?
21. Should all dates include timezone information?


## Test Cases

### ✅ Positive Test Cases

| TC ID      | Description                                      | Steps                                                                 | Input                    | Expected Result                                                                                              |
|------------|--------------------------------------------------|-----------------------------------------------------------------------|--------------------------|--------------------------------------------------------------------------------------------------------------|
| PT_01      | Get existing product with multiple variants      | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. Product object returned with non-empty variants array.                                               |
| PT_02      | Product has variants with stock and price        | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. At least one variant contains stock and price objects.                                               |
| PT_03      | Product contains tags                            | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. Tags array is present and iterable.                                                                  |
| PT_04      | Product includes metadata                        | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. Metadata object present with rating, sort_order, extra.                                              |
| PT_05      | Response matches expected schema                 | 1. Send GET /product/P1001 2. Validate against JSON Schema           | id = "P1001"            | 200 OK. All required fields present and types are correct.                                                   |


### ❌ Negative Test Cases

| TC ID      | Description                                      | Steps                                          | Input           | Expected Result                                                                            |
|------------|--------------------------------------------------|------------------------------------------------|-----------------|--------------------------------------------------------------------------------------------|
| NT_01      | Product ID does not exist                        | 1. GET /product/P9999                          | id = "P9999"    | 404 Not Found. Error message: "product not found".                                         |
| NT_02      | Invalid ID format                                | 1. GET /product/@@@                            | id = "@@@"      | 400 Bad Request. Error indicates invalid ID format.                                        |
| NT_03      | Missing authentication                           | 1. GET /product/P1001 without auth header     | id = "P1001"    | 401 Unauthorized (or 403 Forbidden depending on API design).                               |
| NT_04      | Unsupported HTTP method                          | 1. POST /product/P1001                         | id = "P1001"    | 405 Method Not Allowed. API returns supported methods in Allow header (optional).          |
| NT_05      | Invalid Accept or Content-Type header            | 1. GET /product/P1001 with Accept: application/xml | id = "P1001" | 406 Not Acceptable or default JSON error response.                                         |


### 🧪 Edge Test Cases

| TC ID      | Description                                       | Steps                                                             | Input                             | Expected Result                                                                                          |
|------------|---------------------------------------------------|-------------------------------------------------------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------|
| ET_01      | Product with zero variants                        | 1. GET /product/{id} for product with variants: []               | id = product with no variants     | 200 OK. Variants is an empty array.                                                                       |
| ET_02      | Product with inconsistent data types              | 1. GET /product/P1001                                            | id = "P1001"                      | 200 OK. Client handles mixed data types without crashing.                                                |
| ET_03      | Product with large number of variants             | 1. GET /product/{id} for product with 100+ variants              | id = product with many variants   | 200 OK. API handles pagination or large payload performance correctly.                                   |
| ET_04      | Product missing optional fields                   | 1. GET /product/{id} with no tags or metadata                    | id = product missing optional data | 200 OK. Missing optional fields handled gracefully.                                                      |
| ET_05      | Inconsistent date formats                         | 1. GET /product/P1001                                            | id = "P1001"                      | 200 OK. Client handles date parsing or identifies need to standardize API date formats.                 |
