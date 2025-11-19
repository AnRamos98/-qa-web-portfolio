**Table of Contents:**

- [Introduction](#introduction)
- [Endpoint GET / product](#endpoint-get--product)
  - [Analysis: inconsistencies and errors](#analysis-inconsistencies-and-errors)
  - [Questions for PO/Dev](#questions-for-podev)
  - [Test Cases](#test-cases)
    - [✅ Positive Test Cases](#-positive-test-cases)
    - [❌ Negative Test Cases](#-negative-test-cases)
    - [🧪 Edge Test Cases](#-edge-test-cases)
- [Endpoint POST / product](#endpoint-post--product)
  - [Analysis: inconsistencies and errors](#analysis-inconsistencies-and-errors-1)
  - [Questions for PO/Dev](#questions-for-podev-1)
  - [Test Cases](#test-cases-1)
    - [✔️ Positive Test Cases](#️-positive-test-cases)
    - [❌ Negative Test Cases](#-negative-test-cases-1)
    - [🧪 Edge Cases](#-edge-cases)

## Introduction

This case study will give you a real, hands-on exercise.

Below you can find a JSON response from an API endpoint.

This JSON contains several (actually, many 😅) issues that a QA should identify.

Your tasks:

- Identify all problems and inconsistencies in the request
  
- Review the response (created below) and identify issues

- Ask clarification questions to PO/Dev

- Write positive, negative and of couse edge test cases

Ready to go? Yeahhh 🎉

The structure to consider:

**Category → Product → Variant → Stock → Price**


## Endpoint GET / product

JSON RESPONSE BODY example:

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

### Analysis: inconsistencies and errors

**IDs and data types:**
  - `id` is a string ("P1001"), but variant IDs are mixed types:
    - 2001 -> number
    - "2002" -> string
    - 2003 -> number
      - **ID types should be consistent across all entities**
  - `category_id` is a number, but in many systems IDs follow one consistent type (often strings).
      - Not necessarily wrong, but it should be clearly defined and consistent across the API.

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


### Questions for PO/Dev

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


### Test Cases

#### ✅ Positive Test Cases

| TC ID      | Description                                      | Steps                                                                 | Input                    | Expected Result                                                                                              |
|------------|--------------------------------------------------|-----------------------------------------------------------------------|--------------------------|--------------------------------------------------------------------------------------------------------------|
| PT_01      | Get existing product with multiple variants      | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. Product object returned with non-empty variants array.                                               |
| PT_02      | Product has variants with stock and price        | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. At least one variant contains stock and price objects.                                               |
| PT_03      | Product contains tags                            | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. Tags array is present and iterable.                                                                  |
| PT_04      | Product includes metadata                        | 1. Send GET /product/P1001                                           | id = "P1001"            | 200 OK. Metadata object present with rating, sort_order, extra.                                              |
| PT_05      | Response matches expected schema                 | 1. Send GET /product/P1001 2. Validate against JSON Schema           | id = "P1001"            | 200 OK. All required fields present and types are correct.                                                   |


#### ❌ Negative Test Cases

| TC ID      | Description                                      | Steps                                          | Input           | Expected Result                                                                            |
|------------|--------------------------------------------------|------------------------------------------------|-----------------|--------------------------------------------------------------------------------------------|
| NT_01      | Product ID does not exist                        | 1. GET /product/P9999                          | id = "P9999"    | 404 Not Found. Error message: "product not found".                                         |
| NT_02      | Invalid ID format                                | 1. GET /product/@@@                            | id = "@@@"      | 400 Bad Request. Error indicates invalid ID format.                                        |
| NT_03      | Missing authentication                           | 1. GET /product/P1001 without auth header     | id = "P1001"    | 401 Unauthorized (or 403 Forbidden depending on API design).                               |
| NT_04      | Unsupported HTTP method                          | 1. POST /product/P1001                         | id = "P1001"    | 405 Method Not Allowed. API returns supported methods in Allow header (optional).          |
| NT_05      | Invalid Accept or Content-Type header            | 1. GET /product/P1001 with Accept: application/xml | id = "P1001" | 406 Not Acceptable or default JSON error response.                                         |


#### 🧪 Edge Test Cases

| TC ID      | Description                                       | Steps                                                             | Input                             | Expected Result                                                                                          |
|------------|---------------------------------------------------|-------------------------------------------------------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------|
| ET_01      | Product with zero variants                        | 1. GET /product/{id} for product with variants: []               | id = product with no variants     | 200 OK. Variants is an empty array.                                                                       |
| ET_02      | Product with inconsistent data types              | 1. GET /product/P1001                                            | id = "P1001"                      | 200 OK. Client handles mixed data types without crashing.                                                |
| ET_03      | Product with large number of variants             | 1. GET /product/{id} for product with 100+ variants              | id = product with many variants   | 200 OK. API handles pagination or large payload performance correctly.                                   |
| ET_04      | Product missing optional fields                   | 1. GET /product/{id} with no tags or metadata                    | id = product missing optional data | 200 OK. Missing optional fields handled gracefully.                                                      |
| ET_05      | Inconsistent date formats                         | 1. GET /product/P1001                                            | id = "P1001"                      | 200 OK. Client handles date parsing or identifies need to standardize API date formats.                 |



## Endpoint POST / product

This POST example is intentionally incorrect.

The JSON payload we are sending contains multiple validation errors, and in a real, well implemented API, we wouldn't expect to receive a 201 (Created) response.

A properly designed API should return a 4xx error, such as 400 (Bad Request) or 422 (Unprocessable Entity), indicating that the input is invalid.

However, for the purpose of this exercise, we are simulating a scenario where the API is poorly implemented and still returns a successful 201 response, even though the request payload is invalid.

This allow us to:

- analyze both the incorrect POST request and the incorrect API response,
- identify validation gaps,
- highlight inconsistencies,
- and evaluate the behaviour of an API that does not enforce proper data validation.

**In short:
We know the payload is wrong. We know the response status is wrong.
This is deliberate, we are testing how to analyze and reason about a faulty API.**

Let's go!

JSON REQUEST BODY example:

Endpoint: POST /product/{id}
```
{
  "id": "P-200",
  "name": "Nike Pegasus",
  "category_id": 10,
  "description": 1234,
  "status": "Active",
  "variants": [
    {
      "id": 3001,
      "color": "Blue",
      "size": "42",
      "stock": {
        "quantity": "100",
        "warehouse_id": "WH1"
      },
      "price": {
        "currency": "EUR",
        "amount": "130.00",
        "discount": "15%"
      }
    },
    {
      "id": 3002,
      "color": "Green",
      "size": 43,
      "stock": {
        "quantity": -2,
        "warehouse_id": 2
      },
      "price": {
        "currency": "usd",
        "amount": null
      }
    }
  ],
  "tags": ["men", null, 12],
  "metadata": {
    "rating": "5",
    "sort_order": "first",
    "extra": {}
  }
}
```

JSON RESPONSE example:

```
{
  "product_id": 200,
  "status": "created",
  "message": "Product created sucessfully",
  "created_at": "20-03-2025T10:00",
  "warnings": [
    "discount format invalid",
    null,
    150
  ],
  "debug": true,
  "metadata": {
    "version": "v1"
  }
}
```

### Analysis: inconsistencies and errors

**product_id**
 - The request sen ```"P-200"```
 - The response returned ```200``` (number)
    - Inconsistent ID between request and response

**status**
  - ```"created"``` is in lowercase
    - If this is an enum, it should be uppercase: ```"CREATED"```

**message**
  - ```"sucessfully"``` contains a typo
    - It should be ```"successfully"```

**created_at**
  - ```"20-03-2025T10:00"```
    - Invalid format (DD-MM-YYYY + missing second + missing "Z" + mixed styles)
  - Correct format should be: ```"2025-03-20T10:00:00Z"```

**warnings**
  - Contains:
    - a valid string
    - ```null```
    - a number
      - **Warnings list should be 100% strings**

**debug**
  - ```"debug": true```
    - Debug flags should not be included in production environments

**metadata.version**
  - ```"version": "v1"```
    - Ok, but it is not part of the product creation
    - The response should remain consistent


### Questions for PO/Dev

1. Does the API return the same ID that was sent, or does it generate a new one?
2. Is "created" the expected status value? Does casing matter (uppercase vs lowercase)?
3. What is the official date format the API should follow in responses?
4. Should warnings always be an array of strings?
5. Should debug information ever be returned in non-development environments?
6. Does the API always include metadata fields such as version in its responses?
7. Is the success message standardized across the API?


### Test Cases

#### ✔️ Positive Test Cases

| TC ID | Description                               | Input (JSON Extract)                                                                                                                        | Expected Result                               |
|-------|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------|
| PT_01 | Create product with valid data             | `{ "name": "Nike Pegasus", "category_id": 10, "description": "Running shoe", "variants": [{ "id": 3001, "color": "Blue", "size": 42, "stock": { "quantity": 100, "warehouse_id": 1 }, "price": { "currency": "EUR", "amount": 130, "discount": 10 }}] }` | 201 Created + product_id                      |
| PT_02 | Create product with a single valid variant | `{ "name": "Nike Zoom", "category_id": 5, "description": "Lightweight shoe", "variants": [{ "id": 4001, "color": "Black", "size": 41, "stock": { "quantity": 50, "warehouse_id": 2 }, "price": { "currency": "EUR", "amount": 110 }}] }` | 201 Created                                   |
| PT_03 | Create product without metadata            | `{ "name": "Adidas Solar", "category_id": 8, "description": "Training shoe", "variants": [{ "id": 4267, "color": "Green", "size": 43, "stock": { "quantity": 20, "warehouse_id": 3 }, "price": { "currency": "EUR", "amount": 95 }}] }` | 201 Created                                   |
| PT_04 | Response uses ISO8601 timestamp            | Any valid POST request                                                                                                                      | created_at must match `YYYY-MM-DDTHH:MM:SSZ`  |


#### ❌ Negative Test Cases

| TC ID | Description                      | Input (JSON Extract)                                                                                                                          | Expected Result                                 |
|-------|-----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------|
| NT_01 | Negative stock value              | `{ "variants": [{ "stock": { "quantity": -5, "warehouse_id": 1 }, "price": { "currency": "EUR", "amount": 80 }}] }`                            | 400 / 422 - quantity cannot be negative         |
| NT_02 | Discount in invalid format        | `{ "variants": [{ "price": { "currency": "EUR", "amount": 100, "discount": "15%" }}] }`                                                        | 400 - discount must be a number                 |
| NT_03 | Amount is null                    | `{ "variants": [{ "price": { "currency": "EUR", "amount": null }}] }`                                                                          | 400 - amount is required                        |
| NT_04 | Tags contain invalid types        | `{ "tags": ["men", null, 123] }`                                                                                                                | 400 - tags must be an array of strings          |
| NT_05 | Currency in lowercase             | `{ "variants": [{ "price": { "currency": "usd", "amount": 50 }}] }`                                                                             | 400 - currency must follow ISO uppercase format |
| NT_06 | Description is not a string       | `{ "description": 12345 }`                                                                                                                     | 400 - description must be a string              |


#### 🧪 Edge Cases

| TC ID | Description                    | Input (JSON Extract)                                                                    | Expected Result                                     |
|-------|--------------------------------|------------------------------------------------------------------------------------------|-----------------------------------------------------|
| ET_01 | Product without variants        | `{ "name": "Test", "category_id": 1, "variants": [] }`                                   | 400 OR allowed (business rule dependent)            |
| ET_02 | Product with 100 variants       | `{ "variants": [ {...} x100 ] }`                                                         | 201 Created / Performance OK                        |
| ET_03 | Duplicate variant IDs           | `{ "variants": [{ "id": 1 }, { "id": 1 }] }`                                            | 409 Conflict OR 400 Bad Request                     |
| ET_04 | Extremely high price            | `{ "variants": [{ "price": { "currency": "EUR", "amount": 999999 }}] }`                 | Validation error OR accepted (depends on limits)    |
| ET_05 | Category does not exist         | `{ "name": "Test", "category_id": 9999, "variants": [] }`                               | 404 Not Found OR 400 Bad Request                    |



 