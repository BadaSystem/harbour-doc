# 📘 harbour-doc — User Guide / Manual de Usuario

---

# 🇬🇧 ENGLISH VERSION

## 📖 Table of Contents

1. [What is harbour-doc?](#-what-is-harbour-doc)
2. [Installation](#-installation)
3. [Quick Start](#-quick-start)
4. [Command Reference](#-command-reference)
5. [Documenting Your Code](#-documenting-your-code)
6. [Output Formats](#-output-formats)
7. [Configuration File](#-configuration-file)
8. [HTML Output Features](#-html-output-features)
9. [Validation & Strict Mode](#-validation--strict-mode)
10. [Examples Gallery](#-examples-gallery)
11. [Troubleshooting](#-troubleshooting)

---

## 🎯 What is harbour-doc?

**harbour-doc** is a modern, zero-dependency documentation generator for the **Harbour** programming language.

It reads your `.prg` source files, extracts structured documentation from `/** … */` comment blocks, and produces beautiful, searchable API documentation in **HTML**, **Markdown**, **JSON**, or **Text**.

Think **JSDoc** for JavaScript, **Doxygen** for C++, **PHPDoc** for PHP — but designed natively for the Harbour ecosystem.

> ✨ **One standard. One tool. Complete documentation.**

---

## 💾 Installation

### Requirements

| Component | Version |
|-----------|---------|
| Harbour | 3.2.0dev or higher |
| MiniGUI | Extended |
| Compiler | BCC 32-bit (or compatible) |
| OS | Windows / Linux / macOS |

```bash
git clone https://github.com/BadaSystem/harbour-doc.git
cd harbour-doc
```

### Verify installation

```bash
harbour-doc version
```

Expected output:

```
harbour-doc v1.2.0
Harbour Documentation Generator
Copyright (c) 2026 BadaSystem
Author: Marcos Jarrin
Harbour 3.2.0dev + MiniGUI Extended
```

---

## ⚡ Quick Start

### 1️⃣ Document a function

Add a `/** … */` block above every public symbol:

```harbour
/**
 * Calculates the total price including tax and discount.
 * @param nPrice    (N) -> Base unit price before tax
 * @param nQuantity (N) -> Number of units to calculate
 * @param nTaxRate  (N) -> Tax percentage to apply (0-100)
 * @param nDiscount (N) -> Flat discount amount to subtract
 * @return (N) -> Final total price
 * @since 1.0.0
 * @see FormatCurrency
 * @example
 *   nTotal := CalculateTotal( 100.00, 5, 12.0, 50.00 )
 *   // Result: 510.00
 */
FUNCTION CalculateTotal( nPrice, nQuantity, nTaxRate, nDiscount )
   ...
```

### 2️⃣ Generate documentation

```bash
harbour-doc generate -i src -o docs -f html
```

### 3️⃣ Open the result

Open `docs/index.html` in your browser. 🎉

---

## 🖥️ Command Reference

### Syntax

```bash
harbour-doc <command> [options]
```

### Commands

| Command    | Purpose                                              |
|------------|------------------------------------------------------|
| `generate` | 🏗️ Scan source, extract symbols, generate output     |
| `validate` | ✅ Validate documentation quality without output     |
| `scan`     | 🔍 Tokenize source and report symbol counts          |
| `help`     | ❓ Show help                                         |
| `version`  | ℹ️ Show version information                          |

### Options

| Flag                    | Description                                    |
|-------------------------|------------------------------------------------|
| `-i, --input <dir>`     | Input directory with `.prg` files              |
| `-o, --output <dir>`    | Output directory for generated docs            |
| `-f, --format <fmt>`    | Output format (repeatable)                     |
| `--strict`              | Treat warnings as errors                       |
| `-V, --verbose`         | Enable verbose logging                         |
| `--no-highlight`        | Disable syntax highlighting                    |

### Format aliases

| Alias      | Canonical  | Output file   |
|------------|------------|---------------|
| `html`     | `html`     | `index.html`  |
| `htm`      | `html`     | `index.html`  |
| `md`       | `markdown` | `index.md`    |
| `markdown` | `markdown` | `index.md`    |
| `txt`      | `text`     | `index.txt`   |
| `text`     | `text`     | `index.txt`   |
| `json`     | `json`     | `index.json`  |

---

## ✍️ Documenting Your Code

### Supported tags

| Tag            | Purpose                                       |
|----------------|-----------------------------------------------|
| `@param`       | Documents a function/method parameter         |
| `@return`      | Documents the return value                    |
| `@since`       | Version in which the symbol was added         |
| `@see`         | Cross-reference another symbol                |
| `@example`     | Usage example (multi-line, auto-accumulated)  |
| `@deprecated`  | Marks the symbol as deprecated                |
| `@internal`    | Marks the symbol as internal                  |

### Type codes

| Code | Meaning          | Code | Meaning       |
|------|------------------|------|---------------|
| `C`  | Character        | `H`  | Hash          |
| `N`  | Numeric          | `O`  | Object        |
| `L`  | Logical          | `X`  | Any / Unknown |
| `D`  | Date             | `NIL`| No value      |
| `A`  | Array            |      |               |

### Symbol coverage

| Symbol     | Detected by                     |
|------------|---------------------------------|
| Function   | `FUNCTION`                      |
| Procedure  | `PROCEDURE`                     |
| Class      | `CLASS` (with `FROM`)           |
| Method     | `METHOD`                        |
| Constant   | `#define`                       |

### Example: Function

```harbour
/**
 * Converts a numeric value to a formatted currency string.
 * @param nAmount (N) -> Amount to format
 * @param cSymbol (C) -> Currency symbol (default: "$")
 * @return (C) -> Formatted currency string
 * @since 1.2.0
 * @example
 *   cResult := FormatCurrency( 1234.56 )
 *   // Result: "$1,234.56"
 */
FUNCTION FormatCurrency( nAmount, cSymbol )
```

### Example: Class

```harbour
/**
 * Represents a customer entity with validation.
 * @since 2.0.0
 * @see Order
 * @example
 *   oCustomer := Customer():New( "John Doe" )
 */
CLASS Customer FROM Entity
```

### Example: Constant

```harbour
/**
 * Maximum allowed name length in characters.
 * @since 1.0.0
 */
#define MAX_NAME_LENGTH 60
```

---

## 📦 Output Formats

### HTML (default)

```bash
harbour-doc generate -i src -o docs -f html
```

Produces a **standalone, self-contained** `index.html` with search, filters, dark mode, and collapsible cards.

### Markdown

```bash
harbour-doc generate -i src -o docs -f md
```

Produces a clean `index.md` with table of contents, code blocks, and cross-references.

### JSON

```bash
harbour-doc generate -i src -o docs -f json
```

Produces a machine-readable `index.json` with the full AST.

### Text

```bash
harbour-doc generate -i src -o docs -f txt
```

Produces a plain-text `index.txt` for terminal or legacy consumption.

### All formats at once ⭐

```bash
harbour-doc generate -i src -o docs -f html -f md -f json -f txt
```

Generates **all four artifacts** in a single run.

---

## ⚙️ Configuration File

harbour-doc reads an optional `harbour-doc.ini` file from the current directory.

### Example `harbour-doc.ini`

```ini
[general]
input_dir  = src
output_dir = docs
format     = html,md,json
lang       = EN
strict     = false
verbose    = false

[logging]
logfile = harbour-doc.log

[validation]
max_file_mb = 50
```

### Precedence

```
CLI flags  >  INI file  >  hardcoded defaults
```

---

## 🎨 HTML Output Features

| Feature                 | Description                                    |
|-------------------------|------------------------------------------------|
| 🌙 Dark mode toggle     | Persistent via `localStorage`                  |
| 🔍 Live search          | Instant filter by name or content              |
| 🎛️ Type filter buttons  | Filter by function, class, method, constant    |
| 📂 Collapsible cards    | Click headers to expand/collapse               |
| 📋 Copy-to-clipboard    | One-click copy of code examples                |
| 🔗 Cross-reference links| `@see` tags become clickable anchors           |
| 📱 Responsive layout    | Works on desktop, tablet, and mobile           |
| 🖨️ Print-friendly       | Collapses filters and controls when printing   |
| 📊 Statistics dashboard | Symbol counts by type at a glance              |

---

## ✅ Validation & Strict Mode

### Validate only (no output)

```bash
harbour-doc validate -i src
```

### Strict mode

```bash
harbour-doc validate -i src --strict
```

In strict mode, **warnings count as errors**. The build fails if any public API is undocumented.

### Validation rules

| Rule             | Severity | Description                          |
|------------------|----------|--------------------------------------|
| `documented`     | error    | Every symbol must have a brief       |
| `params_match`   | warning  | Functions with params need `@param`  |
| `return_doc`     | warning  | Functions need `@return`             |
| `valid_types`    | error    | Type codes must be valid             |

---

## 🎓 Examples Gallery

### Example 1: Single file, HTML output

```bash
harbour-doc generate -i src -o docs -f html
```

### Example 2: All formats

```bash
harbour-doc generate -i src -o docs -f html -f md -f json -f txt
```

### Example 3: Strict validation

```bash
harbour-doc validate -i src --strict
```

### Example 4: Inspect tokens per file

```bash
harbour-doc scan -i src
```

### Example 5: Verbose logging

```bash
harbour-doc generate -i src -o docs -f html -V
```

### Example 6: Custom input/output

```bash
harbour-doc generate -i C:\Projects\MyApp\src -o C:\Docs\MyApp -f html
```

---

## 🛠️ Troubleshooting

### Problem: `err.no.files.found`

**Cause:** No `.prg` files in the input directory.

**Solution:** Check the `-i` path. Ensure it contains `.prg` files.

```bash
harbour-doc scan -i src
```

### Problem: `err.invalid.format`

**Cause:** Unsupported format name.

**Solution:** Use a valid alias: `html`, `md`, `json`, `txt`.

### Problem: No symbols extracted

**Cause:** Comments not in `/** … */` format.

**Solution:** Ensure doc blocks start with `/**` (not `/*` or `//`).

### Problem: HTML file is empty

**Cause:** No documented symbols found.

**Solution:** Run `harbour-doc scan -i src` to verify tokens.

### Problem: `--strict` fails the build

**Cause:** Some symbols lack documentation.

**Solution:** Run without `--strict` first, then review the report:

```bash
harbour-doc validate -i src
```

---

# 🇪🇸 VERSIÓN EN ESPAÑOL

## 📖 Tabla de Contenidos

1. [¿Qué es harbour-doc?](#-qué-es-harbour-doc)
2. [Instalación](#-instalación)
3. [Inicio Rápido](#-inicio-rápido)
4. [Referencia de Comandos](#-referencia-de-comandos)
5. [Documentar tu Código](#-documentar-tu-código)
6. [Formatos de Salida](#-formatos-de-salida)
7. [Archivo de Configuración](#-archivo-de-configuración)
8. [Características del HTML](#-características-del-html)
9. [Validación y Modo Estricto](#-validación-y-modo-estricto)
10. [Galería de Ejemplos](#-galería-de-ejemplos)
11. [Solución de Problemas](#-solución-de-problemas)

---

## 🎯 ¿Qué es harbour-doc?

**harbour-doc** es un generador de documentación moderno y sin dependencias para el lenguaje **Harbour**.

Lee tus archivos fuente `.prg`, extrae documentación estructurada de los bloques de comentarios `/** … */` y produce documentación de API hermosa y buscable en **HTML**, **Markdown**, **JSON** o **Texto plano**.

Piensa en **JSDoc** para JavaScript, **Doxygen** para C++, **PHPDoc** para PHP — pero diseñado nativamente para el ecosistema Harbour.

> ✨ **Un solo estándar. Una sola herramienta. Documentación completa.**

---

## 💾 Instalación

### Requisitos

| Componente | Versión |
|------------|---------|
| Harbour | 3.2.0dev o superior |
| MiniGUI | Extended |
| Compilador | BCC 32-bit (o compatible) |
| SO | Windows / Linux / macOS |

```bash
git clone https://github.com/BadaSystem/harbour-doc.git
cd harbour-doc
```

### Verificar la instalación

```bash
harbour-doc version
```

Salida esperada:

```
harbour-doc v3.2.0
Harbour Documentation Generator
Copyright (c) 2026 BadaSystem
Author: Marcos Jarrin
Harbour 3.2.0dev + MiniGUI Extended
```

---

## ⚡ Inicio Rápido

### 1️⃣ Documenta una función

Añade un bloque `/** … */` encima de cada símbolo público:

```harbour
/**
 * Calcula el precio total incluyendo impuestos y descuento.
 * @param nPrice    (N) -> Precio unitario base antes de impuestos
 * @param nQuantity (N) -> Número de unidades a calcular
 * @param nTaxRate  (N) -> Porcentaje de impuesto a aplicar (0-100)
 * @param nDiscount (N) -> Descuento plano a restar
 * @return (N) -> Precio total final
 * @since 1.0.0
 * @see FormatCurrency
 * @example
 *   nTotal := CalculateTotal( 100.00, 5, 12.0, 50.00 )
 *   // Resultado: 510.00
 */
FUNCTION CalculateTotal( nPrice, nQuantity, nTaxRate, nDiscount )
   ...
```

### 2️⃣ Genera la documentación

```bash
harbour-doc generate -i src -o docs -f html
```

### 3️⃣ Abre el resultado

Abre `docs/index.html` en tu navegador. 🎉

---

## 🖥️ Referencia de Comandos

### Sintaxis

```bash
harbour-doc <comando> [opciones]
```

### Comandos

| Comando    | Propósito                                            |
|------------|------------------------------------------------------|
| `generate` | 🏗️ Escanea el código, extrae símbolos y genera salida |
| `validate` | ✅ Valida la calidad de la documentación sin salida   |
| `scan`     | 🔍 Tokeniza el código y reporta conteo de símbolos   |
| `help`     | ❓ Muestra ayuda                                     |
| `version`  | ℹ️ Muestra información de versión                    |

### Opciones

| Bandera                 | Descripción                                    |
|-------------------------|------------------------------------------------|
| `-i, --input <dir>`     | Directorio de entrada con archivos `.prg`      |
| `-o, --output <dir>`    | Directorio de salida para docs generadas       |
| `-f, --format <fmt>`    | Formato de salida (repetible)                  |
| `--strict`              | Trata advertencias como errores                |
| `-V, --verbose`         | Activa registro detallado                      |
| `--no-highlight`        | Desactiva resaltado de sintaxis                |

### Alias de formatos

| Alias      | Canónico   | Archivo de salida |
|------------|------------|-------------------|
| `html`     | `html`     | `index.html`      |
| `htm`      | `html`     | `index.html`      |
| `md`       | `markdown` | `index.md`        |
| `markdown` | `markdown` | `index.md`        |
| `txt`      | `text`     | `index.txt`       |
| `text`     | `text`     | `index.txt`       |
| `json`     | `json`     | `index.json`      |

---

## ✍️ Documentar tu Código

### Etiquetas soportadas

| Etiqueta       | Propósito                                     |
|----------------|-----------------------------------------------|
| `@param`       | Documenta un parámetro de función/método      |
| `@return`      | Documenta el valor de retorno                 |
| `@since`       | Versión en la que se añadió el símbolo        |
| `@see`         | Referencia cruzada a otro símbolo             |
| `@example`     | Ejemplo de uso (multilínea, auto-acumulado)   |
| `@deprecated`  | Marca el símbolo como obsoleto                |
| `@internal`    | Marca el símbolo como interno                 |

### Códigos de tipo

| Código | Significado      | Código | Significado   |
|--------|------------------|--------|---------------|
| `C`    | Carácter         | `H`    | Hash          |
| `N`    | Numérico         | `O`    | Objeto        |
| `L`    | Lógico           | `X`    | Cualquiera    |
| `D`    | Fecha            | `NIL`  | Sin valor     |
| `A`    | Arreglo          |        |               |

### Cobertura de símbolos

| Símbolo    | Detectado por                   |
|------------|---------------------------------|
| Función    | `FUNCTION`                      |
| Procedimiento | `PROCEDURE`                  |
| Clase      | `CLASS` (con `FROM`)            |
| Método     | `METHOD`                        |
| Constante  | `#define`                       |

### Ejemplo: Función

```harbour
/**
 * Convierte un valor numérico a una cadena de moneda formateada.
 * @param nAmount (N) -> Cantidad a formatear
 * @param cSymbol (C) -> Símbolo de moneda (predeterminado: "$")
 * @return (C) -> Cadena de moneda formateada
 * @since 1.2.0
 * @example
 *   cResult := FormatCurrency( 1234.56 )
 *   // Resultado: "$1,234.56"
 */
FUNCTION FormatCurrency( nAmount, cSymbol )
```

### Ejemplo: Clase

```harbour
/**
 * Representa una entidad cliente con validación.
 * @since 2.0.0
 * @see Order
 * @example
 *   oCustomer := Customer():New( "John Doe" )
 */
CLASS Customer FROM Entity
```

### Ejemplo: Constante

```harbour
/**
 * Longitud máxima permitida del nombre en caracteres.
 * @since 1.0.0
 */
#define MAX_NAME_LENGTH 60
```

---

## 📦 Formatos de Salida

### HTML (predeterminado)

```bash
harbour-doc generate -i src -o docs -f html
```

Produce un `index.html` **autónomo y autocontenido** con búsqueda, filtros, modo oscuro y tarjetas plegables.

### Markdown

```bash
harbour-doc generate -i src -o docs -f md
```

Produce un `index.md` limpio con tabla de contenidos, bloques de código y referencias cruzadas.

### JSON

```bash
harbour-doc generate -i src -o docs -f json
```

Produce un `index.json` legible por máquina con el AST completo.

### Texto

```bash
harbour-doc generate -i src -o docs -f txt
```

Produce un `index.txt` en texto plano para terminal o consumo heredado.

### Todos los formatos a la vez ⭐

```bash
harbour-doc generate -i src -o docs -f html -f md -f json -f txt
```

Genera **los cuatro artefactos** en una sola ejecución.

---

## ⚙️ Archivo de Configuración

harbour-doc lee un archivo opcional `harbour-doc.ini` del directorio actual.

### Ejemplo `harbour-doc.ini`

```ini
[general]
input_dir  = src
output_dir = docs
format     = html,md,json
lang       = ES
strict     = false
verbose    = false

[logging]
logfile = harbour-doc.log

[validation]
max_file_mb = 50
```

### Precedencia

```
Banderas CLI  >  Archivo INI  >  valores predeterminados
```

---

## 🎨 Características del HTML

| Característica           | Descripción                                    |
|--------------------------|------------------------------------------------|
| 🌙 Modo oscuro           | Persistente vía `localStorage`                 |
| 🔍 Búsqueda en vivo      | Filtro instantáneo por nombre o contenido      |
| 🎛️ Botones de filtro    | Filtra por función, clase, método, constante   |
| 📂 Tarjetas plegables    | Clic en encabezados para expandir/colapsar     |
| 📋 Copiar al portapapeles| Copia de ejemplos con un clic                  |
| 🔗 Enlaces cruzados      | Etiquetas `@see` se vuelven anclas clicables   |
| 📱 Diseño responsivo     | Funciona en escritorio, tablet y móvil         |
| 🖨️ Amigable para imprimir| Colapsa filtros al imprimir                    |
| 📊 Panel de estadísticas | Conteo de símbolos por tipo                    |

---

## ✅ Validación y Modo Estricto

### Solo validar (sin salida)

```bash
harbour-doc validate -i src
```

### Modo estricto

```bash
harbour-doc validate -i src --strict
```

En modo estricto, **las advertencias cuentan como errores**. La compilación falla si alguna API pública no está documentada.

### Reglas de validación

| Regla            | Severidad | Descripción                          |
|------------------|-----------|--------------------------------------|
| `documented`     | error     | Todo símbolo debe tener un resumen   |
| `params_match`   | warning   | Funciones con parámetros necesitan `@param` |
| `return_doc`     | warning   | Funciones necesitan `@return`        |
| `valid_types`    | error     | Los códigos de tipo deben ser válidos |

---

## 🎓 Galería de Ejemplos

### Ejemplo 1: Un archivo, salida HTML

```bash
harbour-doc generate -i src -o docs -f html
```

### Ejemplo 2: Todos los formatos

```bash
harbour-doc generate -i src -o docs -f html -f md -f json -f txt
```

### Ejemplo 3: Validación estricta

```bash
harbour-doc validate -i src --strict
```

### Ejemplo 4: Inspeccionar tokens por archivo

```bash
harbour-doc scan -i src
```

### Ejemplo 5: Registro detallado

```bash
harbour-doc generate -i src -o docs -f html -V
```

### Ejemplo 6: Entrada/salida personalizada

```bash
harbour-doc generate -i C:\Proyectos\MiApp\src -o C:\Docs\MiApp -f html
```

---

## 🛠️ Solución de Problemas

### Problema: `err.no.files.found`

**Causa:** No hay archivos `.prg` en el directorio de entrada.

**Solución:** Verifica la ruta `-i`. Asegúrate de que contiene archivos `.prg`.

```bash
harbour-doc scan -i src
```

### Problema: `err.invalid.format`

**Causa:** Nombre de formato no soportado.

**Solución:** Usa un alias válido: `html`, `md`, `json`, `txt`.

### Problema: No se extraen símbolos

**Causa:** Los comentarios no están en formato `/** … */`.

**Solución:** Asegúrate de que los bloques de documentación comiencen con `/**` (no `/*` ni `//`).

### Problema: El archivo HTML está vacío

**Causa:** No se encontraron símbolos documentados.

**Solución:** Ejecuta `harbour-doc scan -i src` para verificar tokens.

### Problema: `--strict` hace fallar la compilación

**Causa:** Algunos símbolos no tienen documentación.

**Solución:** Ejecuta primero sin `--strict`, luego revisa el reporte:

```bash
harbour-doc validate -i src
```
---
