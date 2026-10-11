# 📘 harbour-doc

> **A modern, zero-dependency documentation generator for the Harbour language.**

[![Version](https://img.shields.io/badge/version-3.2.0-blue.svg)](https://github.com/badasystem/harbour-doc)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Harbour](https://img.shields.io/badge/Harbour-3.2.0dev-orange.svg)](https://harbour.github.io/)
[![MiniGUI](https://img.shields.io/badge/MiniGUI-Extended-green.svg)](http://www.hmgforum.com/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)]()

---

## 🎯 What is harbour-doc?

**harbour-doc** reads your Harbour `.prg` source files, extracts structured
documentation from `/** … */` comment blocks, and produces beautiful,
searchable API documentation in **HTML**, **Markdown**, **JSON**, or plain
**Text**.

Think **JSDoc** for JavaScript, **Doxygen** for C++, **PHPDoc** for PHP — but
designed natively for the Harbour ecosystem.

> ✨ **One standard. One tool. Complete documentation.**

---

## 💡 Why harbour-doc?

### 😤 The problem

Harbour projects typically ship with:
- 📄 Scattered comments with no consistent format
- 🔍 No searchable API reference for the team
- 🧩 No way to validate that public functions are documented
- 🌍 Docs locked to one language or one output format

### 🚀 The solution

| Benefit | What it means for you |
|---------|------------------------|
| 📐 **One standard** | Every symbol documented the same way, by every developer |
| 🔎 **Searchable HTML** | Interactive search, type filters, dark mode, print support |
| 📦 **Multi-format** | Generate HTML + Markdown + JSON + Text in a single run |
| ✅ **Built-in validation** | Fail the build when public APIs are undocumented |
| 🌐 **Internationalization** | English and Spanish UI out of the box |
| 🔗 **Cross-references** | `@see` tags become clickable anchors automatically |
| 🧠 **OOP-aware** | Understands `CLASS`, `FROM`, `METHOD`, inheritance trees |
| 🎯 **Zero dependencies** | No external libraries, no database, no runtime |
| ⚡ **Fast** | Tokenizes, extracts, validates, and renders in milliseconds |
| 🖥️ **Multi-output** | Single binary, one command, four artifacts |

---

## ⚡ Quick Start

### 1️⃣ Install

Clone and build with Harbour 3.2.0dev + MiniGUI Extended:

```bash
git clone https://github.com/BadaSystem/harbour-doc.git
cd harbour-doc
```

### 2️⃣ Document your code

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

### 3️⃣ Generate documentation

```bash
harbour-doc generate -i src -o docs -f html
```

Open `docs/index.html` in your browser. 🎉

---

## 🖥️ Command-Line Interface

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

| Alias      | Canonical  | Output        |
|------------|------------|---------------|
| `html`     | `html`     | `index.html`  |
| `htm`      | `html`     | `index.html`  |
| `md`       | `markdown` | `index.md`    |
| `markdown` | `markdown` | `index.md`    |
| `txt`      | `text`     | `index.txt`   |
| `text`     | `text`     | `index.txt`   |
| `json`     | `json`     | `index.json`  |

### Examples

```bash
# 📄 Single format
harbour-doc generate -i src -o docs -f html

# 📦 All formats at once
harbour-doc generate -i src -o docs -f html -f md -f json -f txt

# ✅ Strict validation only
harbour-doc validate -i src --strict

# 🔍 Inspect token counts per file
harbour-doc scan -i src
```

---

## 🎨 HTML Output Features

The HTML backend produces a **standalone, self-contained** page — no server,
no build step, no network access required.

| Feature                 | Emoji | Description                                    |
|-------------------------|-------|------------------------------------------------|
| Dark mode toggle        | 🌙    | Persistent via `localStorage`                  |
| Live search             | 🔍    | Instant filter by name or content              |
| Type filter buttons     | 🎛️    | Filter by function, class, method, constant    |
| Collapsible cards       | 📂    | Click headers to expand/collapse               |
| Copy-to-clipboard       | 📋    | One-click copy of code examples                |
| Cross-reference links   | 🔗    | `@see` tags become clickable anchors           |
| Responsive layout       | 📱    | Works on desktop, tablet, and mobile           |
| Print-friendly          | 🖨️    | Collapses filters and controls when printing   |
| Custom scrollbar        | 🎚️    | Brand-consistent visual experience             |
| Statistics dashboard    | 📊    | Symbol counts by type at a glance              |

---

## 📚 Documentation Standard

**harbour-doc** defines a documentation standard based on familiar
conventions from JSDoc, Doxygen, and PHPDoc.

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

📖 **Read the complete standard:** [`docs/STANDARD.md`](docs/STANDARD.md)

---

## 🏗️ Architecture

```
┌───────────────┐     ┌──────────────┐     ┌─────────────────┐
│   Source      │────▶│    Lexer     │────▶│   Extractor     │
│  (.prg files) │     │ (tokenize)   │     │ (associate doc) │
└───────────────┘     └──────────────┘     └────────┬────────┘
                                                     │
                                            ┌────────▼────────┐
                                            │       AST       │
                                            │ (symbols, tags) │
                                            └────────┬────────┘
                                                     │
                       ┌─────────────────────────────┼─────────────────────────────┐
                       │                             │                             │
              ┌────────▼────────┐          ┌────────▼────────┐          ┌────────▼────────┐
              │   Validator     │          │  Backend        │          │  Backend        │
              │ (rules engine)  │          │  HTML           │          │  Markdown       │
              └─────────────────┘          └─────────────────┘          └─────────────────┘
                                                     │                             │
                                            ┌────────▼────────┐          ┌────────▼────────┐
                                            │  Backend        │          │  Backend        │
                                            │  JSON           │          │  Text           │
                                            └─────────────────┘          └─────────────────┘
```

**Zero external dependencies.** Every parser, encoder, and renderer is
implemented natively in Harbour using `hbclass.ch`.

---

## 🧪 Validation Rules

The built-in validator catches documentation gaps before they reach
production.

| Rule              | Severity | Checks                                        |
|-------------------|----------|-----------------------------------------------|
| `documented`      | ❌ error  | Every public symbol has a brief description   |
| `params_match`    | ⚠️ warning| Parameters have `@param` tags                 |
| `return_doc`      | ⚠️ warning| Functions have `@return` tags                 |
| `valid_types`     | ❌ error  | Type codes are one of `C N L D A H O X`       |

Run with `--strict` to make warnings fail the build:

```bash
harbour-doc generate -i src -o docs -f html --strict
```

---

## 🌐 Internationalization

The CLI itself is available in **English** and **Spanish**:

```bash
# Force a language (falls back to English)
harbour-doc generate -i src -o docs -f html -l ES
```

Language files live in `i18n/`:

```
i18n/
├── en.json    # English dictionary (86 keys)
└── es.json    # Spanish dictionary (86 keys)
```

Adding a new language is as simple as copying `en.json` and translating the
values — no code changes required.

---

## 🙏 Credits

- **Author:** Marcos Jarrín — [@marvijarrin](mailto:marvijarrin@gmail.com)
- **Organization:** [BadaSystem](https://badasystem.com)
- **Built with:** [Harbour 3.2.0dev](https://harbour.github.io/) +
  [MiniGUI Extended](http://www.hmgforum.com/)
- **Inspired by:** JSDoc, Doxygen, PHPDoc

---

## 🌟 Show your support

If **harbour-doc** helps your project:

- ⭐ Star the repository
- 🐛 Report bugs and request features
- 📝 Improve the documentation standard
- 🌍 Translate the CLI to your language

---

<p align="center">
  <strong>📘 harbour-doc</strong><br>
  <em>Documentation that scales with your Harbour project.</em>
</p>

---

# README.es.md (Spanish)

# 📘 harbour-doc

> **Un generador de documentación moderno y sin dependencias para el lenguaje Harbour.**

[![Versión](https://img.shields.io/badge/versi%C3%B3n-3.2.0-blue.svg)](https://github.com/badasystem/harbour-doc)
[![Licencia: MIT](https://img.shields.io/badge/Licencia-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Harbour](https://img.shields.io/badge/Harbour-3.2.0dev-orange.svg)](https://harbour.github.io/)
[![MiniGUI](https://img.shields.io/badge/MiniGUI-Extended-green.svg)](http://www.hmgforum.com/)
[![Plataforma](https://img.shields.io/badge/plataforma-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)]()

---

## 🎯 ¿Qué es harbour-doc?

**harbour-doc** lee tus archivos fuente Harbour `.prg`, extrae la
documentación estructurada de los bloques de comentario `/** … */` y produce
documentación de API hermosa y consultable en **HTML**, **Markdown**,
**JSON** o **Texto** plano.

Piensa en **JSDoc** para JavaScript, **Doxygen** para C++, **PHPDoc** para PHP —
pero diseñado nativamente para el ecosistema Harbour.

> ✨ **Un estándar. Una herramienta. Documentación completa.**

---

## 💡 ¿Por qué harbour-doc?

### 😤 El problema

Los proyectos Harbour típicamente se entregan con:
- 📄 Comentarios dispersos sin un formato consistente
- 🔍 Sin referencia de API consultable para el equipo
- 🧩 Sin forma de validar que las funciones públicas estén documentadas
- 🌍 Documentación limitada a un idioma o un formato de salida

### 🚀 La solución

| Beneficio | Qué significa para ti |
|-----------|------------------------|
| 📐 **Un solo estándar** | Cada símbolo documentado igual, por cada desarrollador |
| 🔎 **HTML consultable** | Búsqueda interactiva, filtros por tipo, modo oscuro, soporte de impresión |
| 📦 **Multi-formato** | Genera HTML + Markdown + JSON + Texto en una sola ejecución |
| ✅ **Validación integrada** | Falla el build cuando las APIs públicas no están documentadas |
| 🌐 **Internacionalización** | Interfaz en inglés y español lista para usar |
| 🔗 **Referencias cruzadas** | Las etiquetas `@see` se convierten en anclas clicables automáticamente |
| 🧠 **Consciente de OOP** | Entiende `CLASS`, `FROM`, `METHOD`, árboles de herencia |
| 🎯 **Cero dependencias** | Sin librerías externas, sin base de datos, sin runtime |
| ⚡ **Rápido** | Tokeniza, extrae, valida y renderiza en milisegundos |
| 🖥️ **Multi-salida** | Un binario, un comando, cuatro artefactos |

---

## ⚡ Inicio Rápido

### 1️⃣ Instalar

Clona y compila con Harbour 3.2.0dev + MiniGUI Extended:

```bash
git clone https://github.com/BadaSystem/harbour-doc.git
cd harbour-doc
```

### 2️⃣ Documenta tu código

Añade un bloque `/** … */` encima de cada símbolo público:

```harbour
/**
 * Calcula el precio total incluyendo impuesto y descuento.
 * @param nPrice    (N) -> Precio unitario base antes de impuesto
 * @param nQuantity (N) -> Número de unidades a calcular
 * @param nTaxRate  (N) -> Porcentaje de impuesto a aplicar (0-100)
 * @param nDiscount (N) -> Monto de descuento plano a restar
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

### 3️⃣ Genera la documentación

```bash
harbour-doc generate -i src -o docs -f html
```

Abre `docs/index.html` en tu navegador. 🎉

---

## 🖥️ Interfaz de Línea de Comandos

```bash
harbour-doc <comando> [opciones]
```

### Comandos

| Comando    | Propósito                                              |
|------------|--------------------------------------------------------|
| `generate` | 🏗️ Escanear fuente, extraer símbolos, generar salida    |
| `validate` | ✅ Validar calidad de documentación sin generar salida  |
| `scan`     | 🔍 Tokenizar fuente y reportar conteos de símbolos     |
| `help`     | ❓ Mostrar ayuda                                        |
| `version`  | ℹ️ Mostrar información de versión                       |

### Opciones

| Bandera                 | Descripción                                    |
|-------------------------|------------------------------------------------|
| `-i, --input <dir>`     | Directorio de entrada con archivos `.prg`      |
| `-o, --output <dir>`    | Directorio de salida para docs generadas       |
| `-f, --format <fmt>`    | Formato de salida (repetible)                  |
| `--strict`              | Tratar advertencias como errores               |
| `-V, --verbose`         | Habilitar logging detallado                    |
| `--no-highlight`        | Deshabilitar resaltado de sintaxis             |

### Alias de formato

| Alias      | Canónico   | Salida        |
|------------|------------|---------------|
| `html`     | `html`     | `index.html`  |
| `htm`      | `html`     | `index.html`  |
| `md`       | `markdown` | `index.md`    |
| `markdown` | `markdown` | `index.md`    |
| `txt`      | `text`     | `index.txt`   |
| `text`     | `text`     | `index.txt`   |
| `json`     | `json`     | `index.json`  |

### Ejemplos

```bash
# 📄 Un solo formato
harbour-doc generate -i src -o docs -f html

# 📦 Todos los formatos a la vez
harbour-doc generate -i src -o docs -f html -f md -f json -f txt

# ✅ Solo validación estricta
harbour-doc validate -i src --strict

# 🔍 Inspeccionar conteo de tokens por archivo
harbour-doc scan -i src
```

---

## 🎨 Características de la Salida HTML

El backend HTML produce una página **autónoma y autocontenida** — sin
servidor, sin paso de compilación, sin acceso a la red.

| Característica              | Emoji | Descripción                                    |
|-----------------------------|-------|------------------------------------------------|
| Modo oscuro                 | 🌙    | Persistente vía `localStorage`                 |
| Búsqueda en vivo            | 🔍    | Filtro instantáneo por nombre o contenido      |
| Botones de filtro por tipo  | 🎛️    | Filtrar por función, clase, método, constante  |
| Tarjetas colapsables        | 📂    | Clic en encabezados para expandir/colapsar     |
| Copiar al portapapeles      | 📋    | Copia de ejemplos con un solo clic             |
| Enlaces de referencia       | 🔗    | Las etiquetas `@see` se vuelven anclas         |
| Diseño responsivo           | 📱    | Funciona en escritorio, tablet y móvil         |
| Amigable para imprimir      | 🖨️    | Colapsa filtros y controles al imprimir        |
| Barra de desplazamiento     | 🎚️    | Experiencia visual consistente con la marca    |
| Panel de estadísticas       | 📊    | Conteos de símbolos por tipo de un vistazo     |

---

## 📚 Estándar de Documentación

**harbour-doc** define un estándar de documentación basado en convenciones
familiares de JSDoc, Doxygen y PHPDoc.

### Etiquetas soportadas

| Etiqueta       | Propósito                                      |
|----------------|------------------------------------------------|
| `@param`       | Documenta un parámetro de función/método       |
| `@return`      | Documenta el valor de retorno                  |
| `@since`       | Versión en la que se añadió el símbolo         |
| `@see`         | Referencia cruzada a otro símbolo              |
| `@example`     | Ejemplo de uso (multilínea, auto-acumulado)    |
| `@deprecated`  | Marca el símbolo como obsoleto                 |
| `@internal`    | Marca el símbolo como interno                  |

### Códigos de tipo

| Código | Significado      | Código | Significado   |
|--------|------------------|--------|---------------|
| `C`    | Carácter         | `H`    | Hash          |
| `N`    | Numérico         | `O`    | Objeto        |
| `L`    | Lógico           | `X`    | Cualquiera    |
| `D`    | Fecha            | `NIL`  | Sin valor     |
| `A`    | Arreglo          |        |               |

### Cobertura de símbolos

| Símbolo        | Detectado por                   |
|----------------|---------------------------------|
| Función        | `FUNCTION`                      |
| Procedimiento  | `PROCEDURE`                     |
| Clase          | `CLASS` (con `FROM`)            |
| Método         | `METHOD`                        |
| Constante      | `#define`                       |

📖 **Lee el estándar completo:** [`docs/STANDARD.es.md`](docs/STANDARD.es.md)

---

## 🏗️ Arquitectura

```
┌───────────────┐     ┌──────────────┐     ┌─────────────────┐
│    Fuente     │────▶│    Lexer     │────▶│   Extractor     │
│  (archivos)   │     │ (tokeniza)   │     │ (asocia doc)    │
└───────────────┘     └──────────────┘     └────────┬────────┘
                                                     │
                                            ┌────────▼────────┐
                                            │       AST       │
                                            │ (símbolos,tags) │
                                            └────────┬────────┘
                                                     │
                       ┌─────────────────────────────┼─────────────────────────────┐
                       │                             │                             │
              ┌────────▼────────┐          ┌────────▼────────┐          ┌────────▼────────┐
              │   Validador     │          │  Backend        │          │  Backend        │
              │ (motor reglas)  │          │  HTML           │          │  Markdown       │
              └─────────────────┘          └─────────────────┘          └─────────────────┘
                                                     │                             │
                                            ┌────────▼────────┐          ┌────────▼────────┐
                                            │  Backend        │          │  Backend        │
                                            │  JSON           │          │  Texto          │
                                            └─────────────────┘          └─────────────────┘
```

**Cero dependencias externas.** Cada parser, codificador y renderizador está
implementado nativamente en Harbour usando `hbclass.ch`.

---

## 🧪 Reglas de Validación

El validador integrado detecta vacíos de documentación antes de que lleguen a
producción.

| Regla             | Severidad | Verifica                                       |
|-------------------|-----------|------------------------------------------------|
| `documented`      | ❌ error   | Cada símbolo público tiene descripción breve   |
| `params_match`    | ⚠️ aviso   | Los parámetros tienen etiquetas `@param`       |
| `return_doc`      | ⚠️ aviso   | Las funciones tienen etiquetas `@return`       |
| `valid_types`     | ❌ error   | Los códigos de tipo son uno de `C N L D A H O X` |

Ejecuta con `--strict` para que las advertencias fallen el build:

```bash
harbour-doc generate -i src -o docs -f html --strict
```

---

## 🌐 Internacionalización

La CLI misma está disponible en **inglés** y **español**:

```bash
# Forzar un idioma (recurre a inglés si no se encuentra)
harbour-doc generate -i src -o docs -f html -l ES
```

Los archivos de idioma viven en `i18n/`:

```
i18n/
├── en.json    # Diccionario inglés (86 claves)
└── es.json    # Diccionario español (86 claves)
```

Añadir un nuevo idioma es tan simple como copiar `en.json` y traducir los
valores — sin cambios de código requeridos.

---

## 🙏 Créditos

- **Autor:** Marcos Jarrín — [@marvijarrin](mailto:marvijarrin@gmail.com)
- **Organización:** [BadaSystem](https://badasystem.com)
- **Construido con:** [Harbour 3.2.0dev](https://harbour.github.io/) +
  [MiniGUI Extended](http://www.hmgforum.com/)
- **Inspirado por:** JSDoc, Doxygen, PHPDoc

---

## 🌟 Muestra tu apoyo

Si **harbour-doc** ayuda a tu proyecto:

- ⭐ Dale estrella al repositorio
- 🐛 Reporta bugs y solicita funcionalidades
- 📝 Mejora el estándar de documentación
- 🌍 Traduce la CLI a tu idioma

---

<p align="center">
  <strong>📘 harbour-doc</strong><br>
  <em>Documentación que escala con tu proyecto Harbour.</em>
</p>

---

**Convenciones de GitHub:**

- `README.md` es el archivo que GitHub muestra por defecto → debe ser el inglés.

**Emojis clave utilizados (consistentes en ambos idiomas):**

| Emoji | Uso                                              |
|-------|--------------------------------------------------|
| 📘    | Logo/nombre del proyecto                         |
| 🎯    | Sección "¿Qué es?" / "What is?"                  |
| 💡    | Sección de justificación / beneficios            |
| ⚡    | Inicio rápido                                    |
| 🖥️    | Interfaz de línea de comandos                    |
| 🎨    | Características del HTML                         |
| 📚    | Estándar de documentación                        |
| 🏗️    | Arquitectura                                     |
| 🧪    | Validación / testing                             |
| 🌐    | Internacionalización                             |
| 📂    | Estructura del proyecto                          |
| 🤝    | Contribuir                                       |
| 📜    | Licencia                                         |
| 🙏    | Créditos                                         |
| 🌟    | Apoyo                                            |
| ✅❌⚠️  | Estados de validación (error/advertencia/éxito)  |
| 🔍🔎  | Búsqueda y filtros                               |
| 🌙📋🔗📱🖨️ | Características específicas del HTML        |