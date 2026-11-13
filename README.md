# AI Collab

A Java Swing desktop app where **Claude, GPT, and Gemini debate the same prompt**. Each model answers a question independently, then reads the other two responses and reacts — agreeing, challenging, or refining its own answer — for a configurable number of rounds. A final synthesis pass turns the debate into a single structured report.

The project explores multi-model orchestration: routing one request to three providers, normalizing their responses into a shared conversation format, and giving each model visibility into what the others said.

## How it works

1. You ask a question through the GUI.
2. `Maestro` sends the prompt independently to `AnthropicClient`, `OpenAiClient`, and `GeminiClient`, all behind a common `LlmClient` interface.
3. Each response is appended to a shared `ConversationContext`.
4. On each debate round, every model is re-prompted with the others' latest responses and reacts.
5. A synthesis step produces the final report, and the Swing UI renders every round side by side.

Every request carries layered context assembled by `PromptBuilder`: organization context, the model's agent identity (each provider is given a distinct perspective), and the profile of the person asking.

## Beyond the debate

On top of the debate engine the app includes an **Executive Suite**: a board of one-click task buttons (meeting prep, weekly report, stakeholder briefing, and more) that gather structured input through short forms, run either a single model or a full debate, and keep a local organization context fresh over time. That task layer is being developed further as a standalone project, [`cowork-suite`](https://github.com/grant-hauskins/cowork-suite).

## Run it

Requires Java 21 and Maven.

```bash
mvn compile exec:java
```

On first launch the app asks for API keys (Anthropic, OpenAI, Gemini) and writes them to a local, git-ignored `config.properties`. See `config.properties.example` for the keys needed and where to get them.

## Project layout

```
src/main/java/collab/
├── Main.java, MainGui.java        Entry point and Swing shell
├── Maestro.java                   Runs the multi-round debate and synthesis
├── LlmClient.java                 Interface every provider implements
├── AnthropicClient.java           Claude
├── OpenAiClient.java              GPT
├── GeminiClient.java              Gemini
├── PromptBuilder.java             Assembles layered context for each call
├── ConversationContext.java       Shared transcript across rounds
├── *Task.java, ButtonPanel.java   Executive Suite task buttons and forms
├── OrganizationContext.java,
│   ReconciliationService.java     Local context store and safe AI-proposed updates
└── *Dialog.java, *Store.java      Editors and JSON persistence
docs/archive/                      Original roadmap, design notes, and the v0.3 prototype
```

## Stack

Java 21 · Maven · Swing (FlatLaf) · Anthropic, OpenAI, and Gemini REST APIs · Gson

## Design notes

The code favors readability over cleverness: one concept per class, block comments that explain *why*, and intentionally parallel provider clients so reading one teaches all three. The original planning documents, including the roadmap and the single-file prototype this grew out of, are preserved in [`docs/archive`](docs/archive/).

## License

MIT
