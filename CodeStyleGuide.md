# C# Code Style Guide

This document defines the organization of a .NET solution on disk and the formatting and style conventions for its C# source files. The conventions are enforced by the `.editorconfig` file at the repository root.

## 1. Solution and Folder Structure

The repository root holds two physical folders that contain projects: `src` and `test`. Any other grouping visible in the Solution Explorer is a **solution folder**, a virtual grouping recorded in the `.sln` file that has no corresponding path on disk.

Each immediate child of `src` is a project folder holding the `.csproj` file and the source files of that project. The same rule applies to `test`: each immediate child is a single test project. A project is never nested under an intermediate grouping folder, such as `src/ProjectName/ProjectName.Api`, even when the repository contains a single microservice.

```
├── src/
│   ├── ProjectName.Api/
│   ├── ProjectName.Application/
│   ├── ProjectName.Domain/
│   └── ProjectName.Infrastructure/
├── test/
│   ├── ProjectName.Api.Tests/
│   └── ProjectName.Application.Tests/
├── ProjectName.sln
├── .editorconfig
└── CODE_STYLE_GUIDE.md
```

Solution-level files that belong to no project reside at the repository root. These files include the `.editorconfig`, the solution file, build scripts, and `.gitignore`. The IDE surfaces them under a `Solution Items` solution folder.

## 2. File-Level Conventions

| Setting | Value |
|---------|-------|
| Encoding | `utf-8` |
| Indentation | Spaces, never tabs |
| Indent width | 4 |
| Trailing whitespace | Removed from every line |
| Final newline | Exactly one |

## 3. Namespaces and Using Directives

Every file declares a **file-scoped namespace**, the C# 10 syntax that terminates the declaration with a semicolon instead of opening a block. The block form (`namespace Foo { ... }`), which nests every type one indent level deeper, does not appear in the codebase.

```csharp
namespace ProjectName.Api.Services;

public class QuoteService
{
    // ...
}
```

A single blank line separates the `using` directives from the namespace declaration:

```csharp
using ProjectName.Api.Models;

namespace ProjectName.Api.Services;

public class QuoteService
{
    // ...
}
```

The namespace of a file matches the folder path of the file. A file scaffolded with a non-matching namespace is corrected to reflect its actual location before the file is committed.

The `using` directives are sorted with `System.*` namespaces first, followed by all other namespaces in a single, unbroken alphabetical group. No blank line separates the two groups. Directives for namespaces that can be omitted, such as implicit usings, are not declared.

## 4. Naming Conventions

| Symbol | Style | Prefix | Example |
|--------|-------|--------|---------|
| Types, methods, properties, events | PascalCase | None | `QuoteService`, `GetQuote` |
| Primary constructor properties | PascalCase | None | `Repository` |
| Non-private static fields | PascalCase | None | `DefaultTimeout` |
| Non-private readonly fields | PascalCase | None | `Instance` |
| Constants (`const` fields and locals) | PascalCase | None | `MaxLineItems` |
| Private static fields | camelCase | `s_` | `s_instanceCount` |
| Private instance fields | camelCase | `_` | `_repository` |
| Local functions | PascalCase | None | `ApplyDiscount` |
| Locals and parameters | camelCase | None | `subtotal` |

```csharp
public class QuoteCalculator
{
    public const int MaxLineItems = 50;
    public static readonly TimeSpan DefaultTimeout = TimeSpan.FromSeconds(30);

    private static int s_instanceCount;
    private readonly IQuoteRepository _repository;

    public decimal Calculate(Quote quote)
    {
        var subtotal = 0m;

        decimal ApplyDiscount(decimal amount) => amount * 0.9m;

        return subtotal;
    }
}
```

Accessibility modifiers are stated explicitly on every member. No member falls back on its default accessibility.

## 5. Language Feature Preferences

### 5.1 Local Variable Types

The `var` keyword declares every local variable, regardless of whether the type is built-in, apparent from the right-hand side, or inferred from elsewhere in the expression.

```csharp
var count = 0;
var quote = new Quote();
var items = repository.GetLineItems(quoteId);
```

### 5.2 Pattern Matching and Related Constructs

Pattern matching replaces an `is` check followed by a cast and an `as` check followed by a null check.

```csharp
if (segment is FlightSegment flight)
{
    // preferred over: if (segment is FlightSegment) { var flight = (FlightSegment)segment; }
}
```

| Construct | Preference |
|-----------|------------|
| `out` variables | Declared inline at the point of use |
| `throw` | A `throw` expression replaces a `throw` statement wherever the surrounding expression allows |
| Delegate invocation | `handler?.Invoke(...)` replaces a preceding null check |

### 5.3 Expression Bodies

Methods, constructors, and operators always take a full block body, regardless of the length of the body. Properties, indexers, and accessors may use an expression body when the implementation fits on a single line.

```csharp
public decimal Total
{
    get => _lineItems.Sum(item => item.Amount);
}

public decimal GetDiscountedTotal(decimal rate)
{
    return Total * (1 - rate);
}
```

### 5.4 Lambda Expressions

A lambda expression carries a single space on both sides of the `=>` operator, whether the body is an expression or a block.

```csharp
var names = travelers.Select(p => p.SomeProperty);
var flights = segments.Where(s => s.IsInternational).ToList();
```

## 6. Spacing

| Context | Rule | Example |
|---------|------|---------|
| Cast | No space after the cast | `(int)value` |
| Comma | Space after, none before | `Foo(a, b, c)` |
| Member access (dot) | No space before or after | `traveler.FirstName` |
| Control-flow keyword | Space after `if`, `for`, `while`, `foreach`, `switch`, `catch` | `if (isValid)` |
| `for` statement semicolons | Space after, none before | `for (var i = 0; i < n; i++)` |
| Binary operators | Space before and after | `a + b`, `x == y` |
| Inheritance colon | Space before and after | `class Quote : Entity` |
| Method call or declaration parentheses | No space before `(`, none inside | `Calculate(quote)` |
| Empty parameter list | No space between the parentheses | `GetTotal()` |
| Indexer brackets | No space before `[`, none inside | `items[0]` |
| Empty square brackets | No space between them | `new int[]` |
| Grouping parentheses | No space inside | `(a + b) * c` |

## 7. Line Breaks and Braces

The opening brace of every construct starts on its own line. The constructs include types, methods, properties, `if` and `else`, `try`, `catch`, and `finally`, loops, and object and collection initializers.

```csharp
if (quote.IsValid)
{
    Process(quote);
}
else
{
    Reject(quote);
}
```

The keywords `else`, `catch`, and `finally` each start on a new line after the closing brace of the preceding block. Members of an object initializer or an anonymous type, and the clauses of a LINQ query expression, each start on a new line as well.

Braces are always used, including for a single-statement `if`, `for`, or `while` body. A brace-less single-line conditional does not appear in the codebase.

## 8. Indentation

Block contents, `switch` case contents, and `switch` case labels are indented one level relative to the enclosing construct. An opening or closing brace aligns with the line that introduces the block.

```csharp
switch (status)
{
    case QuoteStatus.Draft:
        Process();
        break;
    default:
        throw new InvalidOperationException();
}
```

A `goto` label is placed flush against the left margin, regardless of the indentation of the surrounding block.

## 9. XML Documentation

A public type or member carries an XML documentation comment when the purpose, the parameters, or the return value are not fully evident from the name and signature. The `<summary>` element states what the member represents or does. The `<param>` and `<returns>` elements are added when the meaning of a parameter or a return value is not self-explanatory.

```csharp
/// <summary>
/// Calculates the total price of a quote after applying the traveler's loyalty discount.
/// </summary>
/// <param name="quote">The quote whose line items are summed.</param>
/// <param name="loyaltyTier">The traveler's loyalty tier, used to select the discount rate.</param>
/// <returns>The discounted total, in the quote's base currency.</returns>
public decimal CalculateDiscountedTotal(Quote quote, LoyaltyTier loyaltyTier)
```

A private member, a self-explanatory property, and a method whose name and parameter list convey its behavior carry no documentation comment. A comment that restates the member name in prose, such as `/// <summary>Gets the quote.</summary>` on a method named `GetQuote()`, adds no information beyond the signature and is not written.
