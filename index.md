# Architecture Without Architects

> Markdown version of the project page at https://aiml-sdu.github.io/architecture-without-architects-page/. Interactive figures are on the web page. Every number here also appears there and comes from the paper or its stored results.

SAGAI Workshop at IEEE ICSA 2026 · Position paper

How AI coding agents shape software architecture

Coding agents are usually treated as fast typists, yet they also pick the framework, the store and the integration of the systems they build. When those systems call a language model themselves, the wording of the prompt decides which infrastructure has to exist.

**Phongsakon Mark Konrad**, **Tim Lukas Adam**, **Riccardo Terrenzi**, **Serkan Ayvaz**

Centre for Industrial Software, University of Southern Denmark · April 2026

[arXiv](https://arxiv.org/abs/2604.04990) · [PDF](https://arxiv.org/pdf/2604.04990) · [Markdown](index.md) · [Code](https://github.com/phomarkon/vibe-architecting-case-study) · Cite this article

- We gave one coding agent the same chatbot task three times and changed only the prompt. The systems it built grew from 141 lines in 2 files to 472 lines in 4 files to 827 lines in 6 files.

- Asking for structured output brought in three components that the plain version lacks, a Zod schema, a retry handler and a fallback. Declaring three tools brought in a tool registry, an agent loop and an SQLite state store.

- A survey of six coding tools yields five mechanisms through which agents make architectural decisions that nobody records.

- We propose six patterns that map a prompt feature to the infrastructure it requires. Four of them are contingent and may fade as models improve. Two are fundamental and stay.

- The case study is an illustration. Each prompt ran once, with one agent and one runtime model.

**First**AI coding agents select frameworks, scaffold infrastructure and wire integrations within seconds. Those are architectural decisions, and almost no one reviews them as such. We identify five mechanisms by which agents make these choices implicitly, drawn from a survey of six tools.

**Second**We propose six prompt to architecture coupling patterns in three categories. Each one maps a feature of a natural language prompt to the infrastructure it requires. Four are contingent, like structured output validation, and may weaken as models gain native capabilities. Two are fundamental, like tool call orchestration, and persist whatever the model can do.

**Finally**A small demonstration shows that prompt wording alone yields structurally different systems for the same task, from 141 to 827 lines of code and from two to six files. We call the phenomenon vibe architecting and sketch review practices, decision records and tooling that would bring these hidden decisions under governance.

**Worst case** The claim would fail if changing the prompt left the generated structure unchanged. The demonstration tested exactly that and found three different component sets. It did so once per prompt, with one agent, one runtime model and prompts that themselves name architectural features. A replication across agents and models, and a run with minimally prescriptive prompts, have not been done.

**Vibe coding.** Building software by describing what you want in plain language and letting a model write the code, in the phrase coined by Karpathy.

**Vibe architecting.** Architecture shaped by natural language prompts instead of by deliberate, recorded design.

**Prompt to architecture coupling.** A link from a feature of a prompt, such as an output schema or a tool signature, to the components the system then needs.

**Contingent coupling.** A coupling likely to weaken as models gain native capabilities, for example native JSON output.

**Fundamental coupling.** A coupling that is logically necessary whatever the model can do. Executing a tool always needs orchestration.

**Architecture decision record (ADR).** A short document that states one architectural decision, its context and its rationale.

**MCP.** The Model Context Protocol, a standard way for agents to discover, invoke and exchange data with tools.

## The prompt becomes the architecture specification

We asked one coding agent, with default settings, to build a customer service chatbot three times. Each run started from scratch with no shared context. Every variant used GPT-4o-mini at runtime, so the prompt was the only thing that changed. The first prompt names a task and asks to answer questions from a FAQ, the second adds an output contract that asks for JSON with intent, confidence and entities, and the third declares capabilities by listing three tools the chatbot may call.

Variant A came out flat, with 141 lines in 2 files. B acquired a Zod schema, a retry handler and a fallback generator and grew to 472 lines in 4 files. The tool version acquired a tool registry, an agent loop of ten iterations and an SQLite state store, reaching 827 lines in 6 files. All three use Express. Their components, dependency graphs and failure modes differ.

As the prompt gets more specific, prompting and architecture blur, because a capability declaration is in practice an interface definition and an output schema is in practice a data contract that the rest of the system has to honour. The generated infrastructure follows from the prompt. When agents write prompts for other agents, as in coordinated sub-agent workflows, this coupling runs with no human review at all.

*Figure.* **Figure 1**Size of the three generated systems as reported in the paper. Switch between lines of code and files. The marked bar is the tool access variant.

The prompts were not neutral. The extra instructions for B asked for schema validation with a retry or a fallback, and those for C asked for multi turn history. The hero above shows them under each prompt. The paper names this as a threat to internal validity, since the prompts encode architectural expectations at varying specificity.

## Agents decide through five mechanisms, and none leaves a record

The range of what agents decide has widened year by year. Line level autocomplete in 2022 had almost no architectural footprint, while the multi file tools of 2024 edited across files and so drew module boundaries without anyone deciding where they should go. System level agents arrived in 2025 and scaffold whole projects from one prompt. Coordinated agents now split work across sub-agents, which turns decomposition itself into an architectural concern. Standards such as MCP and the AGENTS.md convention have hardened this into a de facto integration architecture.

*Figure.* **Figure 2**The widening decision surface, as the paper describes it. Each ring adds what agents decide at that stage. The years are the paper's own and the rings are an illustration with no measured size.

From a survey of six tools we identify five mechanisms through which these decisions happen.

Three properties set agent decisions apart from human ones.

## Six couplings tie prompt features to infrastructure, and two of them will not go away

We traced the abstractions in LangChain, LlamaIndex and provider documentation, following each prompt feature to the infrastructure it requires. Six patterns fall into three groups. Constraint patterns shape the form of the output. Capability patterns declare what the model can do. Context patterns control which information reaches the model.

Each pattern is either contingent or fundamental. Structured output needs a parser, a validator, a retry and a fallback today, but native JSON guarantees may shrink that stack. Function calling is different. Typed tool signatures produce a router, an argument validator, an error handler and a response loop, and every added tool widens the attack surface. Tool execution needs orchestration however capable the model becomes. The catalog is not exhaustive.

*Figure.* **Figure 3**The six coupling patterns of the paper's catalog. Pick one to trace its trigger to the infrastructure it requires. Fundamental patterns are drawn in red. Quality impacts are analytical, not measured.

## Couplings compose in one system

A retrieval chatbot with tool access pulls in retrieval, context reduction, structured output and function calling at once. Each pattern adds its own components to the pipeline. Cross cutting concerns such as authentication, rate limiting and logging then span several patterns and add infrastructure that no single pattern accounts for. Whether those concerns grow additively or faster than that as patterns combine is an open question in the paper.

*Figure.* **Figure 4**An illustration of the paper's composition figure. Switch patterns on and off to assemble the pipeline. The dashed cross cutting bars light up once two or more patterns share the system.

Composition widens the gap between building and reviewing, since agents scaffold a system in minutes while the team that owns it needs hours or days to audit what was built. Tool use patterns widen the attack surface through indirect prompt injection. MCP standardizes tool integration and with it the attack vectors.

## Governance can borrow three layers from software architecture

Agents make architectural decisions, but no feedback loop ties those decisions to established architectural knowledge. We sketch a framework with three layers, each matching a mechanism that tools already have to a concept the field already uses.

*Figure.* **Figure 5**The three layer framework from the paper. Pick a layer to see the tool mechanism it builds on and the architecture concept it formalizes.

Prompt specifications are architectural documents and belong in architectural review. An impact statement written before generation, such as the paper's example “Structured JSON output adds validator, retry handler, fallback; +330 LoC, two new failure modes”, would make the coupling explicit. The 5.9× growth in code and the threefold growth in files across the case study suggest that complexity thresholds could trigger a review when exceeded.

### It shows

- That one agent, given three wordings of one task, built three structurally different systems.

- Five mechanisms of implicit architectural decision, supported by a survey of six tools.

- A catalog of six coupling patterns, each classed as contingent or fundamental.

### It does not show

- That the divergence generalizes. Each prompt ran once, with one tool and one runtime model.

- What an agent decides unprompted. The prompts encode architectural expectations at varying specificity.

- Empirical validation of the six patterns or of their quality impacts, which are analytical constructs offered for discussion.

### Next steps

The paper names five directions. Replicate across agents to see whether agent choice is itself an architectural decision. Build footprint metrics richer than lines and files. Build tools that preview architectural impact before generation. Extract decision records from agent reasoning traces. Study how patterns compose.

Konrad, P. M., Adam, T. L., Terrenzi, R., and Ayvaz, S. Architecture Without Architects. How AI Coding Agents Shape Software Architecture. SAGAI Workshop at IEEE ICSA 2026.

```bibtex
@inproceedings{konrad2026architecture,
  title     = {Architecture Without Architects: How AI Coding Agents Shape Software Architecture},
  author    = {Konrad, Phongsakon Mark and Adam, Tim Lukas and Terrenzi, Riccardo and Ayvaz, Serkan},
  booktitle = {SAGAI Workshop, IEEE International Conference on Software Architecture (ICSA)},
  year      = {2026},
  eprint    = {2604.04990},
  archivePrefix = {arXiv}
}
```
