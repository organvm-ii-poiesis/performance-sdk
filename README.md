> **⚠️ ARCHIVED** — This repository has been consolidated into [metasystem-master](https://github.com/organvm-ii-poiesis/metasystem-master). All active development has moved there. This repository is preserved for historical reference only.

---

[![ORGAN-II: Poiesis](https://img.shields.io/badge/ORGAN--II-Poiesis-6a1b9a?style=flat-square)](https://github.com/organvm-ii-poiesis)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react&logoColor=black)](https://react.dev/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](./LICENSE)
[![Vite](https://img.shields.io/badge/Vite-5-646CFF?style=flat-square&logo=vite&logoColor=white)](https://vitejs.dev/)

# performance-sdk

**Real-time computational agency for participatory performance.**

A TypeScript/React SDK that provides the unified interface between performers and audiences in live, participatory creative events. The SDK powers two complementary surfaces — an **Audience Interface** where participants influence performance parameters through continuous input, and a **Performer Dashboard** where artists monitor live consensus data, inspect metasystem health across the broader project universe, and exercise authoritative override when artistic judgment demands it.

This is not a media player or a sequencer. It is the **control surface for collective creative agency** — the layer where individual audience inputs converge into consensus parameters, where those parameters flow to the performer in real time, and where the performer retains sovereign authority over the final artistic output. The SDK implements a specific philosophy: audiences have *influence*, performers have *authority*, and the tension between the two is where the art happens.

---

## Table of Contents

- [Artistic Purpose](#artistic-purpose)
- [Conceptual Approach](#conceptual-approach)
- [Architecture Overview](#architecture-overview)
- [Technical Components](#technical-components)
  - [Audience Interface](#audience-interface)
  - [Performer Dashboard](#performer-dashboard)
  - [Wallet Authentication](#wallet-authentication)
  - [Shared Infrastructure](#shared-infrastructure)
- [Installation and Quick Start](#installation-and-quick-start)
- [Configuration](#configuration)
- [Theory Implemented](#theory-implemented)
- [System Integration](#system-integration)
- [Related Work](#related-work)
- [Contributing](#contributing)
- [License](#license)
- [Author](#author)

---

## Artistic Purpose

Live performance has always involved an implicit negotiation between performer and audience. The audience's energy, attention, and response shape what the performer does next — but this feedback loop is analog, ambiguous, and unidirectional. The performer reads the room; the audience has no explicit channel back.

**performance-sdk** makes this negotiation explicit, digital, and bidirectional. It gives audiences a direct parametric channel into the performance — sliders that control density, tempo, timbre, reverb, and any other parameter the performance exposes. It aggregates these individual inputs into a collective consensus. And it gives the performer a live dashboard that renders this consensus as actionable data, alongside the ability to override any parameter when the artistic moment demands it.

The result is a new kind of performance event: one where the audience is not passive, the performer is not autocratic, and the boundary between the two is a negotiated, visible, real-time interface. This is what we mean by **computational agency** — not AI replacing human creativity, but computation mediating the space between human creative agents.

Every performance becomes unrepeatable not because of improvisation alone, but because the specific configuration of audience inputs, consensus dynamics, and performer overrides can never recur. The SDK does not produce art. It produces the *conditions* under which a particular kind of art — participatory, emergent, collective — becomes possible.

## Conceptual Approach

A good performance SDK must solve three problems simultaneously:

1. **The Input Problem:** How do you let hundreds or thousands of audience members express creative preference without chaos? The solution is parametric reduction — instead of free-form input, audiences manipulate a small number of continuous parameters (0.0 to 1.0) that map to meaningful creative dimensions. Each parameter is a question: *How dense should the sound be? How fast? How bright? How wet?* The audience answers with sliders. The system answers with consensus.

2. **The Authority Problem:** Collective input is valuable but not sacred. A performer must retain the ability to override consensus when artistic vision demands it — to push against the crowd, to introduce surprise, to exercise the judgment that makes performance an art form rather than a poll. The SDK encodes this through explicit override mechanisms on the performer side, separate from the consensus pipeline.

3. **The Liveness Problem:** Performance is real-time or it is nothing. Latency between audience input and performer visibility must be imperceptible. The SDK uses WebSocket connections (Socket.IO) with dedicated namespaces for audience and performer traffic, ensuring that parameter updates flow at the speed of human perception rather than the speed of HTTP request-response cycles.

Beyond these three problems, the SDK also addresses a fourth concern that is specific to the ORGAN system: **metasystem observability**. The performer dashboard does not only show audience consensus — it also renders a live health view of the broader project universe, with the ability to dispatch agentic signals to any workspace. This transforms the performer from a musician or visual artist into a *metasystem operator* — someone who is simultaneously performing for an audience and monitoring the health of the infrastructure that makes the performance possible.

## Architecture Overview

The SDK is structured as a React application with three primary domains, a shared infrastructure layer, and a WebSocket transport backbone:

```
performance-sdk/
├── src/
│   ├── App.tsx                          # Router: Home / Audience / Performer
│   ├── main.tsx                         # React 18 entry point (StrictMode)
│   │
│   ├── audience-interface/              # Audience-facing surface
│   │   ├── components/
│   │   │   ├── ConnectionStatus.tsx     # WebSocket health indicator
│   │   │   ├── ParameterSlider.tsx      # Continuous parameter input (0–1)
│   │   │   └── VotingPanel.tsx          # Discrete consensus voting UI
│   │   └── hooks/
│   │       ├── useConnection.ts         # Socket.IO lifecycle management
│   │       └── useVoting.ts             # Vote submission and tallying
│   │
│   ├── performer-dashboard/             # Performer-facing surface
│   │   ├── components/
│   │   │   ├── LiveParameters.tsx       # Real-time consensus visualization
│   │   │   ├── OverridePanel.tsx        # Performer authority controls
│   │   │   └── PerformerStatus.tsx      # Connection and system status
│   │   └── hooks/
│   │       ├── useBusSubscription.ts    # Event bus parameter subscription
│   │       └── useOverride.ts           # Override state management
│   │
│   ├── auth/
│   │   └── WalletLogin.tsx              # Solana wallet authentication (Phantom/Solflare)
│   │
│   └── shared/
│       ├── constants/
│       │   └── parameters.ts            # Parameter definitions and ranges
│       ├── types/
│       │   ├── consensus.ts             # Consensus computation types
│       │   └── performance.ts           # Performance session types
│       └── utils/
│           └── parameter-utils.ts       # Parameter normalization and math
│
├── vite.config.ts                       # Vite 5 dev server (port 3000)
├── tsconfig.json                        # TypeScript 5.3 strict mode
└── package.json                         # @omni-dromenon-engine/performance-sdk
```

**Transport:** The SDK communicates with a backend core engine over Socket.IO WebSockets, using separate namespaces (`/audience` and `/performer`) to segregate traffic types. The audience namespace handles high-volume parameter input events; the performer namespace handles aggregated consensus values and metasystem health broadcasts.

**Rendering:** Built on React 18 with React Router v6 for client-side navigation between the three views (Home, Audience, Performer). The UI is intentionally minimal — dark-themed, functional, focused on data clarity over decorative aesthetics. In a live performance context, both the audience interface (on phones) and the performer dashboard (on a stage monitor) need to be instantly legible under variable lighting conditions.

## Technical Components

### Audience Interface

The audience interface is the public-facing surface — typically loaded on audience members' phones via a shared URL or QR code at the beginning of a performance.

**Core interaction model:** Four continuous parameter sliders (density, tempo, timbre, reverb), each ranging from 0.0 to 1.0, each emitting `input` events over the `/audience` WebSocket namespace on every change. The backend aggregates these inputs across all connected audience members to compute a consensus value per parameter.

**`ConnectionStatus`** renders a small colored indicator (green/red) showing WebSocket health. In a live performance context, connection status is not a nice-to-have — it is critical feedback that tells an audience member whether their input is actually reaching the system.

**`ParameterSlider`** provides the continuous input control. Each slider is a standard HTML range input, but the component architecture is designed for extensibility: additional parameters can be added to the system by extending the parameter constants, and custom slider components (logarithmic, stepped, threshold-based) can replace the default linear slider.

**`VotingPanel`** provides discrete consensus voting — for moments in a performance where the collective decision is binary or categorical rather than continuous. ("Should the piece accelerate? Yes / No." "Choose the next movement: A / B / C.")

**`useConnection`** manages the Socket.IO lifecycle: connect, reconnect, disconnect, error handling. It abstracts the transport layer so that components never interact with the socket directly.

**`useVoting`** manages vote submission, deduplication, and local tallying before consensus results arrive from the server.

### Performer Dashboard

The performer dashboard is the privileged surface — typically displayed on a laptop or stage monitor visible only to the performer.

**Metasystem Health View:** A grid of project cards showing the health of workspaces across the broader ORGAN system. Each card displays the project name, tech stack, and last test result (pass/fail). The performer can dispatch an agentic signal to any workspace — triggering a consistency check or routine maintenance task — directly from the dashboard. This transforms the dashboard from a performance tool into an **operations console** for the entire metasystem.

**Live Parameters View:** A set of horizontal progress bars showing the current consensus value and confidence level for each parameter. Values update in real time via the `/performer` WebSocket namespace. The confidence metric indicates how much agreement exists among audience inputs — a high-confidence 0.8 means the audience broadly agrees on high density; a low-confidence 0.5 means the audience is evenly split.

**`OverridePanel`** gives the performer explicit authority controls. When the performer overrides a parameter, their value supersedes the audience consensus. Overrides can be temporary (snap back to consensus after a duration) or persistent (hold until manually released). This is the mechanism by which performer authority is encoded in the system rather than merely assumed.

**`useBusSubscription`** subscribes to the event bus for real-time parameter updates, abstracting the subscription lifecycle from component rendering.

**`useOverride`** manages override state — which parameters are currently overridden, what the override values are, and when overrides expire.

### Wallet Authentication

The SDK includes Solana wallet authentication via `WalletLogin.tsx`, supporting Phantom and Solflare wallets on Devnet. This serves two purposes:

1. **Identity without accounts:** Audience members authenticate via their existing crypto wallet rather than creating a platform-specific account. This is zero-friction identity — if you have Phantom installed, you have an identity.

2. **Future token-gating:** Wallet authentication lays the groundwork for token-gated performances, where participation requires holding a specific NFT or token. This connects the performance SDK to the broader ORGAN-III commerce infrastructure, where creative experiences can be economically sustained through on-chain mechanisms.

The wallet integration uses `@solana/wallet-adapter-react` with the standard provider pattern: `ConnectionProvider` wraps `WalletProvider` wraps `WalletModalProvider`, exposing the `WalletMultiButton` for user interaction.

### Shared Infrastructure

**Types:** `performance.ts` defines the core data structures for performance sessions (session ID, state, connected participants, active parameters). `consensus.ts` defines the types for consensus computation (input aggregation, confidence intervals, override state).

**Constants:** `parameters.ts` defines the parameter registry — the authoritative list of parameters the system supports, their display names, default values, and valid ranges.

**Utilities:** `parameter-utils.ts` provides normalization, clamping, interpolation, and mathematical operations on parameter values. These utilities ensure that parameter values are always within valid bounds and that transitions between values are smooth rather than abrupt.

## Installation and Quick Start

### Prerequisites

- **Node.js** 18+ (LTS recommended)
- **npm** 9+ or **pnpm**
- A running instance of the core engine backend (see [System Integration](#system-integration))

### Install Dependencies

```bash
git clone https://github.com/organvm-ii-poiesis/performance-sdk.git
cd performance-sdk
npm install
```

### Development Server

```bash
npm run dev
```

The application starts on `http://localhost:3000`. Navigate to:
- `/` — Home screen with links to Audience and Performer views
- `/audience` — Audience parameter interface
- `/performer` — Performer dashboard with live data and metasystem health

### Production Build

```bash
npm run build     # TypeScript compilation + Vite production bundle
npm run preview   # Serve the production build locally
```

### Run Tests

```bash
npm test          # Runs Vitest
```

### Lint

```bash
npm run lint      # ESLint for .ts and .tsx files
```

### Environment Configuration

The SDK connects to the core engine backend at a configurable API URL. The default points to the deployed Cloud Run instance:

```
https://omni-dromenon-core-dkxnci5fua-uc.a.run.app
```

To connect to a local backend during development, set the API URL in your environment or modify the `API_URL` constant in `src/App.tsx`.

## Configuration

### TypeScript

The project uses TypeScript 5.3 in **strict mode** with the following enforcement flags:
- `noUnusedLocals` — no dead variables
- `noUnusedParameters` — no dead parameters
- `noFallthroughCasesInSwitch` — exhaustive switch handling

Target is ES2020 with React JSX transform. Module resolution uses Vite's bundler mode for optimal tree-shaking.

### Adding New Parameters

To add a new audience-controllable parameter:

1. Add the parameter definition to `src/shared/constants/parameters.ts`
2. Add the initial value to the `values` state object in the Audience component
3. The parameter will automatically render as a slider and emit events
4. The performer dashboard will automatically display the new parameter's consensus value

The system is designed so that parameter additions are a configuration change, not a code change.

## Theory Implemented

This SDK is the practical implementation of several theoretical positions developed in ORGAN-I (`organvm-i-theoria`):

**Recursive Agency:** The system implements a recursive loop — audience input shapes performance output, performance output shapes audience perception, audience perception shapes the next round of input. The SDK does not break this loop; it instruments it. Every parameter value is a snapshot of a continuous recursive process.

**Consensus as Emergent Property:** Individual audience inputs are not votes on a correct answer. They are expressions of preference that, when aggregated, produce an emergent consensus that no individual intended. The consensus is not the average — it is a new signal that exists only because multiple agents contributed to it. This is a direct implementation of the ORGAN-I concept of emergent properties in multi-agent systems.

**Observer-Performer Collapse:** In traditional performance, the observer and performer are distinct roles with a clear boundary. This SDK collapses that boundary — audience members are participants whose inputs materially affect the performance, and the performer is an observer of the audience's collective state. The boundary does not disappear; it becomes negotiable, visible, and encoded in the override mechanism.

**Metasystem Awareness:** The performer dashboard's metasystem health view implements a key ORGAN-IV principle: every component of the system should be aware of the system it belongs to. The performer is not only performing — they are operating within a larger metasystem and can inspect and influence that metasystem from within the performance context.

## System Integration

**performance-sdk** operates within the broader Omni-Dromenon engine architecture and the ORGAN system:

| Component | Repository | Relationship |
|-----------|-----------|-------------|
| **Core Engine** | [`organvm-ii-poiesis/core-engine`](https://github.com/organvm-ii-poiesis/core-engine) | Backend that receives audience inputs, computes consensus, and broadcasts to performers. The SDK is the frontend to this backend. |
| **Metasystem Master** | [`organvm-ii-poiesis/metasystem-master`](https://github.com/organvm-ii-poiesis/metasystem-master) | Orchestration layer that the performer dashboard monitors via the metasystem health view. Dispatch signals route through this system. |
| **Recursive Engine** | [`organvm-i-theoria/recursive-engine`](https://github.com/organvm-i-theoria/recursive-engine) | Theoretical framework for the recursive agency model that the audience-performer feedback loop implements. |
| **Agentic Titan** | [`organvm-iv-taxis/agentic-titan`](https://github.com/organvm-iv-taxis/agentic-titan) | Agentic orchestration layer that receives dispatch signals from the performer dashboard. |

**Data Flow:**

```
Audience phones          performance-sdk         Core Engine          Performer monitor
─────────────────       (this repo)              (backend)            (this repo)
                    ┌─────────────────┐     ┌──────────────┐    ┌──────────────────┐
  Slider input ───→ │ Audience View   │ ──→ │  Aggregate   │ ─→ │ Performer View   │
                    │ /audience ns    │     │  Consensus   │    │ /performer ns    │
                    └─────────────────┘     └──────────────┘    │                  │
                                                                │ Override ───────→│──→ (output)
                                                                │ Dispatch ───────→│──→ metasystem
                                                                └──────────────────┘
```

## Related Work

**performance-sdk** exists in a landscape of tools for live creative coding and real-time audience interaction. It is useful to understand what it is *not*:

- **[Hydra](https://hydra.ojack.xyz/)** is a live-codable video synthesizer. It enables a single performer to write visual shader code in real time. The performance-sdk is not a synthesizer — it is the control surface that could *drive* a Hydra instance, feeding audience-derived parameters into a performer's visual patch.

- **[LiveCodeLab](https://livecodelab.net/)** provides a browser-based environment for live-coded visuals and music. Like Hydra, it is a single-performer tool. The performance-sdk addresses the multi-agent problem that LiveCodeLab does not: what happens when the audience is also a creative agent?

- **[Gibber](https://gibber.cc/)** is a live-coding environment for music and graphics with collaborative features. Gibber's collaboration model is peer-to-peer among performers; the performance-sdk's model is asymmetric — audience members have influence, the performer has authority, and the roles are structurally distinct.

- **Audience response systems** (Mentimeter, Slido, and similar) provide poll-based audience input. The performance-sdk goes beyond polling by providing *continuous* parametric input with real-time feedback — this is not a survey, it is a live control surface.

The key differentiator is the **authority model**. Most collaborative creative tools assume symmetric roles. The performance-sdk encodes an asymmetric relationship — influence vs. authority — and makes that asymmetry visible, negotiable, and artistically productive.

## Contributing

Contributions are welcome. This is an early-stage SDK and the API surface is not yet stable.

### Development Workflow

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Make changes with tests
4. Run `npm run lint && npm test` before committing
5. Submit a pull request with a clear description of the change

### Areas for Contribution

- **Custom parameter types** — logarithmic sliders, XY pads, gesture-based input
- **Mobile optimization** — the audience interface must work flawlessly on phones
- **Accessibility** — screen reader support, keyboard navigation, high-contrast mode
- **Additional wallet adapters** — beyond Phantom and Solflare
- **Test coverage** — component tests, integration tests, WebSocket mock tests
- **Documentation** — API reference, performance setup guides, deployment tutorials

### Code Standards

- TypeScript strict mode (no `any` unless genuinely necessary)
- React functional components with hooks
- Named exports from barrel files
- Descriptive variable and function names

## License

[MIT](./LICENSE)

## Author

**Anthony Padavano** ([@4444j99](https://github.com/4444j99))

Part of the [ORGAN-II: Poiesis](https://github.com/organvm-ii-poiesis) creative organ — art, generative systems, and performance infrastructure within the [ORGAN metasystem](https://github.com/meta-organvm).
