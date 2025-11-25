# Training to Collaboration Flow

How conversation becomes addressable knowledge.

```
┌─────────────────────────────────────────────────────────────────────┐
│                           AI LAYER                                  │
│                                                                     │
│    ┌─────────────┐                ┌─────────────┐                  │
│    │   Expert    │◄──────────────►│     AI      │                  │
│    │             │   conversation │             │                  │
│    └─────────────┘                └──────┬──────┘                  │
│                                          │                          │
│                                          │ extracts + writes        │
│                                          ▼                          │
│                                ┌─────────────────┐                 │
│                                │  Mental Model   │                 │
│                                │  (externalized) │                 │
│                                └────────┬────────┘                 │
└─────────────────────────────────────────┼───────────────────────────┘
                                          │
                                          │ persists to
                                          ▼
┌───────────────────────────────────────────────────────────────────────┐
│                           URI LAYER                                   │
│                                                                       │
│    ┌─────────────────────────────────────────────────────────────┐   │
│    │                    CLAUDE.md files                          │   │
│    │                                                              │   │
│    │   github.com/org/service-a/CLAUDE.md                        │   │
│    │   github.com/org/service-b/CLAUDE.md                        │   │
│    │   ...                                                       │   │
│    │                                                              │   │
│    │   (URI-addressable knowledge)                               │   │
│    └─────────────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────────────────┘


                         LATER: COLLABORATION
                         ────────────────────

┌─────────────────────────────────────────────────────────────────────┐
│                           AI LAYER                                  │
│                                                                     │
│    ┌─────────────┐   shared       ┌─────────────┐                  │
│    │   Expert    │◄──────────────►│     AI      │                  │
│    │             │   context      │             │                  │
│    └─────────────┘                └──────┬──────┘                  │
│                                          │                          │
│                                          │ loads context            │
│                                          │                          │
└──────────────────────────────────────────┼──────────────────────────┘
                                           │
                                           │ fetches from
                                           │
┌──────────────────────────────────────────┼──────────────────────────┐
│                           URI LAYER      │                          │
│                                          ▼                          │
│    ┌─────────────────────────────────────────────────────────────┐ │
│    │                    CLAUDE.md files                          │ │
│    └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘


                         THE FLOW
                         ────────
           ┌───────┐         ┌───────┐         ┌───────────┐
           │ CHAT  │────────►│ FETCH │────────►│ COLLABORATE│
           └───────┘         └───────┘         └───────────┘
              │                  │                   │
              ▼                  ▼                   ▼
         Expert talks       AI loads            Working
         AI extracts +    context from        together with
         writes to URI       URI              shared context
```

## Two Layers

**URI Layer**: Where knowledge lives. CLAUDE.md files at stable addresses.

**AI Layer**: Where conversation happens. Expert talks, AI listens and writes.

## The Shift

The expert just chats. The AI:
1. Extracts the mental model from conversation
2. Writes it to CLAUDE.md
3. Persists it at a URI

Later, any AI can fetch that context and collaborate with the expert as if they'd had that conversation.

No fine-tuning. No embeddings. No RAG pipelines. Just conversation → file → URI → collaboration.
