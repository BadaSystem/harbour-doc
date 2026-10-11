# HARBOUR-DOC TECHNICAL-GUIDE
# DOCUMENTACIÓN TECNICA DE HARBOUR-DOC

---

# PART 1 — ENGLISH MANUAL

## Table of Contents

1. Introduction
2. Philosophy & Scope
3. The Documentation Block
4. Documenting Functions & Procedures
5. Documenting Classes
6. Documenting Methods
7. Documenting Constants (`#define`)
8. The Tag Reference
9. Type Codes
10. Complete Real-World Examples
11. Rules of the Parser (What works, what doesn't)
12. Best Practices & Style Guide
13. Common Mistakes
14. Generating Documentation
15. Quick Reference Card

---

## 1. Introduction

`harbour-doc` is a documentation generator for the Harbour language. It reads
your `.prg` source files, extracts structured comments, and produces output in
HTML, Markdown, JSON, or plain text.

This manual defines the **official documentation standard** for Harbour. If you
follow it exactly, `harbour-doc` will produce complete, correct, and searchable
API documentation for your project. The goal is to establish one consistent
convention that the entire Harbour community can adopt.

The standard is inspired by JSDoc, Doxygen, and PHPDoc, but it is adapted to
Harbour's syntax, types, and OOP model (`hbclass.ch`).

---

## 2. Philosophy & Scope

The standard follows three principles:

1. **Proximity** — Documentation lives directly above the symbol it describes.
2. **Structure** — Tags (`@param`, `@return`, `@see` …) carry machine-readable
   metadata; free text carries human-readable meaning.
3. **Simplicity** — No compiler directives, no external files, no annotations
   that Harbour cannot parse. A doc block is just a `/** … */` comment.

`harbour-doc` documents the following symbol types:

| Symbol     | Detected by keyword         |
|------------|-----------------------------|
| Function   | `FUNCTION`                  |
| Procedure  | `PROCEDURE`                 |
| Class      | `CLASS` (with `FROM`)       |
| Method     | `METHOD`                    |
| Constant   | `#define`                   |

---

## 3. The Documentation Block

A documentation block is a comment that begins with `/**` (two asterisks) and
ends with `*/`. The leading `/**`, trailing `*/`, and every leading `*` on the
continuation lines are automatically stripped by the parser.

### 3.1 Multi-line form (recommended)

```harbour
/**
 * Brief description on the first line.
 *
 * Extended description on the following lines.
 * Can span multiple paragraphs.
 *
 * @param cName (C) -> Description of the parameter
 * @return (L) -> Description of the return value
 * @since 1.0.0
 * @see RelatedSymbol
 * @example
 *   LOCAL lOk := MyFunction( "test" )
 */
FUNCTION MyFunction( cName )
   ...
```

### 3.2 Single-line form (compact)

```harbour
/** Returns the entity identifier. @return (C) -> ID string */
METHOD GetId() CLASS TBaseEntity
   RETURN ::cId
```

### 3.3 Critical rules

- The block **must** start with `/**`. A block starting with `/*` is treated as
  a regular comment and is ignored.
- A doc block is associated with the **next non-comment code line**. Anything
  between the block and the symbol (except blank lines) breaks the association.
- Free-text description is only accumulated **before the first tag**. Text after
  a tag belongs to that tag.
- Regular `/* … */` comments and `//` line comments do not interfere.

---

## 4. Documenting Functions & Procedures

### 4.1 The minimal function

```harbour
/**
 * Calculates the total price including tax.
 * @param nPrice (N) -> Base unit price
 * @return (N) -> Final total price
 */
FUNCTION CalculateTotal( nPrice )
   RETURN nPrice * 1.12
```

### 4.2 The complete function

```harbour
/**
 * Calculates the total price including tax and discount.
 *
 * This function applies a percentage tax to the base price and then
 * subtracts an optional flat discount. Negative totals are clamped to zero.
 *
 * @param nPrice    (N) -> Base unit price before tax
 * @param nQuantity (N) -> Number of units to calculate
 * @param nTaxRate  (N) -> Tax percentage to apply (0-100)
 * @param nDiscount (N) -> Flat discount amount to subtract
 * @return (N) -> Final total price after tax and discount
 * @since 1.0.0
 * @see FormatCurrency
 * @example
 *   nTotal := CalculateTotal( 100.00, 5, 12.0, 50.00 )
 *   // Result: 510.00
 */
FUNCTION CalculateTotal( nPrice, nQuantity, nTaxRate, nDiscount )
   LOCAL nSubtotal := nPrice * nQuantity
   LOCAL nTax      := nSubtotal * ( nTaxRate / 100 )
   LOCAL nTotal    := nSubtotal + nTax - nDiscount
   IF nTotal < 0
      nTotal := 0
   ENDIF
   RETURN nTotal
```

### 4.3 Procedures

Procedures have no return value. Document them with `PROCEDURE` and omit the
`@return` tag (or use `@return (NIL) -> Nothing`):

```harbour
/**
 * Logs a system event to the application event table.
 * @param cEventCode (C) -> Event type code (INFO, WARN, ERROR)
 * @param cModule    (C) -> Module name generating the event
 * @param cMessage   (C) -> Descriptive event message
 * @param cUser      (C) -> Username associated with the event
 * @since 1.0.0
 * @see CalculateTotal
 * @example
 *   LogEvent( "INFO", "ORDERS", "Order #1234 created", "admin" )
 */
PROCEDURE LogEvent( cEventCode, cModule, cMessage, cUser )
   ...
   RETURN
```

---

## 5. Documenting Classes

Class documentation sits **immediately above** the `CLASS` statement. The
parser detects `FROM` clauses automatically and links the parent class.

```harbour
/**
 * Base entity class providing common fields and behavior
 * for all business model objects.
 *
 * Includes audit tracking, soft delete support, and hash serialization.
 * All domain models should inherit from this class.
 *
 * @since 1.0.0
 * @see TCustomer
 * @see TOrder
 */
CLASS TBaseEntity
   DATA cId        INIT ""
   DATA dCreatedAt INIT NIL
   DATA lIsActive  INIT .T.

   METHOD New() CONSTRUCTOR
   METHOD GetId()
   METHOD SetId( cId )
   METHOD ToHash()
ENDCLASS
```

### 5.1 Inheritance

```harbour
/**
 * Customer model representing a business client.
 * Inherits from TBaseEntity and adds customer-specific
 * fields such as name, contact info, and credit balance.
 * @since 1.0.0
 * @see TBaseEntity
 * @see TOrder
 */
CLASS TCustomer FROM TBaseEntity
   DATA cName    INIT ""
   DATA cEmail   INIT ""
   DATA nBalance INIT 0

   METHOD New( cName, cEmail ) CONSTRUCTOR
   METHOD GetDisplayName()
   METHOD AddToBalance( nAmount, cReason )
ENDCLASS
```

The parser extracts `TBaseEntity` as the parent and renders a link in the HTML
output. Class documentation does **not** need `@param`/`@return`; those belong
to the constructor method.

---

## 6. Documenting Methods

A method is documented exactly like a function. The doc block must sit
immediately above the `METHOD … CLASS …` statement.

### 6.1 Constructor

```harbour
/**
 * Constructor for TCustomer. Calls the parent constructor and
 * initializes customer-specific fields.
 * @param cName  (C) -> Customer full name or company name
 * @param cEmail (C) -> Primary contact email address
 * @return (O) -> New TCustomer instance
 * @since 1.0.0
 * @example
 *   oCust := TCustomer():New( "Acme Corp", "billing@acme.com" )
 */
METHOD New( cName, cEmail ) CLASS TCustomer
   ::Super:New()
   ::cName  := cName
   ::cEmail := cEmail
   RETURN Self
```

### 6.2 Regular method

```harbour
/**
 * Adds or subtracts an amount from the customer credit balance.
 *
 * Positive values increase the balance, negative values decrease it.
 * Records the reason for audit.
 *
 * @param nAmount (N) -> Amount to add (positive) or subtract (negative)
 * @param cReason (C) -> Business reason for the adjustment
 * @return (N) -> New balance after adjustment
 * @since 2.0.0
 * @see Validate
 * @example
 *   nNewBal := oCust:AddToBalance( -150.00, "Invoice #4521 payment" )
 */
METHOD AddToBalance( nAmount, cReason ) CLASS TCustomer
   ::nBalance  += nAmount
   ::dUpdatedAt := Date()
   RETURN ::nBalance
```

### 6.3 Method overriding a parent

```harbour
/**
 * Validates customer data before persistence.
 * Extends base validation with email format and name checks.
 * @return (L) -> .T. if customer data is valid
 * @since 1.0.0
 * @see TBaseEntity
 */
METHOD Validate() CLASS TCustomer
   LOCAL lValid := ::Super:Validate()
   IF Empty( ::cName )
      lValid := .F.
   ENDIF
   RETURN lValid
```

---

## 7. Documenting Constants (`#define`)

Constants are documented with a doc block **immediately above** the `#define`
line. The parser extracts both the name and the value.

```harbour
/**
 * Maximum allowed length for customer name fields.
 * Values exceeding this limit are rejected during validation.
 * @since 1.0.0
 * @see ValidateCustomerName
 */
#define MAX_NAME_LENGTH      60

/**
 * Maximum number of line items allowed per order.
 * Orders exceeding this limit must be split.
 * @since 2.0.0
 * @see ValidateOrderItems
 */
#define MAX_ORDER_ITEMS      500

/**
 * Application version string displayed in all output.
 * @since 1.0.0
 */
#define APP_VERSION          "3.1.0"
```

The `@param` and `@return` tags do not apply to constants. Use `@since` and
`@see` only.

---

## 8. The Tag Reference

All tags begin with `@` and appear on their own line inside the doc block.

| Tag            | Applies to                     | Purpose                                 |
|----------------|--------------------------------|-----------------------------------------|
| `@param`       | Function, Method, Constructor  | Documents a parameter                    |
| `@return`      | Function, Method, Constructor  | Documents the return value               |
| `@returns`     | (alias of `@return`)           | Same as above                            |
| `@since`       | Any symbol                     | Version in which the symbol was added    |
| `@see`         | Any symbol                     | Cross-reference to another symbol        |
| `@example`     | Any symbol                     | Usage example (multi-line, accumulated)  |
| `@deprecated`  | Any symbol                     | Marks the symbol as deprecated           |
| `@internal`    | Any symbol                     | Marks the symbol as internal             |

### 8.1 `@param`

**Syntax:**

```
@param cName (Type) -> Description
```

- `cName` — the parameter name (required, first word).
- `(Type)` — one-letter type code in parentheses (optional).
- `->` — separator (optional; stripped automatically).
- `Description` — free text (optional).

**Examples:**

```harbour
@param cName (C) -> Customer full name
@param nAmount (N) -> Amount to add
@param lFlag (L) -> Enable flag
@param dStart (D) -> Range start date
@param aItems (A) -> Array of item hashes
@param hData (H) -> Data hash
@param oObject (O) -> Object instance
@param xValue (X) -> Any value
```

Bare form (no type, no arrow):

```harbour
@param cName Customer full name
```

### 8.2 `@return`

**Syntax:**

```
@return (Type) -> Description
```

**Examples:**

```harbour
@return (C) -> Formatted display string
@return (N) -> New balance after adjustment
@return (L) -> .T. if validation passes
@return (O) -> New TCustomer instance
@return (H) -> Hash with all entity fields
@return (NIL) -> Nothing (procedure)
```

### 8.3 `@since`

Records the version in which the symbol was introduced.

```harbour
@since 1.0.0
@since 2.0.0
@since 3.1.0
```

### 8.4 `@see`

Cross-references another symbol. Each `@see` line refers to **one** symbol.

```harbour
@see TBaseEntity
@see ValidateEmail
@see CalculateTotal
```

Multiple references require multiple `@see` lines. In the HTML output each
reference becomes a clickable anchor.

### 8.5 `@example`

Introduces a usage example. Everything after `@example` — including subsequent
non-tag lines — is accumulated until the next `@` tag or the end of the block.

```harbour
/**
 * @example
 *   LOCAL cId := GenerateUniqueId( "ORD", 16 )
 *   ? cId
 *   // Prints: ORD-20261009-A7X2
 */
```

Inline form:

```harbour
@example nTotal := CalculateTotal( 100, 5, 12, 0 )
```

### 8.6 `@deprecated` and `@internal`

```harbour
/**
 * @deprecated Use NewMethod() instead.
 */
FUNCTION OldMethod()
```

```harbour
/**
 * @internal Not part of the public API.
 */
```

---

## 9. Type Codes

| Code | Meaning         | Example values                |
|------|-----------------|-------------------------------|
| `C`  | Character       | `"hello"`, `""`               |
| `N`  | Numeric         | `42`, `3.14`, `0`             |
| `L`  | Logical         | `.T.`, `.F.`                  |
| `D`  | Date            | `Date()`, `CToD(...)`         |
| `A`  | Array           | `{ 1, 2, 3 }`                 |
| `H`  | Hash            | `{ => }`, `{ "k" => "v" }`    |
| `O`  | Object          | `clMyClass():New()`           |
| `X`  | Any / Unknown   | mixed types                   |
| `NIL`| No value        | procedure return              |

Type codes are **single letters** and are always written between parentheses:
`(C)`, `(N)`, `(L)`, `(D)`, `(A)`, `(H)`, `(O)`, `(X)`.

`harbour-doc` validates type codes. Unknown codes (e.g. `(STR)`, `(INT)`) are
flagged as errors by the `valid_types` rule.

---

## 10. Complete Real-World Examples

### 10.1 A full utility module

```harbour
/*
   BadaSystem
   Module        : sample_utils.prg
   SPDX-License-Identifier: MIT
*/

#include "minigui.ch"

/**
 * Calculates the total price including tax and discount.
 *
 * Applies a percentage tax to the base price, then subtracts an
 * optional flat discount. The result is never negative.
 *
 * @param nPrice    (N) -> Base unit price before tax
 * @param nQuantity (N) -> Number of units to calculate
 * @param nTaxRate  (N) -> Tax percentage to apply (0-100)
 * @param nDiscount (N) -> Flat discount amount to subtract
 * @return (N) -> Final total price after tax and discount
 * @since 1.0.0
 * @see FormatCurrency
 * @example
 *   nTotal := CalculateTotal( 100.00, 5, 12.0, 50.00 )
 *   // Result: 510.00
 */
FUNCTION CalculateTotal( nPrice, nQuantity, nTaxRate, nDiscount )
   LOCAL nSubtotal := nPrice * nQuantity
   LOCAL nTax      := nSubtotal * ( nTaxRate / 100 )
   LOCAL nTotal    := nSubtotal + nTax - nDiscount
   IF nTotal < 0
      nTotal := 0
   ENDIF
   RETURN nTotal

/**
 * Formats a numeric value as a currency string.
 * @param nAmount (N) -> The monetary amount to format
 * @param cSymbol (C) -> Currency symbol prefix (e.g., "$", "EUR")
 * @return (C) -> Formatted currency string
 * @since 1.0.0
 * @see CalculateTotal
 * @example
 *   cResult := FormatCurrency( 1234567.89, "$" )
 *   // Result: "$1,234,567.89"
 */
FUNCTION FormatCurrency( nAmount, cSymbol )
   ...
   RETURN cSymbol + cFormatted
```

### 10.2 A full OOP model

```harbour
#include "hbclass.ch"

/**
 * Customer model representing a business client.
 * Inherits from TBaseEntity and adds customer-specific
 * fields such as name, contact info, and credit balance.
 * @since 1.0.0
 * @see TBaseEntity
 * @see TOrder
 */
CLASS TCustomer FROM TBaseEntity
   DATA cName     INIT ""
   DATA cEmail    INIT ""
   DATA cPhone    INIT ""
   DATA cCity     INIT ""
   DATA nBalance  INIT 0

   METHOD New( cName, cEmail ) CONSTRUCTOR
   METHOD GetDisplayName()
   METHOD AddToBalance( nAmount, cReason )
   METHOD Validate()
   METHOD ToHash()
ENDCLASS

/**
 * Constructor for TCustomer. Calls the parent constructor and
 * initializes customer-specific fields.
 * @param cName  (C) -> Customer full name or company name
 * @param cEmail (C) -> Primary contact email address
 * @return (O) -> New TCustomer instance
 * @since 1.0.0
 * @example
 *   oCust := TCustomer():New( "Acme Corp", "billing@acme.com" )
 */
METHOD New( cName, cEmail ) CLASS TCustomer
   ::Super:New()
   IF cName == NIL  ; cName  := "" ; ENDIF
   IF cEmail == NIL ; cEmail := "" ; ENDIF
   ::cName  := cName
   ::cEmail := cEmail
   RETURN Self

/**
 * Returns a formatted display name combining the customer
 * name and city for use in UI labels and reports.
 * @return (C) -> Formatted display string
 * @since 1.5.0
 * @see GetId
 */
METHOD GetDisplayName() CLASS TCustomer
   LOCAL cDisplay := ::cName
   IF !Empty( ::cCity )
      cDisplay += " (" + ::cCity + ")"
   ENDIF
   RETURN cDisplay

/**
 * Adds or subtracts an amount from the customer credit balance.
 * Positive values increase the balance, negative values decrease it.
 * @param nAmount (N) -> Amount to add or subtract
 * @param cReason (C) -> Business reason for the adjustment
 * @return (N) -> New balance after adjustment
 * @since 2.0.0
 * @see Validate
 * @example
 *   nNewBal := oCust:AddToBalance( -150.00, "Invoice #4521 payment" )
 */
METHOD AddToBalance( nAmount, cReason ) CLASS TCustomer
   ::nBalance   += nAmount
   ::dUpdatedAt := Date()
   RETURN ::nBalance
```

---

## 11. Rules of the Parser (What works, what doesn't)

To guarantee correct extraction, respect the following parser behaviours.

### 11.1 Block boundaries

| Rule                                                         | Effect             |
|--------------------------------------------------------------|--------------------|
| Start with `/**`                                             | Recognized         |
| Start with `/*` (single asterisk)                            | Ignored            |
| Start with `//`                                              | Ignored            |
| Terminate with `*/`                                          | Recognized         |
| `/**` inside a string literal `"…/**…"`                      | Ignored (safe)     |
| `/**` after `//` on the same line                            | Ignored (safe)     |
| Unclosed block at EOF                                        | Flushed as token   |

### 11.2 Association

The doc block is associated with the **first symbol keyword** that follows it.
Blank lines are allowed between the block and the symbol. Any non-blank code
line resets the association.

```harbour
/** Documents Foo */
                          ← blank lines OK
FUNCTION Foo()            ← associated
```

```harbour
/** Intended for Foo */
LOCAL x := 1              ← breaks association
FUNCTION Foo()            ← NOT associated
```

### 11.3 Description vs. tag content

- The **description** is every non-tag line **before the first `@` tag**.
- After the first tag, non-tag lines belong to the previous tag (useful for
  `@example`, less so for `@param`).
- Place the free-text description first, then tags.

### 11.4 `@example` accumulation

Everything after `@example` — including blank lines and non-tag lines — is
captured until the next `@` tag or the closing `*/`.

### 11.5 Cleanup

The parser automatically strips:
- The leading `/**` and trailing `*/`.
- One leading `*` on each continuation line.
- Leading and trailing whitespace on every line.

So the following are all equivalent:

```harbour
/**
 * Brief.
 * @param x (N) -> X
 */

/** Brief. @param x (N) -> X */

/**
Brief.
@param x (N) -> X
*/
```

The multi-line `*`-prefixed form is the recommended style.

---

## 12. Best Practices & Style Guide

### 12.1 Always

- **Start every public symbol with a doc block.** Undocumented public symbols
  are flagged by the `documented` rule.
- **Write a one-line brief first.** This becomes the summary in the output.
- **Write the description before any tag.** The parser stops accumulating the
  description at the first tag.
- **Give every parameter a type code and a description.**
- **Give every function a `@return`.** Even procedures benefit from an explicit
  `@return (NIL) -> Nothing`.
- **Use `@since` on every public symbol.** Consumers need to know when APIs
  appear.
- **Use `@see` to link related symbols.** These become clickable anchors in
  HTML.

### 12.2 Prefer

- **Multi-line blocks with `*` prefixes.** They are easier to read and diff.
- **One `@param` per line.** Even when a function has many parameters.
- **One `@see` per line.** Multiple references need multiple lines.
- **Verbs in the third person** for functions and methods:
  *“Returns …”, “Calculates …”, “Validates …”.*
- **`Returns` / `Params` / `Value` section names** as generated automatically.

### 12.3 Avoid

- **`@param` in a different order than the actual signature.** The parser
  preserves order; mismatches confuse readers.
- **Inline `@param` and `@return` on the same line as `@example`.** Keep
  `@example` last.
- **Emojis inside doc comments.** Some Harbour lexers fail on multi-byte UTF-8
  sequences inside `.prg` files. Use HTML entities in the output, not in the
  source.
- **Doc blocks far away from the symbol.** Association breaks silently.

### 12.4 Naming convention for parameters

Use Hungarian notation consistent with the type code:

| Prefix | Type | Example    |
|--------|------|------------|
| `c`    | C    | `cName`    |
| `n`    | N    | `nAmount`  |
| `l`    | L    | `lEnabled` |
| `d`    | D    | `dStart`   |
| `a`    | A    | `aItems`   |
| `h`    | H    | `hData`    |
| `o`    | O    | `oObject`  |
| `x`    | X    | `xValue`   |

This makes the type code in `@param` redundant but self-documenting.

---

## 13. Common Mistakes

### 13.1 Using `/*` instead of `/**`

```harbour
/* This is NOT a doc block. */
FUNCTION Foo()              ← not documented
```

### 13.2 Placing the doc block too far away

```harbour
/**
 * Intended for Foo.
 */
#include "minigui.ch"        ← association lost
FUNCTION Foo()              ← not documented
```

### 13.3 Writing the description after a tag

```harbour
/**
 * @param nX (N) -> X value
 * This line is NOT part of the description.
 * It becomes part of the @param content.
 */
FUNCTION Foo( nX )
```

### 13.4 Missing `->` separator

The separator is **optional**. `@param cName (C) Customer name` and
`@param cName (C) -> Customer name` are equivalent. But for consistency across
the community, **use `->`**.

### 13.5 Using invalid type codes

```harbour
@param nAge (INT) -> Age in years    ← invalid; use (N)
@param cName (STR) -> Name           ← invalid; use (C)
```

### 13.6 Mixing `@return` and `@returns`

Both are recognized but produce different tag names internally. **Pick one and
stick with it.** The community standard is `@return` (singular).

### 13.7 Emojis in source

```harbour
/**
 * 📘 Returns the manual.   ← may trigger E0030 lexer errors
 */
```

---

## 14. Generating Documentation

Once your code follows the standard, generate documentation with the CLI.

```bash
# HTML (default)
harbour-doc generate -i src -o docs -f html

# Multiple formats in one run
harbour-doc generate -i src -o docs -f html -f txt -f json -f md

# Strict mode: warnings become errors
harbour-doc generate -i src -o docs -f html --strict

# Validate only (no output files)
harbour-doc validate -i src --strict

# Inspect token counts
harbour-doc scan -i src
```

**Format aliases:**

| Alias   | Canonical  | File          |
|---------|------------|---------------|
| `html`  | `html`     | `index.html`  |
| `htm`   | `html`     | `index.html`  |
| `md`    | `markdown` | `index.md`    |
| `markdown` | `markdown` | `index.md` |
| `txt`   | `text`     | `index.txt`   |
| `text`  | `text`     | `index.txt`   |
| `json`  | `json`     | `index.json`  |

**Exit codes:**

| Code | Meaning           |
|------|-------------------|
| `0`  | Success           |
| `1`  | Runtime error     |
| `2`  | Usage error       |

---

## 15. Quick Reference Card

```harbour
/**
 * <Brief one-line description.>
 *
 * <Extended description. Free text.>
 * <Ends at the first @ tag.>
 *
 * @param cName  (C) -> Parameter description
 * @param nCount (N) -> Parameter description
 * @return (L) -> .T. on success
 * @since 1.0.0
 * @see OtherSymbol
 * @see AnotherSymbol
 * @deprecated Use NewSymbol() instead.
 * @internal
 * @example
 *   LOCAL lOk := MyFunc( "test", 42 )
 */
FUNCTION MyFunc( cName, nCount )
   RETURN .T.
```

**Format rules:**

- Block starts with `/**` and ends with `*/`.
- Every continuation line begins with ` * `.
- One tag per line.
- `@param cName (C) -> Description`.
- `@return (Type) -> Description`.
- Description first; tags after.
- `@example` always last.

---

# PARTE 2 — MANUAL EN ESPAÑOL

## Tabla de Contenidos

1. Introducción
2. Filosofía y Alcance
3. El Bloque de Documentación
4. Documentar Funciones y Procedimientos
5. Documentar Clases
6. Documentar Métodos
7. Documentar Constantes (`#define`)
8. Referencia de Etiquetas
9. Códigos de Tipo
10. Ejemplos Completos del Mundo Real
11. Reglas del Parser (Qué funciona y qué no)
12. Buenas Prácticas y Guía de Estilo
13. Errores Comunes
14. Generar la Documentación
15. Tarjeta de Referencia Rápida

---

## 1. Introducción

`harbour-doc` es un generador de documentación para el lenguaje Harbour. Lee
los archivos fuente `.prg`, extrae los comentarios estructurados y produce
salida en HTML, Markdown, JSON o texto plano.

Este manual define el **estándar oficial de documentación** para Harbour. Si lo
sigues al pie de la letra, `harbour-doc` producirá documentación completa,
correcta y consultable de tu proyecto. El objetivo es establecer una única
convención que toda la comunidad Harbour pueda adoptar.

El estándar está inspirado en JSDoc, Doxygen y PHPDoc, pero está adaptado a la
sintaxis, los tipos y el modelo OOP (`hbclass.ch`) de Harbour.

---

## 2. Filosofía y Alcance

El estándar sigue tres principios:

1. **Proximidad** — La documentación vive directamente encima del símbolo que
   describe.
2. **Estructura** — Las etiquetas (`@param`, `@return`, `@see` …) llevan
   metadatos legibles por máquina; el texto libre lleva el significado para el
   humano.
3. **Simplicidad** — Sin directivas del compilador, sin archivos externos, sin
   anotaciones que Harbour no pueda parsear. Un bloque de documentación es
   simplemente un comentario `/** … */`.

`harbour-doc` documenta los siguientes tipos de símbolo:

| Símbolo    | Detectado por la palabra   |
|------------|----------------------------|
| Función    | `FUNCTION`                 |
| Procedimiento | `PROCEDURE`             |
| Clase      | `CLASS` (con `FROM`)       |
| Método     | `METHOD`                   |
| Constante  | `#define`                  |

---

## 3. El Bloque de Documentación

Un bloque de documentación es un comentario que comienza con `/**` (dos
asteriscos) y termina con `*/`. El `/**` inicial, el `*/` final y cada `*`
inicial de las líneas de continuación son eliminados automáticamente por el
parser.

### 3.1 Forma multilínea (recomendada)

```harbour
/**
 * Descripción breve en la primera línea.
 *
 * Descripción extendida en las líneas siguientes.
 * Puede abarcar varios párrafos.
 *
 * @param cName (C) -> Descripción del parámetro
 * @return (L) -> Descripción del valor de retorno
 * @since 1.0.0
 * @see SimboloRelacionado
 * @example
 *   LOCAL lOk := MiFuncion( "test" )
 */
FUNCTION MiFuncion( cName )
   ...
```

### 3.2 Forma de una sola línea (compacta)

```harbour
/** Devuelve el identificador de la entidad. @return (C) -> Cadena de ID */
METHOD GetId() CLASS TBaseEntity
   RETURN ::cId
```

### 3.3 Reglas críticas

- El bloque **debe** comenzar con `/**`. Un bloque que comienza con `/*` es
  tratado como comentario regular y es ignorado.
- Un bloque de documentación se asocia con la **siguiente línea de código no
  comentada**. Cualquier cosa entre el bloque y el símbolo (excepto líneas en
  blanco) rompe la asociación.
- El texto libre de la descripción solo se acumula **antes de la primera
  etiqueta**. El texto después de una etiqueta pertenece a esa etiqueta.
- Los comentarios regulares `/* … */` y de línea `//` no interfieren.

---

## 4. Documentar Funciones y Procedimientos

### 4.1 Función mínima

```harbour
/**
 * Calcula el precio total incluyendo impuesto.
 * @param nPrice (N) -> Precio unitario base
 * @return (N) -> Precio total final
 */
FUNCTION CalculateTotal( nPrice )
   RETURN nPrice * 1.12
```

### 4.2 Función completa

```harbour
/**
 * Calcula el precio total incluyendo impuesto y descuento.
 *
 * Esta función aplica un impuesto porcentual al precio base y luego
 * resta un descuento plano opcional. Los totales negativos se
 * ajustan a cero.
 *
 * @param nPrice    (N) -> Precio unitario base antes de impuesto
 * @param nQuantity (N) -> Número de unidades a calcular
 * @param nTaxRate  (N) -> Porcentaje de impuesto a aplicar (0-100)
 * @param nDiscount (N) -> Monto de descuento plano a restar
 * @return (N) -> Precio total final después de impuesto y descuento
 * @since 1.0.0
 * @see FormatCurrency
 * @example
 *   nTotal := CalculateTotal( 100.00, 5, 12.0, 50.00 )
 *   // Resultado: 510.00
 */
FUNCTION CalculateTotal( nPrice, nQuantity, nTaxRate, nDiscount )
   LOCAL nSubtotal := nPrice * nQuantity
   LOCAL nTax      := nSubtotal * ( nTaxRate / 100 )
   LOCAL nTotal    := nSubtotal + nTax - nDiscount
   IF nTotal < 0
      nTotal := 0
   ENDIF
   RETURN nTotal
```

### 4.3 Procedimientos

Los procedimientos no tienen valor de retorno. Documéntalos con `PROCEDURE` y
omite la etiqueta `@return` (o usa `@return (NIL) -> Nada`):

```harbour
/**
 * Registra un evento del sistema en la tabla de eventos.
 * @param cEventCode (C) -> Código de tipo de evento (INFO, WARN, ERROR)
 * @param cModule    (C) -> Nombre del módulo que genera el evento
 * @param cMessage   (C) -> Mensaje descriptivo del evento
 * @param cUser      (C) -> Usuario asociado al evento
 * @since 1.0.0
 * @see CalculateTotal
 * @example
 *   LogEvent( "INFO", "ORDERS", "Orden #1234 creada", "admin" )
 */
PROCEDURE LogEvent( cEventCode, cModule, cMessage, cUser )
   ...
   RETURN
```

---

## 5. Documentar Clases

La documentación de la clase va **inmediatamente encima** de la sentencia
`CLASS`. El parser detecta automáticamente la cláusula `FROM` y enlaza la clase
padre.

```harbour
/**
 * Clase base de entidad que provee campos y comportamiento comunes
 * para todos los objetos de modelo de negocio.
 *
 * Incluye seguimiento de auditoría, soporte de borrado lógico y
 * serialización a hash. Todos los modelos de dominio deben heredar
 * de esta clase.
 *
 * @since 1.0.0
 * @see TCustomer
 * @see TOrder
 */
CLASS TBaseEntity
   DATA cId        INIT ""
   DATA dCreatedAt INIT NIL
   DATA lIsActive  INIT .T.

   METHOD New() CONSTRUCTOR
   METHOD GetId()
   METHOD SetId( cId )
   METHOD ToHash()
ENDCLASS
```

### 5.1 Herencia

```harbour
/**
 * Modelo de cliente que representa a un cliente de negocio.
 * Hereda de TBaseEntity y añade campos específicos del cliente
 * como nombre, información de contacto y saldo de crédito.
 * @since 1.0.0
 * @see TBaseEntity
 * @see TOrder
 */
CLASS TCustomer FROM TBaseEntity
   DATA cName    INIT ""
   DATA cEmail   INIT ""
   DATA nBalance INIT 0

   METHOD New( cName, cEmail ) CONSTRUCTOR
   METHOD GetDisplayName()
   METHOD AddToBalance( nAmount, cReason )
ENDCLASS
```

El parser extrae `TBaseEntity` como padre y renderiza un enlace en la salida
HTML. La documentación de la clase **no** necesita `@param`/`@return`; esos
pertenecen al método constructor.

---

## 6. Documentar Métodos

Un método se documenta exactamente igual que una función. El bloque de
documentación debe situarse inmediatamente encima de la sentencia
`METHOD … CLASS …`.

### 6.1 Constructor

```harbour
/**
 * Constructor de TCustomer. Llama al constructor padre e
 * inicializa los campos específicos del cliente.
 * @param cName  (C) -> Nombre completo o razón social del cliente
 * @param cEmail (C) -> Dirección de correo electrónico principal
 * @return (O) -> Nueva instancia de TCustomer
 * @since 1.0.0
 * @example
 *   oCust := TCustomer():New( "Acme Corp", "billing@acme.com" )
 */
METHOD New( cName, cEmail ) CLASS TCustomer
   ::Super:New()
   ::cName  := cName
   ::cEmail := cEmail
   RETURN Self
```

### 6.2 Método regular

```harbour
/**
 * Suma o resta un monto del saldo de crédito del cliente.
 *
 * Los valores positivos aumentan el saldo, los negativos lo reducen.
 * Registra la razón para auditoría.
 *
 * @param nAmount (N) -> Monto a sumar (positivo) o restar (negativo)
 * @param cReason (C) -> Razón de negocio del ajuste
 * @return (N) -> Nuevo saldo después del ajuste
 * @since 2.0.0
 * @see Validate
 * @example
 *   nNewBal := oCust:AddToBalance( -150.00, "Pago factura #4521" )
 */
METHOD AddToBalance( nAmount, cReason ) CLASS TCustomer
   ::nBalance  += nAmount
   ::dUpdatedAt := Date()
   RETURN ::nBalance
```

### 6.3 Método que sobrescribe al padre

```harbour
/**
 * Valida los datos del cliente antes de persistir.
 * Extiende la validación base con verificación de correo y nombre.
 * @return (L) -> .T. si los datos del cliente son válidos
 * @since 1.0.0
 * @see TBaseEntity
 */
METHOD Validate() CLASS TCustomer
   LOCAL lValid := ::Super:Validate()
   IF Empty( ::cName )
      lValid := .F.
   ENDIF
   RETURN lValid
```

---

## 7. Documentar Constantes (`#define`)

Las constantes se documentan con un bloque de documentación **inmediatamente
encima** de la línea `#define`. El parser extrae tanto el nombre como el valor.

```harbour
/**
 * Longitud máxima permitida para campos de nombre de cliente.
 * Los valores que excedan este límite se rechazan en la validación.
 * @since 1.0.0
 * @see ValidateCustomerName
 */
#define MAX_NAME_LENGTH      60

/**
 * Número máximo de líneas permitidas por pedido.
 * Los pedidos que excedan este límite deben dividirse.
 * @since 2.0.0
 * @see ValidateOrderItems
 */
#define MAX_ORDER_ITEMS      500

/**
 * Cadena de versión de la aplicación mostrada en toda la salida.
 * @since 1.0.0
 */
#define APP_VERSION          "3.1.0"
```

Las etiquetas `@param` y `@return` no aplican a constantes. Usa solo `@since`
y `@see`.

---

## 8. Referencia de Etiquetas

Todas las etiquetas comienzan con `@` y aparecen en su propia línea dentro del
bloque de documentación.

| Etiqueta      | Aplica a                        | Propósito                                |
|---------------|---------------------------------|------------------------------------------|
| `@param`      | Función, Método, Constructor    | Documenta un parámetro                   |
| `@return`     | Función, Método, Constructor    | Documenta el valor de retorno            |
| `@returns`    | (alias de `@return`)            | Igual que el anterior                    |
| `@since`      | Cualquier símbolo               | Versión en la que se añadió el símbolo   |
| `@see`        | Cualquier símbolo               | Referencia cruzada a otro símbolo        |
| `@example`    | Cualquier símbolo               | Ejemplo de uso (multilínea, acumulativo) |
| `@deprecated` | Cualquier símbolo               | Marca el símbolo como obsoleto           |
| `@internal`   | Cualquier símbolo               | Marca el símbolo como interno            |

### 8.1 `@param`

**Sintaxis:**

```
@param cName (Tipo) -> Descripción
```

- `cName` — nombre del parámetro (obligatorio, primera palabra).
- `(Tipo)` — código de tipo de una letra entre paréntesis (opcional).
- `->` — separador (opcional; se elimina automáticamente).
- `Descripción` — texto libre (opcional).

**Ejemplos:**

```harbour
@param cName (C) -> Nombre completo del cliente
@param nAmount (N) -> Monto a sumar
@param lFlag (L) -> Bandera de habilitación
@param dStart (D) -> Fecha de inicio del rango
@param aItems (A) -> Arreglo de hashes de ítems
@param hData (H) -> Hash de datos
@param oObject (O) -> Instancia de objeto
@param xValue (X) -> Cualquier valor
```

Forma simple (sin tipo, sin flecha):

```harbour
@param cName Nombre completo del cliente
```

### 8.2 `@return`

**Sintaxis:**

```
@return (Tipo) -> Descripción
```

**Ejemplos:**

```harbour
@return (C) -> Cadena de visualización formateada
@return (N) -> Nuevo saldo después del ajuste
@return (L) -> .T. si la validación pasa
@return (O) -> Nueva instancia de TCustomer
@return (H) -> Hash con todos los campos de la entidad
@return (NIL) -> Nada (procedimiento)
```

### 8.3 `@since`

Registra la versión en la que se introdujo el símbolo.

```harbour
@since 1.0.0
@since 2.0.0
@since 3.1.0
```

### 8.4 `@see`

Referencia cruzada a otro símbolo. Cada línea `@see` se refiere a **un**
símbolo.

```harbour
@see TBaseEntity
@see ValidateEmail
@see CalculateTotal
```

Múltiples referencias requieren múltiples líneas `@see`. En la salida HTML
cada referencia se convierte en un ancla clicable.

### 8.5 `@example`

Introduce un ejemplo de uso. Todo lo que sigue a `@example` — incluyendo las
líneas no etiqueta posteriores — se acumula hasta la siguiente etiqueta `@` o
el final del bloque.

```harbour
/**
 * @example
 *   LOCAL cId := GenerateUniqueId( "ORD", 16 )
 *   ? cId
 *   // Imprime: ORD-20261009-A7X2
 */
```

Forma en línea:

```harbour
@example nTotal := CalculateTotal( 100, 5, 12, 0 )
```

### 8.6 `@deprecated` y `@internal`

```harbour
/**
 * @deprecated Use NewMethod() en su lugar.
 */
FUNCTION OldMethod()
```

```harbour
/**
 * @internal No forma parte de la API pública.
 */
```

---

## 9. Códigos de Tipo

| Código | Significado       | Valores de ejemplo            |
|--------|-------------------|-------------------------------|
| `C`    | Carácter          | `"hola"`, `""`                |
| `N`    | Numérico          | `42`, `3.14`, `0`             |
| `L`    | Lógico            | `.T.`, `.F.`                  |
| `D`    | Fecha             | `Date()`, `CToD(...)`         |
| `A`    | Arreglo           | `{ 1, 2, 3 }`                 |
| `H`    | Hash              | `{ => }`, `{ "k" => "v" }`    |
| `O`    | Objeto            | `clMyClass():New()`           |
| `X`    | Cualquiera / Desconocido | tipos mixtos           |
| `NIL`  | Sin valor         | retorno de procedimiento      |

Los códigos de tipo son **letras individuales** y siempre se escriben entre
paréntesis: `(C)`, `(N)`, `(L)`, `(D)`, `(A)`, `(H)`, `(O)`, `(X)`.

`harbour-doc` valida los códigos de tipo. Los códigos desconocidos (p. ej.
`(STR)`, `(INT)`) son marcados como errores por la regla `valid_types`.

---

## 10. Ejemplos Completos del Mundo Real

### 10.1 Módulo utilitario completo

```harbour
/*
   BadaSystem
   Módulo        : sample_utils.prg
   SPDX-License-Identifier: MIT
*/

#include "minigui.ch"

/**
 * Calcula el precio total incluyendo impuesto y descuento.
 *
 * Aplica un impuesto porcentual al precio base y luego resta un
 * descuento plano opcional. El resultado nunca es negativo.
 *
 * @param nPrice    (N) -> Precio unitario base antes de impuesto
 * @param nQuantity (N) -> Número de unidades a calcular
 * @param nTaxRate  (N) -> Porcentaje de impuesto a aplicar (0-100)
 * @param nDiscount (N) -> Monto de descuento plano a restar
 * @return (N) -> Precio total final después de impuesto y descuento
 * @since 1.0.0
 * @see FormatCurrency
 * @example
 *   nTotal := CalculateTotal( 100.00, 5, 12.0, 50.00 )
 *   // Resultado: 510.00
 */
FUNCTION CalculateTotal( nPrice, nQuantity, nTaxRate, nDiscount )
   LOCAL nSubtotal := nPrice * nQuantity
   LOCAL nTax      := nSubtotal * ( nTaxRate / 100 )
   LOCAL nTotal    := nSubtotal + nTax - nDiscount
   IF nTotal < 0
      nTotal := 0
   ENDIF
   RETURN nTotal

/**
 * Formatea un valor numérico como cadena de moneda.
 * @param nAmount (N) -> Monto monetario a formatear
 * @param cSymbol (C) -> Prefijo de símbolo de moneda (p. ej. "$", "EUR")
 * @return (C) -> Cadena de moneda formateada
 * @since 1.0.0
 * @see CalculateTotal
 * @example
 *   cResult := FormatCurrency( 1234567.89, "$" )
 *   // Resultado: "$1,234,567.89"
 */
FUNCTION FormatCurrency( nAmount, cSymbol )
   ...
   RETURN cSymbol + cFormatted
```

### 10.2 Modelo OOP completo

```harbour
#include "hbclass.ch"

/**
 * Modelo de cliente que representa a un cliente de negocio.
 * Hereda de TBaseEntity y añade campos específicos del cliente
 * como nombre, información de contacto y saldo de crédito.
 * @since 1.0.0
 * @see TBaseEntity
 * @see TOrder
 */
CLASS TCustomer FROM TBaseEntity
   DATA cName     INIT ""
   DATA cEmail    INIT ""
   DATA cPhone    INIT ""
   DATA cCity     INIT ""
   DATA nBalance  INIT 0

   METHOD New( cName, cEmail ) CONSTRUCTOR
   METHOD GetDisplayName()
   METHOD AddToBalance( nAmount, cReason )
   METHOD Validate()
   METHOD ToHash()
ENDCLASS

/**
 * Constructor de TCustomer. Llama al constructor padre e
 * inicializa los campos específicos del cliente.
 * @param cName  (C) -> Nombre completo o razón social del cliente
 * @param cEmail (C) -> Dirección de correo electrónico principal
 * @return (O) -> Nueva instancia de TCustomer
 * @since 1.0.0
 * @example
 *   oCust := TCustomer():New( "Acme Corp", "billing@acme.com" )
 */
METHOD New( cName, cEmail ) CLASS TCustomer
   ::Super:New()
   IF cName == NIL  ; cName  := "" ; ENDIF
   IF cEmail == NIL ; cEmail := "" ; ENDIF
   ::cName  := cName
   ::cEmail := cEmail
   RETURN Self

/**
 * Devuelve un nombre de visualización formateado combinando el
 * nombre del cliente y la ciudad para usar en etiquetas UI e informes.
 * @return (C) -> Cadena de visualización formateada
 * @since 1.5.0
 * @see GetId
 */
METHOD GetDisplayName() CLASS TCustomer
   LOCAL cDisplay := ::cName
   IF !Empty( ::cCity )
      cDisplay += " (" + ::cCity + ")"
   ENDIF
   RETURN cDisplay

/**
 * Suma o resta un monto del saldo de crédito del cliente.
 * Los valores positivos aumentan el saldo, los negativos lo reducen.
 * @param nAmount (N) -> Monto a sumar o restar
 * @param cReason (C) -> Razón de negocio del ajuste
 * @return (N) -> Nuevo saldo después del ajuste
 * @since 2.0.0
 * @see Validate
 * @example
 *   nNewBal := oCust:AddToBalance( -150.00, "Pago factura #4521" )
 */
METHOD AddToBalance( nAmount, cReason ) CLASS TCustomer
   ::nBalance   += nAmount
   ::dUpdatedAt := Date()
   RETURN ::nBalance
```

---

## 11. Reglas del Parser (Qué funciona y qué no)

Para garantizar una extracción correcta, respeta los siguientes
comportamientos del parser.

### 11.1 Límites del bloque

| Regla                                                         | Efecto             |
|---------------------------------------------------------------|--------------------|
| Comienza con `/**`                                            | Reconocido         |
| Comienza con `/*` (un solo asterisco)                         | Ignorado           |
| Comienza con `//`                                             | Ignorado           |
| Termina con `*/`                                              | Reconocido         |
| `/**` dentro de una cadena `"…/**…"`                          | Ignorado (seguro)  |
| `/**` después de `//` en la misma línea                       | Ignorado (seguro)  |
| Bloque sin cerrar al final del archivo                        | Se emite igual     |

### 11.2 Asociación

El bloque de documentación se asocia con la **primera palabra clave de símbolo**
que le sigue. Se permiten líneas en blanco entre el bloque y el símbolo.
Cualquier línea de código no vacía reinicia la asociación.

```harbour
/** Documenta Foo */
                          ← líneas en blanco OK
FUNCTION Foo()            ← asociado
```

```harbour
/** Destinado a Foo */
LOCAL x := 1              ← rompe la asociación
FUNCTION Foo()            ← NO asociado
```

### 11.3 Descripción vs. contenido de etiqueta

- La **descripción** son todas las líneas no etiqueta **antes de la primera
  etiqueta `@`**.
- Después de la primera etiqueta, las líneas no etiqueta pertenecen a la
  etiqueta anterior (útil para `@example`, menos para `@param`).
- Coloca la descripción de texto libre primero, luego las etiquetas.

### 11.4 Acumulación de `@example`

Todo lo que sigue a `@example` — incluyendo líneas en blanco y no etiqueta —
se captura hasta la siguiente etiqueta `@` o el cierre `*/`.

### 11.5 Limpieza

El parser elimina automáticamente:
- El `/**` inicial y el `*/` final.
- Un `*` inicial en cada línea de continuación.
- Espacios en blanco al inicio y al final de cada línea.

Por lo tanto, las siguientes formas son equivalentes:

```harbour
/**
 * Breve.
 * @param x (N) -> X
 */

/** Breve. @param x (N) -> X */

/**
Breve.
@param x (N) -> X
*/
```

La forma multilínea con prefijo `*` es el estilo recomendado.

---

## 12. Buenas Prácticas y Guía de Estilo

### 12.1 Siempre

- **Comienza cada símbolo público con un bloque de documentación.** Los
  símbolos públicos sin documentar son marcados por la regla `documented`.
- **Escribe una descripción breve de una línea primero.** Se convierte en el
  resumen en la salida.
- **Escribe la descripción antes de cualquier etiqueta.** El parser deja de
  acumular la descripción en la primera etiqueta.
- **Da a cada parámetro un código de tipo y una descripción.**
- **Da a cada función un `@return`.** Incluso los procedimientos se benefician
  de un `@return (NIL) -> Nada` explícito.
- **Usa `@since` en cada símbolo público.** Los consumidores necesitan saber
  cuándo aparecen las APIs.
- **Usa `@see` para enlazar símbolos relacionados.** Se convierten en anclas
  clicables en HTML.

### 12.2 Prefiere

- **Bloques multilínea con prefijos `*`.** Son más fáciles de leer y comparar.
- **Un `@param` por línea.** Incluso cuando una función tiene muchos
  parámetros.
- **Un `@see` por línea.** Múltiples referencias requieren múltiples líneas.
- **Verbos en tercera persona** para funciones y métodos:
  *“Devuelve …”, “Calcula …”, “Valida …”.*
- **Nombres de sección `Returns` / `Params` / `Value`** como se generan
  automáticamente.

### 12.3 Evita

- **`@param` en un orden distinto al de la firma real.** El parser preserva el
  orden; los desajustes confunden a los lectores.
- **`@param` y `@return` en línea con `@example`.** Mantén `@example` al final.
- **Emojis dentro de comentarios de documentación.** Algunos lexers de Harbour
  fallan con secuencias UTF-8 multibyte dentro de archivos `.prg`. Usa
  entidades HTML en la salida, no en el código fuente.
- **Bloques de documentación lejos del símbolo.** La asociación se rompe
  silenciosamente.

### 12.4 Convención de nombres para parámetros

Usa notación húngara consistente con el código de tipo:

| Prefijo | Tipo | Ejemplo    |
|---------|------|------------|
| `c`     | C    | `cName`    |
| `n`     | N    | `nAmount`  |
| `l`     | L    | `lEnabled` |
| `d`     | D    | `dStart`   |
| `a`     | A    | `aItems`   |
| `h`     | H    | `hData`    |
| `o`     | O    | `oObject`  |
| `x`     | X    | `xValue`   |

Esto hace redundante el código de tipo en `@param` pero auto-documentado.

---

## 13. Errores Comunes

### 13.1 Usar `/*` en lugar de `/**`

```harbour
/* Esto NO es un bloque de documentación. */
FUNCTION Foo()              ← no documentado
```

### 13.2 Colocar el bloque demasiado lejos

```harbour
/**
 * Destinado a Foo.
 */
#include "minigui.ch"        ← asociación perdida
FUNCTION Foo()              ← no documentado
```

### 13.3 Escribir la descripción después de una etiqueta

```harbour
/**
 * @param nX (N) -> Valor de X
 * Esta línea NO es parte de la descripción.
 * Se convierte en parte del contenido de @param.
 */
FUNCTION Foo( nX )
```

### 13.4 Separador `->` ausente

El separador es **opcional**. `@param cName (C) Nombre del cliente` y
`@param cName (C) -> Nombre del cliente` son equivalentes. Pero por
consistencia en la comunidad, **usa `->`**.

### 13.5 Usar códigos de tipo inválidos

```harbour
@param nAge (INT) -> Edad en años    ← inválido; usa (N)
@param cName (STR) -> Nombre         ← inválido; usa (C)
```

### 13.6 Mezclar `@return` y `@returns`

Ambos son reconocidos pero producen nombres de etiqueta internos diferentes.
**Elige uno y sé consistente.** El estándar de la comunidad es `@return`
(singular).

### 13.7 Emojis en el código fuente

```harbour
/**
 * 📘 Devuelve el manual.   ← puede provocar errores E0030 del lexer
 */
```

---

## 14. Generar la Documentación

Una vez que tu código sigue el estándar, genera documentación con la CLI.

```bash
# HTML (predeterminado)
harbour-doc generate -i src -o docs -f html

# Múltiples formatos en una sola ejecución
harbour-doc generate -i src -o docs -f html -f txt -f json -f md

# Modo estricto: las advertencias se convierten en errores
harbour-doc generate -i src -o docs -f html --strict

# Solo validar (sin archivos de salida)
harbour-doc validate -i src --strict

# Inspeccionar conteo de tokens
harbour-doc scan -i src
```

**Alias de formato:**

| Alias      | Canónico   | Archivo       |
|------------|------------|---------------|
| `html`     | `html`     | `index.html`  |
| `htm`      | `html`     | `index.html`  |
| `md`       | `markdown` | `index.md`    |
| `markdown` | `markdown` | `index.md`    |
| `txt`      | `text`     | `index.txt`   |
| `text`     | `text`     | `index.txt`   |
| `json`     | `json`     | `index.json`  |

**Códigos de salida:**

| Código | Significado          |
|--------|----------------------|
| `0`    | Éxito                |
| `1`    | Error de ejecución   |
| `2`    | Error de uso         |

---

## 15. Tarjeta de Referencia Rápida

```harbour
/**
 * <Descripción breve de una línea.>
 *
 * <Descripción extendida. Texto libre.>
 * <Termina en la primera etiqueta @.>
 *
 * @param cName  (C) -> Descripción del parámetro
 * @param nCount (N) -> Descripción del parámetro
 * @return (L) -> .T. en caso de éxito
 * @since 1.0.0
 * @see OtroSimbolo
 * @see OtroSimboloMas
 * @deprecated Use NuevoSimbolo() en su lugar.
 * @internal
 * @example
 *   LOCAL lOk := MiFunc( "test", 42 )
 */
FUNCTION MiFunc( cName, nCount )
   RETURN .T.
```

**Reglas de formato:**

- El bloque comienza con `/**` y termina con `*/`.
- Cada línea de continuación comienza con ` * `.
- Una etiqueta por línea.
- `@param cName (C) -> Descripción`.
- `@return (Tipo) -> Descripción`.
- Descripción primero; etiquetas después.
- `@example` siempre al final.

---

**Fin del manual / End of manual.**
