Technical Design Document \| v0.1 \| 15 October 2026

English engineering baseline derived from Concept Document v0.2. Proposed architecture; not implemented. Numeric budgets are hypotheses to profile.

# 1. Product and engineering constraints

First-person systemic fantasy immersive sim; programmable magic, voxel destruction, reactive materials and optional 2–4 player co-op. Windows PC first. Solo uses the same authoritative simulation as multiplayer. First playable: The Flooded Foundry.

- Headless-capable simulation separated from rendering and user interface.

- Server authoritative for gameplay; client-side visual prediction only.

- Bounded reactions and spells to guarantee finite work.

- One coherent interaction model for terrain, machines, enemies and spells.

- Prefer explainable game physics over expensive physical accuracy.

# 2. Component architecture

| Component          | Responsibility                                                |
|--------------------|---------------------------------------------------------------|
| WorldStore         | Chunk persistence, occupancy queries, revisions               |
| MaterialSim        | Heat, fluid, gas, combustion, conductivity, phase transitions |
| StructuralSim      | Support connectivity, collapse and debris conversion          |
| SpellCompiler / VM | Typed spell graphs, validation and bounded execution          |
| Actors / AI        | Movement, damage, pathfinding, objectives                     |
| NetHost            | Command validation, interest management, delta replication    |
| Client             | Input, prediction, mesh generation, VFX, audio and UI         |
| Headless QA        | Scenario runner, deterministic fixtures, fuzzing and replay   |

Fixed simulation tick proposed at 30 Hz; render target 60 FPS. Tick order: validate intents → move actors → execute spells → apply damage/forces → structural edits → material reactions → AI/objectives → replication. Systems communicate through typed commands/events, not direct cross-module mutation.

# 3. World storage and voxel representation

Candidate chunk size 32×32×32 cells, voxel pitch 0.25–0.5 m for prototype. Chunk coordinate, monotonically increasing revision, palette-compressed material IDs, occupancy, dynamic overlay and dirty masks. Sparse dynamic overlays avoid allocating temperature/fluids for untouched cells. Collision queries read authoritative occupancy even if render mesh regeneration is delayed.

| Record      | Illustrative fields                                                        |
|-------------|----------------------------------------------------------------------------|
| Voxel       | material_id:u16, fill:u8, flags:u8, temp_bucket:u16                        |
| Chunk       | coord:i32\[3\], revision:u64, palette, cells, dirty_mask                   |
| MaterialDef | density, hardness, thermal, ignition, conductivity, phase thresholds, tags |
| WorldEvent  | tick:u64, sequence:u64, source, bounds, payload, version                   |

Chunk writes are transactions with source, bounds and revision. Meshing jobs run asynchronously and discard results if revisions change. Active-region management wakes cells near players, spells and unstable reactions; dormant chunks receive no per-tick updates.

# 4. Hybrid material simulation

Do not simulate full continuum physics at voxel resolution. Solids use discrete occupancy and support; heat and fluid/gas transport use coarser grids and bounded active queues. Simulate critical interactions locally at higher fidelity; distant effects become aggregated or cosmetic.

- Heat: bounded neighbor diffusion, energy accounting and timestep clamps.

- Fire: ignition threshold, fuel and oxygen tags, heat release, smoke and residue.

- Water: mass-conserving cellular gravity flow, with approximate pressure equalization.

- Gas: coarse diffusion/buoyancy; avoid full CFD.

- Electricity: bounded traversal of connected conductive materials and wet actors.

- Phase changes: thresholds with hysteresis to prevent rapid toggling.

- Magical transmutation: explicit conversion rules, resource costs and exception tags.

Reaction schema: reactant predicates + environmental conditions + consumption + products + heat/impulse + priority + maximum work + cooldown. Example: oil + ignition + oxidizer produces flame, smoke and heat. All interactions use one event queue with per-tick budgets and overflow continuation.

# 5. Destruction and structural support

Track local support connectivity to anchored substrate and designated load-bearing nodes. After edits, invalidate nearby support components and process bounded incremental updates. Detached regions convert into clustered rigid bodies or simplified rubble, not thousands of individual physics objects. Cap detached bodies and collapse work per tick. Navigation tiles rebuild asynchronously with fallback steering.

# 6. Programmable magic compiler and runtime

Spells are typed directed graphs compiled into immutable intermediate representation. Node groups: Trigger (cast, impact, timer), Target (raycast, sphere, connected material), Logic (condition, delay, branch), Effect (heat, cool, charge, impulse, transmute), Modifier (spread, duration, efficiency).

Example: ON_IMPACT → CONNECTED_CONDUCTOR(radius=6 m) → ELECTRIC_PULSE(energy=40) → IF_WET(multiplier=1.5). Compiler performs schema/type checks, rejects unbounded cycles, estimates worst-case work, and emits a content hash. Server independently validates loadouts.

- Hard limits: graph nodes, recursion/trigger depth, target count, radius, voxel edits, energy and events per cast.

- Feedback: spell inspector shows energy flow, target materials, execution trace and failure reasons.

- Balance: mana, heat, instability, module slots, casting time and cooldowns.

- Security: never run arbitrary native code or user scripts from spell definitions.

# 7. Actors, combat and AI

Actors expose material properties (temperature, wetness, conductivity, mass, armor, flammability) through the same interaction API as terrain. AI selects goals with utility scoring and behavior trees; spell reactions remain systemic. Prototype enemies: metal Sentinel, flammable Spore Crawler and heavy Stone Guardian. Each must support two different systemic defeat strategies.

# 8. Multiplayer topology and authority

Listen-server acceptable for first playable, but host is headless-compatible and dedicated-server-ready. Clients send timestamped movement and cast intents, not authoritative voxel modifications. Host owns world, damage, inventories, mission state and spell execution. Solo runs host locally. Host migration and PvP are out of scope.

| State             | Replication                                                        |
|-------------------|--------------------------------------------------------------------|
| Players / enemies | Frequent transforms, prediction/interpolation and reconciliation   |
| Voxel terrain     | Versioned chunk deltas, compact edit batches, occasional snapshots |
| Reactions         | Authoritative event IDs and state outcomes; cosmetic prediction    |
| Fluids / heat     | Prioritized region summaries; no per-cell network stream           |
| Late join         | Baseline chunk/actor snapshot then replay post-snapshot deltas     |

Interest management subscribes to nearby chunks and relevant distant events. Reliable ordered delivery for baselines and critical events; sequenced unreliable updates for high-frequency motion. Dedupe events using session sequence IDs. Compare region checksums and request resync on mismatch; never assume cross-platform bitwise determinism.

# 9. Initial measurable performance targets

| Metric                   | Prototype hypothesis                                           |
|--------------------------|----------------------------------------------------------------|
| Authoritative simulation | 30 Hz fixed tick (33.3 ms total budget)                        |
| Client rendering         | 60 FPS target on defined reference hardware                    |
| Players                  | 2 initially, 4 as expansion gate                               |
| Typical downstream       | Aim under 100 KB/s per client; measure spikes                  |
| Network tests            | 50/100/200 ms RTT, 1–5% packet loss                            |
| Simulation safety        | No unbounded reaction queues or unrecoverable state divergence |

Telemetry: active cells, simulation time by subsystem, pending reaction count, mesh latency, support rebuild time, physics body count, delta bytes, reconciliation frequency, 1% low FPS and memory peak. All performance claims require reference hardware and repeatable benchmarks.

# 10. Save format, tools and observability

Save world seed, schema version, chunk snapshots/deltas, actor state, mission progress, spell IR hashes and checkpoint tick. Atomic checkpoints, checksums and migration tooling. Expose inspectors for material fields, support graph, spell trace, active-region heatmap, chunk revisions and replication mismatches.

# 11. Automated QA and AI-assisted development

Headless scenario API: reset(seed), spawn(entity/material), issue(command), step(ticks), query(region), assert(invariant), export(replay). Agents generate test cases and play scenarios; hard validators decide correctness. Record seed, build hash, inputs, event trace and snapshots on failure.

| Suite           | Required tests                                                          |
|-----------------|-------------------------------------------------------------------------|
| Properties      | Nonnegative mass; bounded energy; valid material IDs; spell work limits |
| Regression      | Golden command traces and expected critical world states                |
| Fuzz            | Random spell graphs, explosions, fluid/heat combinations                |
| Networking      | Packet loss, duplicated events, join during collapse, reconnect         |
| Soak            | Repeated save/load, long reactions, region activation churn             |
| Gameplay agents | Foundry solved using multiple independent strategies                    |

# 12. Vertical slice: The Flooded Foundry

Recover a sealed arcane core from a damaged foundry. Environmental obstacles: flooded chamber, conductive rails, obstructed furnace passage and compromised supports. Valid solutions include draining/freezing water, routing electrical energy to machinery, breaching a side passage, or stabilizing supports. No single scripted solution path.

Measure route diversity, unexpected combinations, spell iterations, deaths, mission time, network correction counts and player comprehension. External testers should discover at least two distinct unprompted approaches.

# 13. Implementation gates

| Gate          | Deliverable / exit test                                           |
|---------------|-------------------------------------------------------------------|
| G0 Foundation | Headless host, chunk store, replay, telemetry; reproducible edits |
| G1 Materials  | 8 materials, 6 effects, bounded reactions; invariants pass        |
| G2 Magic      | Graph editor/compiler/VM; 3 useful player-made combinations       |
| G3 World      | Structural collapse, nav update and systemic Foundry puzzles      |
| G4 Network    | 2-player host, deltas, snapshots; 100 ms RTT test passes          |
| G5 Playtest   | External players create unexpected but understandable solutions   |

# 14. Risks and mitigation

| Risk                            | Mitigation                                                   |
|---------------------------------|--------------------------------------------------------------|
| CPU spikes from chains          | Active region caps, coarse simulation, budget queues         |
| Network burst on collapse       | Batch/compress deltas, prioritize, snapshot fallback         |
| Incomprehensible interactions   | Strong visual feedback, inspector, simplified rules          |
| One dominant spell combo        | Energy/instability costs and varied material counters        |
| AI-generated regressions        | Contract tests, replay CI and human code review              |
| Voxel novelty without retention | Validate mission loop and progression before content scaling |

# 15. Open decisions and first engineering spike

- Compare Unreal vs custom Rust/Vulkan vs hybrid against headless, voxel editing, co-op replication and iteration speed.

- Benchmark voxel pitch, chunk dimensions, fluid resolution and support graph cost on reference hardware.

- Choose gameplay camera, spell editor UX and friendly-fire policy after player tests.

- Decide persistence scope and dedicated-server hosting after two-player validation.

First spike (timeboxed): implement one destructible room, water/heat/electricity, one composable spell, two networked players and headless replay. Kill or substantially redesign the approach if it cannot sustain bounded simulation, reliable convergence and readable emergent play.

Appendix: engineering definition of done — schema validated; unit/property tests; deterministic scenario fixture; profiling counters; replication tests where applicable; documented failure limits; reviewable source and trace artifacts.
