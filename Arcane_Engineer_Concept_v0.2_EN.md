> A first-person systemic fantasy immersive sim about discovering, engineering and exploiting the laws of magic.

# 1. Executive concept

Arcane Engineer is a solo-playable, co-op-ready first-person immersive sim set in a dangerous Renaissance-inspired fantasy frontier. The player is an outlawed arcane engineer who studies magical matter, constructs modular spells and devices, and solves open-ended objectives inside physically reactive environments. Rather than unlocking a predefined list of spells, players learn consistent rules and invent applications. Every meaningful solution should be explainable by the same simulation used by enemies, machines and the world.

# 2. Player fantasy and positioning

Fantasy: “I discovered a law of magic, built a ridiculous machine, and solved a problem the designers never scripted.” The game combines Noita-like systemic reactions, Teardown-like material consequences and immersive-sim objectives, but prioritizes programmable magic over destruction for its own sake. Voxels are enabling technology, not the selling point.

## Elevator pitch

Invent magic instead of casting it. Combine heat, force, conductivity, pressure and strange arcane properties to infiltrate fortresses, disable machines, overcome creatures and reshape the battlefield. Every material reacts; every plan has consequences.

## Target audience

PC players who enjoy Noita, immersive sims, engineering sandboxes, experimentation, roguelites and shareable systemic moments. Initial target: premium PC release with full solo play and optional 2–4 player cooperative expeditions, subject to early networking feasibility gates.

# 3. Design pillars

1\) Understandable causality: materials and effects obey legible, testable rules. 2) Player authorship: tools are assembled, not merely collected. 3) Multiple solutions: objectives can be reached by different combinations of systems. 4) Spectacular consequences: chains of events generate short, shareable stories. 5) Controlled complexity: depth emerges from a small, consistent vocabulary rather than hundreds of bespoke exceptions.

# 4. Core gameplay loop

Survey a hostile site → identify physical and magical affordances → collect reagents and information → assemble spells/devices → execute a plan → observe consequences and adapt → extract knowledge, components and rewards → upgrade workshop and attempt more demanding missions. Within each mission, the moment-to-moment loop is observe → hypothesize → experiment → exploit.

# 5. World and premise

The setting is a late-medieval / early-Renaissance realm experiencing an industrial awakening driven by newly measurable arcane phenomena. Guilds monopolize magic, military engineers weaponize it and cities are built around unstable deposits. The protagonist operates from a mobile workshop, undertaking commissions, investigations and acts of sabotage. The story should support player choice without requiring a sprawling RPG dialogue system.

# 6. Magic engineering system

A spell is a directed graph of modules with explicit costs, activation conditions and outputs. Players configure a limited number of nodes, test a design in the workshop and deploy it in the field. Failure can be useful and entertaining, but feedback must reveal why it occurred.

## Module families

Sources: mana cell, environmental heat, charged crystal. Triggers: impact, timer, proximity, conductivity threshold. Operators: split, delay, amplify, convert, repeat (bounded), filter. Payloads: heat, cold, impulse, electric charge, corrosion, phase change. Delivery: projectile, beam, field, contact, placed rune. Constraints: energy budget, stability, material compatibility, cooldown and overheating.

## Example constructions

Steam lance: inject heat into water, confine expansion, release directed impulse. Conductive snare: wet floor + triggered electric pulse + metal lattice. Thermal breach: alternate intense heat and cooling on brittle stone, then apply impulse. Emergency elevator: attach force rune to a movable platform with a proximity trigger. These are design targets, not promises of physically exact chemistry.

# 7. Materials and world simulation

The simulation represents a compact set of properties: density, hardness, fracture threshold, flammability, ignition temperature, thermal conductivity, electrical conductivity, porosity, liquid permeability and arcane affinity. Materials include stone, timber, iron, glass, water, oil, ice, combustible gas and crystal. Interactions use simplified deterministic rules, not scientific molecular simulation. The world is chunked into destructible volumes; only active regions receive high-frequency updates. Structural collapse, fluid flow and heat propagation may use approximations to preserve performance.

## Readability and fairness

Provide inspection tools revealing key material properties, visual cues for dangerous pressure and heat, reaction logs in the workshop, undo/reset during testing, and consistent failure explanations. The player must be able to reason about a reaction before it occurs, even when its final scale is surprising.

# 8. Missions and systemic objectives

Each level is a compact simulation puzzle-box, not a corridor shooter. Objectives include recover an artifact, interrupt a refinery, free a prisoner, cross a flooded mine or disable a fortified observatory. Missions should expose several plausible routes: social/stealth access, direct confrontation, physical bypass, engineering intervention or environmental manipulation. Avoid “unbreakable” walls as a default; use costs, material limits and consequences to gate solutions.

## Vertical slice mission: The Flooded Foundry

An arcane foundry pumps contaminated water through an underground complex. Goal: retrieve a regulator core and escape. Routes: divert water to open a maintenance shaft; overload the furnace to distract guards; construct a conductive trap; freeze a leaking pipe to form a bridge; breach a weak foundation and risk collapse. The same simulation powers all routes. An alarm and escalating structural instability create pressure without enforcing one solution.

# 9. Enemies and AI

Enemy behaviors must respond to the same world states as the player. Early archetypes: armored sentinel (conductive, resistant to blades), ash crawler (heat-resistant, brittle when cooled), maintenance automaton (can be disabled through power routing). AI requires perception, navigation updates after terrain damage, hazard avoidance and limited tactical use of materials. Do not promise fully general intelligence; prioritize credible reactions to a small set of systemic states.

# 10. Progression and economy

Progression emphasizes knowledge and expressive capacity, not pure damage inflation. Unlock module slots, energy storage, sensing tools, workshop instrumentation and reusable blueprints. Reagents are obtained from missions and can be purchased or refined. New modules create combinations with old ones. Optional challenge contracts reward efficiency, low collateral damage and ingenious solutions. Persistent blueprints let players retain successful inventions and share their designs.

# 11. Presentation and viral moments

Visual identity: tactile Renaissance machinery, brass instruments, chalk runes, stained glass, grim stone architecture and luminous magical reactions. Avoid generic blocky Minecraft aesthetics. Show voxel structure mainly when surfaces fracture, melt or collapse; use material shaders and strong silhouettes. Viral promise: a ten-second clip where a small invention triggers an understandable cascade, ideally with visible setup and payoff. Add replay camera and blueprint-sharing after the core loop works.

# 12. Technical strategy

Prototype using a small voxel arena and fixed-step simulation where practical. Design the simulation as server-authoritative from day one, including in offline solo mode. Separate render mesh, collision geometry, material state and reaction graph. Use chunk dirty flags, active-cell budgets, capped propagation and simulation LOD. Treat structural integrity, liquids and thermal fields as independent subsystems with explicit interfaces. Log every reaction event with source, target, energy and reason. This supports debugging, automated testing and reproducible player reports.

## AI-assisted development

Use coding agents for isolated subsystems with contracts and regression tests; agents that play the Windows build to validate scenarios; procedural generation of material-combination test cases; automated profiling across scene seeds; and telemetry that identifies degenerate strategies, softlocks and unintended infinite reactions. Keep architecture review and gameplay judgment human-led. Reverse engineer observable mechanics of inspirations where lawful, but implement original code, art and content.

# 13. Prototype gates and milestones

Gate 0 — technical spike (1–2 weeks): one destructible room, eight materials, heat/electric/force interactions, profiling and deterministic replay. Gate 1 — playable toy (3–5 weeks): five spell modules, a workbench, three reactive obstacles, one enemy, save/load blueprints. Gate 2 — vertical slice (8–12 additional weeks): Flooded Foundry mission, 12–20 modules, progression, art direction, UX feedback, capture-ready moments. Timelines are hypotheses for an AI-heavy small team, not committed schedules.

## Success criteria

At least three genuinely different solutions to each of five test challenges; a first-time player creates a novel useful combination within 30 minutes; observers can explain why a spectacular chain reaction happened; no common reproducible softlocks; simulation stays within an agreed frame-time budget on target hardware; external testers voluntarily experiment beyond objectives.

# 14. Risks and mitigations

Performance explosion → active-region budgets and hard caps. Unreadable outcomes → inspectable properties and event feedback. Dominant universal spells → situational resistances and resource tradeoffs. Physics exploits → classify as fun, harmful or progression-breaking. AI-generated code fragility → automated tests, subsystem ownership and profiling gates. Scope creep → no open world, no multiplayer, no full chemistry, no procedural campaign in the first slice. Visual sameness → strong material language and architecture rather than exposed cubes.

# 15. Explicit non-goals

Not a scientifically accurate chemistry simulator. Not a voxel-engine technology demo. Not a survival crafting sandbox at launch. Not a content-heavy RPG. Not an MMO or competitive PvP game. Not an unrestricted spell scripting language that requires coding knowledge.

# 16. Open design decisions

Perspective: FPP is default, but a limited third-person camera could improve destruction readability. Structure: authored puzzle-box missions versus roguelite expeditions. Combat intensity: tactical systemic encounters versus action-heavy shooter. Spell editor: node graph versus compact modular slots. World persistence: per-mission only versus a persistent home region. Business model: premium launch, with modding/blueprint sharing as later retention features.

# 17. Recommendation and next step

Build the smallest prototype that proves one thesis: players enjoy constructing a spell, predicting a reaction, and discovering an unexpected but intelligible result. The first demo should have one room, one door, one enemy and at least five distinct solutions. If that is not fun, adding voxels, content or procedural generation will not rescue the concept.

# 18. Multiplayer vision and player experience

Cooperative expeditions for 2–4 players are the preferred multiplayer mode. Solo play must remain complete and balanced. The central co-op fantasy is collaborative invention followed by unpredictable field execution: one engineer redirects water, another charges the conductive surface, and a third must contain the resulting disaster. Multiplayer amplifies systemic creativity rather than merely multiplying enemy health.

## Co-op mechanics

Shared workshop: players exchange blueprints, jointly prototype devices, and review causal reaction logs. Complementary specializations: thermal, electrical, mechanical, and transmutation knowledge provide different efficiencies and instrumentation, without locking players into classes. Linked spells: independent modules can interact through world properties rather than bespoke combination recipes. Emergency recovery: players stabilize unstable reactors, rescue teammates, or reroute power under pressure.

## Session structure and social design

Start with private invite-based sessions and one shared mission instance. Mission rewards and discoveries are recorded per player; workshop blueprints can be voluntarily shared. Friendly-fire and destructive griefing require explicit host-configurable rules. No competitive PvP, public matchmaking, or persistent shared-world servers in the initial scope.

# 19. Networked simulation architecture

A host or dedicated authoritative server owns world state, reaction resolution, collision outcomes, inventory changes, and mission objectives. Clients send player inputs and spell/device commands, render local prediction for movement and audiovisual effects, and reconcile against authoritative updates. Offline solo runs the same simulation contract without a network transport.

## Replication strategy

Replicate player inputs and high-level spell events; send compressed chunk diffs and material-state deltas only for changed, relevant regions. Use interest management around players and active reactions. Simulate heat, fluid, and propagation on bounded active-region budgets at lower rates than player movement. Provide periodic snapshots, sequence numbers, checksums, and recovery for late join or desynchronization. Do not assume cross-platform floating-point determinism or rely exclusively on lockstep replay.

## Technical constraints and safeguards

Cap concurrent reaction fronts, per-tick voxel edits, fluid volume updates, and debris objects. Separate authoritative gameplay material state from client-only particles. Define network bandwidth, latency, CPU, and memory budgets and measure them under adversarial chain reactions. Protect against malformed spell graphs, unbounded loops, excessive spawn rates, and duplicate client commands. Design save/load and rollback boundaries for host disconnects; host migration is deferred.

# 20. Multiplayer validation gates

Gate M0 — architectural proof: headless simulation process, serializable commands, repeatable scenario tests, two independent local clients, and consistent world snapshots. Gate M1 — two-player foundry: two remote clients can collaboratively create a water/electricity chain reaction and see consistent damage and terrain state under simulated latency and packet loss. Gate M2 — four-player stress: simultaneous spells, explosions, liquid movement, join-in-progress snapshot, and bounded CPU/network usage. Multiplayer is not greenlit for release until M1 passes with acceptable performance; M2 is required before committing to four-player support.

## Co-op playtest acceptance criteria

Players must discover at least two useful cross-player interactions without a scripted tutorial; understand the cause of major shared disasters; complete the vertical slice both solo and as a duo; and encounter no persistent divergence in authoritative mission-critical geometry. Quantitative thresholds for latency, bandwidth, and simulation budgets are to be established by profiling the first headless prototype, not invented upfront.

# 21. Revised production priorities and decisions

Priority 1: prove readable systemic magic and destructible materials in solo play. Priority 2: prove the same core systems support two simultaneous actors with an authoritative simulation. Priority 3: validate that cooperative invention creates meaningful additional fun, not just chaos. Priority 4: expand to four players only after profiling and testing. The launch promise remains conditional until networking feasibility is established.

Decisions still open: listen-server versus dedicated hosting; solo/co-op balancing; how workshop progression persists across sessions; whether joining an active expedition is supported at launch; limits on friendly fire; whether large fluid simulations should use approximate coarse-grained representations.
