# OhtohsEditorConfig

My personal `.editorconfig` for .NET solutions.

The file is meant to work across .NET versions, including older ones, which is why it stays away from rules that only make sense on a recent compiler. It doesn't force file-scoped namespaces, it doesn't flag namespaces or `using` directives that could be omitted. Both are still the preferred approach for newer projects, and [CodeStyleGuide.md](CodeStyleGuide.md) describes them as the way to write code.

## Language Style

These are the `dotnet_style_*` and `csharp_style_*` options. Most are suggestions, so they show up as hints in the editor rather than build warnings.

- No `this.` or `Me.` unless it's actually needed.
- Language keywords over framework type names, so `int` instead of `Int32`.
- `var` everywhere, whether or not the type is obvious from the right-hand side.
- Object and collection initializers, coalesce expressions, null propagation, and explicit tuple names when they fit.
- Pattern matching instead of an `is` check with a cast, or an `as` check followed by a null check.
- Full block bodies for methods, constructors, and operators. Properties, indexers, and accessors can use expression bodies.
- `System.*` usings first, with no blank line between groups.

## Naming Rules

Each naming rule ties a kind of symbol to a naming style. They're evaluated in order, and the first match wins.

| Symbol | Style | Example |
|--------|-------|---------|
| Constants | PascalCase | `MaxRetryCount` |
| Non-private static fields | PascalCase | `DefaultTimeout` |
| Non-private readonly fields | PascalCase | `Instance` |
| Static fields | camelCase with an `s_` prefix | `s_cache` |
| Instance fields | camelCase with an `_` prefix | `_logger` |
| Locals and parameters | camelCase | `orderId` |
| Local functions | PascalCase | `Validate` |
| Everything else | PascalCase | `GetOrder` |

## Formatting

The `csharp_new_line_*`, `csharp_indent_*`, and `csharp_space_*` options take care of layout. In practice that means this:

```csharp
if (isValid)
{
    switch (state)
    {
        case State.Open:
            Process();
            break;
    }
}
else
{
    Reject();
}
```

- Opening braces go on their own line, for every construct.
- `else`, `catch`, and `finally` start on a new line.
- Switch labels and their contents are indented.
- Single-line blocks and statements are left alone.
