# LLM-Memory-Architecture-LMA-

name="LLM Memory Architecture (LMA)"
version="1.0.1"
language="en"
    
A simple persistent memory semantic sintaxis for LLMs. Just upload to your chat interface or agent the LMA-Core-Specification-v1.0.1.md and the LMA-Memory-Template-v1.0.1.md and ask to your LLM to modify the LMA-Memory-Template-v1.0.1.md with your own rules based on the LMA-Core-Specification-v1.0.1.md principles. That´s all.

<br>

# LLM Memory Architecture (LMA) Core Specification v1.0.1

## 1. Purpose

LLM Memory Architecture (LMA) defines a portable structure for expressing persistent behavioral memory across language models.

The Core Specification defines the syntax, semantics, precedence model and conformance requirements used by an LMA Memory Template.

The Memory Template must remain understandable without requiring access to this specification.

## 2. Design principles

LMA follows these principles:

- Provider-agnostic.
- Self-contained memory.
- Minimal syntax.
- Minimal token and character overhead.
- Context efficiency.
- Explicit behavioral rules.
- Deterministic precedence.
- Persistent behavior only.
- No temporary conversation context.
- No unnecessary metadata or visual decoration.
- No duplicated behavioral responsibilities.
- One rule should control one coherent behavioral responsibility.
- Related behaviors may be consolidated when they share a common purpose, reduce persistent context cost and remain unambiguous.
- Specialized or infrequently used behavior should be moved outside persistent memory when it can be activated only when needed.
- Whitespace and physical line layout are non-semantic when element boundaries, category boundaries and instructions remain unambiguous.

## 3. Document structure

An LMA memory document consists of:

1. A root `<persistent_memory>` element.
2. A `<protocol>` block.
3. Behavioral rules grouped by semantic category.

Canonical structure:

```text
<persistent_memory ...>

<protocol>
...
</protocol>

# category

<rule ...>
...
</rule>

</persistent_memory>
```

Core category markers use simple Markdown headings: `# interaction`, `# accuracy`, `# verification`, `# format` and `# conflict`.

Decorative banners, repeated symbols and horizontal separators are non-semantic and should be omitted from persistent memory unless a host system requires them.

## 4. Root element

The root element is:

```xml
<persistent_memory
    name="LLM Memory Architecture (LMA)"
    version="1.0.1"
    language="en">
```

Required attributes:

- `name`: identifies the memory architecture.
- `version`: identifies the LMA Memory Template version.
- `language`: identifies the language used to express the rules.

No additional root attributes are required by LMA v1.0.x.

The expanded form above is recommended for the root element because it keeps document identity easy to inspect. An equivalent single-line serialization has the same semantics.

## 5. Protocol

The `<protocol>` block defines how the LLM must interpret and apply the memory document.

It must be self-contained.

It defines:

- the document as persistent behavioral memory;
- default application of all rules;
- temporary override by explicit user instructions;
- restoration of persistent rules after the current request;
- rule precedence;
- escalation to conflict handling when precedence cannot resolve a material conflict.

The protocol is part of the executable memory and must not depend on this Core Specification for interpretation.

Concise references to rule IDs are permitted when the referenced rule exists in the same Memory Template and the resulting behavior remains clear.

## 6. Rule syntax

Every behavioral rule uses the following structure:

```xml
<rule id="category.rule_name" priority="priority_level">
Rule instruction.
</rule>
```

The following expanded opening tag is semantically equivalent:

```xml
<rule
id="category.rule_name"
priority="priority_level">
```

Required attributes:

- `id`
- `priority`

Attribute order, indentation and line breaks do not change rule semantics. Opening and closing tags must remain identifiable and each instruction must remain associated with the correct rule.

LMA v1.0.x does not define `strength`, `scope`, `override`, `active` or other rule attributes.

## 7. Rule identifier

The `id` attribute uniquely identifies a rule.

Syntax:

```text
category.rule_name
```

Example:

```text
accuracy.no_invention
```

Requirements:

- The category prefix must correspond to the category containing the rule.
- IDs must be unique within the memory document.
- IDs should describe the behavioral responsibility controlled by the rule.
- IDs should remain stable unless the semantic responsibility of the rule changes.

## 8. Priority

`priority` defines precedence exclusively when two or more rules conflict.

It is not a general measure of importance.

LMA v1.0.x defines three levels:

```text
critical > high > normal
```

Semantics:

- `critical`: prevails over conflicting `high` and `normal` rules.
- `high`: prevails over conflicting `normal` rules and yields to `critical`.
- `normal`: default precedence level and yields to `high` and `critical`.

Priority has no effect when rules are compatible.

## 9. User override

An explicit instruction from the user may temporarily override any conflicting rule in the memory document.

The override:

- applies only to the current request;
- does not modify the persistent memory;
- expires after completion of the current request;
- does not remain active in subsequent requests unless explicitly requested again.

After the request is completed, all persistent rules resume normal application.

## 10. Conflict resolution

When two rules conflict:

1. Compare their `priority`.
2. The higher-priority rule prevails.
3. If both rules have the same priority and can coexist, apply both.
4. If both rules have the same priority and require mutually incompatible behavior that materially affects the requested output, invoke the rules in the `conflict` category.

A conflict must be material before conflict handling is invoked.

Differences in wording, emphasis or independent behavioral dimensions do not constitute a conflict.

## 11. Categories

LMA v1.0.x defines five core categories.

Each category is represented by a simple Markdown heading whose text matches the category prefix used by its rule IDs.

### 11.1. Interaction

Marker: `# interaction`

Controls how the LLM interacts with the user.

Typical responsibilities include:

- honoring explicit user instructions;
- requesting clarification;
- maintaining focus;
- response brevity;
- adapting depth.

### 11.2. Accuracy

Marker: `# accuracy`

Controls factual and logical reliability.

Typical responsibilities include:

- preventing fabrication;
- distinguishing certainty from inference;
- maintaining consistency.

### 11.3. Verification

Marker: `# verification`

Controls external information retrieval and evaluation.

Typical responsibilities include:

- determining when external research is required;
- selecting appropriate sources;
- assessing evidence;
- accurately reporting whether a source was accessed or verified.

### 11.4. Format

Marker: `# format`

Controls persistent presentation constraints and conditional formatting conventions for generated outputs.

Typical responsibilities may include:

- global presentation restrictions that apply to all outputs;
- compact Markdown conventions that apply only when a complete Markdown document or note is generated.

Format rules may contain multiple closely related conventions when they share the same output context and can be consolidated without ambiguity.

Conditions for applying a format behavior are expressed directly in the rule text.

LMA v1.0.x does not use a separate `scope` attribute.

Specialized formatting behavior that is only needed for particular workflows should be moved outside persistent memory when possible and activated only when required.

### 11.5. Conflict

Marker: `# conflict`

Defines fallback behavior when equal-priority rules require materially incompatible behavior.

This category must not duplicate accuracy, verification or interaction behavior.

Its purpose is limited to unresolved rule conflicts.

## 12. Rule design requirements

A rule should:

- define one coherent behavioral responsibility;
- use direct and unambiguous language;
- avoid relying on undefined terminology;
- avoid requiring access to the Core Specification;
- avoid unnecessary duplication with another rule;
- contain conditions in the rule text when necessary;
- remain provider-agnostic;
- justify its persistent context cost through frequent or broadly applicable value.

Related behaviors may be consolidated into one rule when:

- they share the same behavioral purpose or output context;
- their combined meaning remains clear and deterministic;
- consolidation reduces persistent token or character overhead;
- consolidation does not create materially different precedence requirements.

Rules should remain separate when they require different priorities, activate under materially different conditions or control unrelated behavioral responsibilities.

Semantic compression is permitted when it preserves every behaviorally relevant property of the original instruction, including:

- normative force: obligation, prohibition or permission;
- activation condition;
- scope;
- exceptions;
- priority and conflict behavior;
- observable behavioral result.

Compact punctuation, lists and rule-ID references may be used only when their interpretation remains unambiguous. Character reduction must not depend on omitted conditions or implicit behavior that a compatible model could interpret differently.

A rule should be removed from persistent memory when:

- it describes expected baseline model quality rather than a persistent preference;
- it duplicates another rule;
- it applies only to a temporary task;
- it does not materially affect model behavior;
- its persistent context cost is not justified by how frequently it is needed;
- it can be more efficiently activated through a specialized skill or equivalent on-demand mechanism.

## 13. Context and size efficiency

Persistent memory is included in the model context whenever the host environment applies it.

Every persistent character and instruction therefore contributes to ongoing context cost, although exact token usage depends on the model tokenizer.

LMA should minimize that cost by:

- keeping rules concise;
- removing redundant instructions;
- consolidating closely related behaviors when doing so remains unambiguous;
- avoiding metadata and visual decoration that do not affect interpretation;
- excluding specialized instructions that are rarely applicable;
- moving conditional, domain-specific or workflow-specific behavior to on-demand mechanisms when available.

As a portability and efficiency recommendation, an LMA Memory Template should be structured below 4,000 Unicode characters, including whitespace, whenever all required behavior can be preserved clearly within that budget.

The 4,000-character recommendation is not a conformance limit. A larger template remains conformant when additional characters are necessary for clarity, completeness or deterministic interpretation. Behavioral reliability takes precedence over the size target.

Authors should leave reasonable space below the limit for future corrections and should measure the final serialized artifact rather than an unformatted draft.

Context efficiency must not reduce behavioral clarity, accuracy or deterministic interpretation.

## 14. Persistent memory boundary

LMA stores persistent behavioral instructions.

It must not be used to store transient conversation state, temporary task requirements or short-lived contextual information.

Temporary user instructions belong to the current interaction and may temporarily override persistent rules according to the protocol.

Specialized instructions that are not broadly persistent should be kept outside the LMA Memory Template when they can be activated only when required.

## 15. Provider independence

An LMA Memory Template must not depend on provider-specific terminology, internal instruction hierarchies or undocumented model behavior.

The memory should remain portable across compatible LLM systems.

Provider-specific restrictions or internal policies remain outside the LMA specification.

## 16. Self-containment

The Memory Template is the executable artifact.

A language model receiving only the Memory Template must have enough information to:

- understand that the document defines persistent behavioral memory;
- interpret each rule;
- understand priority ordering;
- process temporary user overrides;
- resolve ordinary rule conflicts;
- identify when unresolved conflict handling is required.

The Core Specification is documentation for authors and maintainers, not a runtime dependency.

## 17. Conformance

An LMA v1.0.x Memory Template conforms to this specification when:

- it uses `<persistent_memory>` as the root element;
- it contains a `<protocol>` block;
- every rule contains `id` and `priority`;
- priorities use only `critical`, `high` or `normal`;
- rule IDs are unique;
- each rule appears under the category matching its ID prefix;
- the priority order is defined as `critical > high > normal`;
- user overrides are temporary;
- unresolved equal-priority conflicts are handled explicitly;
- the memory does not require this specification to be interpreted;
- no `strength` attribute is required;
- no `scope` attribute is required;
- categories remain semantically distinct;
- persistent rules remain justified by their behavioral value relative to their context cost.

Conformance does not depend on indentation, attribute line wrapping, blank-line count or decorative formatting. These may vary when the document remains unambiguous and all required structure and behavior are preserved.

The recommendation to remain below 4,000 characters is evaluated separately from conformance.

## 18. Versioning

LMA uses semantic versioning:

```text
MAJOR.MINOR.PATCH
```

- `MAJOR`: incompatible changes to syntax or semantics.
- `MINOR`: backward-compatible additions or extensions.
- `PATCH`: corrections or clarifications that do not change compatibility.

Current Core Specification version:

```text
1.0.1
```

LMA Core Specification v1.0.1 corresponds directly to LMA Memory Template v1.0.1. It clarifies compact serialization, non-semantic whitespace and decoration, simple category markers, semantic compression safeguards and the recommended sub-4,000-character Template size without introducing incompatible syntax or behavioral semantics.
