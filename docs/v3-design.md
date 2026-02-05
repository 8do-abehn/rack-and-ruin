# Rack & Ruin v3.0 - Design Document

## Vision
A Tetris-meets-datacenter management sim where computing laws drive the physics and story.

---

## Core Gameplay Loop

1. **Requests queue up** (right side) - like Tetris pieces waiting
2. **Queue overflow = game over** (replaces team satisfaction)
3. **Select job** → hardware appears at dock (left)
4. **Workers carry equipment** → install in racks
5. **Manage budget, staff, power, cooling** to keep up with demand

---

## Computing Laws as Game Physics

### Moore's Law (Compute doubles every ~2 years)
**Mechanic:** Hardware generations
| Year | Gen | Compute/U | Story Beat |
|------|-----|-----------|------------|
| 2015 | 1 | 1x | "Welcome to the Broadwell era" |
| 2017 | 2 | 2x | "Skylake arrives - double the cores!" |
| 2019 | 3 | 4x | "Crimson EPYC Rome: 64 cores per socket" |
| 2021 | 4 | 8x | "Alder Lake & Milan - Moore keeps delivering" |
| 2023 | 5 | 16x | "Emerald Rapids - but at what power cost?" |

**Physics:** Newer hardware does more work per U slot.

---

### Dennard Scaling (Ended ~2006)
**The Problem:** Transistors shrank but power density now INCREASES.

**Mechanic:** Power per compute unit rises each generation
| Gen | Watts per 1U Server | Watts per GPU Node |
|-----|--------------------|--------------------|
| 1 | 100W | 300W |
| 2 | 150W | 450W |
| 3 | 200W | 700W |
| 4 | 250W | 1000W |
| 5 | 300W | 1500W |

**Story Beat (2019):** "The engineers call it the 'power wall.' Moore giveth, Dennard taketh away."

**Physics:** Must upgrade DC power/cooling to use newer hardware.

---

### Koomey's Law (Efficiency doubles every ~1.5 years)
**Mechanic:** Compute per watt improves, partially offsetting Dennard

| Gen | Compute/Watt |
|-----|--------------|
| 1 | 1.0x |
| 2 | 1.5x |
| 3 | 2.2x |
| 4 | 3.3x |
| 5 | 5.0x |

**Physics:** Newer gens are more efficient, but you still need more power because you're packing in more compute.

**Story Beat (2021):** "The new chips do more per watt, but the AI team wants 10x the chips..."

---

### Wirth's Law ("Software bloats faster than hardware improves")
**Mechanic:** Team demands grow each year

| Year | Typical Job Size | Why |
|------|------------------|-----|
| 2015 | 3-4 racks | "Docker is new, VMs are king" |
| 2017 | 4-6 racks | "Kubernetes clusters everywhere" |
| 2019 | 6-8 racks | "Big data pipelines" |
| 2021 | 8-12 racks | "AI training needs scale" |
| 2023 | 12-20 racks | "LLMs are hungry beasts" |

**Story Beat (2020):** "Remember when 4 racks was a 'big' deployment? Those were the days."

**Physics:** This is the core pressure - Moore helps but Wirth eats it all.

---

### Metcalfe's Law (Network value = nodes²)
**Mechanic:** Adjacency bonuses for same-row placement

| Racks in Row | Network Bonus |
|--------------|---------------|
| 2 | +5% speed |
| 3 | +10% speed |
| 4 | +20% speed |
| 5 | +35% speed (full row) |

**Physics:** Jobs complete faster when racks share a top-of-rack switch.

**Story Beat:** "The network team insists - latency matters. Same row or no deal."

---

### Amdahl's Law (Diminishing returns on parallelism)
**Mechanic:** Large jobs have efficiency penalties

| Job Size | Efficiency |
|----------|------------|
| 1-4 racks | 100% |
| 5-8 racks | 95% |
| 9-12 racks | 85% |
| 13-16 racks | 70% |
| 17+ racks | 50% |

**Physics:** Huge deployments waste resources on coordination overhead.

**Story Beat (2023):** "The AI team wants 40 racks for one model. Someone explain Amdahl's Law to them."

---

### Kryder's Law (Storage density doubles yearly)
**Mechanic:** Storage costs decrease over time

| Year | Cost per TB | Media |
|------|-------------|-------|
| 2015 | $100 | Spinning disk |
| 2017 | $50 | Hybrid arrays |
| 2019 | $25 | SATA SSD |
| 2021 | $15 | NVMe |
| 2023 | $10 | NVMe Gen4 |

**But:** Denser storage = more heat per U (SSDs run hot under load)

**Story Beat (2018):** "Kryder's Law is beautiful - until you realize 100TB of NVMe needs its own AC unit."

---

## Infrastructure Systems

### Power (DC-Level)
```
Starting: 50 kW capacity
Upgrades:
  - 100 kW transformer ($50k)
  - 200 kW transformer ($100k)
  - 500 kW transformer ($200k)
  - 1 MW substation ($500k)
```

**Failure mode:** Exceed capacity → breakers trip → random racks go offline

### Cooling (DC-Level)
```
Starting: 40 tons capacity
Upgrades:
  - 80 ton CRAC ($40k)
  - 150 ton CRAC ($80k)
  - 300 ton chiller plant ($150k)
  - 500 ton chiller + free cooling ($300k)
```

**Failure mode:** Exceed capacity → thermal throttling → jobs slow down → queue backs up

### Rack-Level Cooling (Late Game Upgrade)
- In-rack cooling units: +5 kW cooling per rack
- Required for high-density GPU deployments
- Story: "The AI team's racks are literally melting. Time for in-row cooling."

---

## Equipment Catalog

### Compute
| Item | Size | Power | Compute | Gen |
|------|------|-------|---------|-----|
| Entry Server | 1U | 100W | 10 | 1+ |
| Dual-Socket | 2U | 200W | 25 | 1+ |
| Quad-Socket | 4U | 400W | 60 | 2+ |
| GPU Node (4x) | 4U | 1000W | 200 | 3+ |
| GPU Node (8x) | 6U | 2000W | 500 | 4+ |

### Storage
| Item | Size | Power | Capacity | Gen |
|------|------|-------|----------|-----|
| Disk Shelf | 2U | 150W | 100TB | 1+ |
| SSD Array | 2U | 200W | 50TB | 2+ |
| NVMe Array | 2U | 300W | 100TB | 3+ |
| All-Flash | 4U | 500W | 500TB | 4+ |

### Network
| Item | Size | Power | Purpose |
|------|------|-------|---------|
| 1G Switch | 1U | 50W | Basic connectivity |
| 10G ToR | 1U | 100W | Standard rack |
| 25G ToR | 1U | 150W | High bandwidth |
| 100G Spine | 2U | 300W | Rack interconnect |
| Patch Panel | 1U | 0W | Cable management |
| PDU | 0U | 0W | Power distribution |

### Cooling (Rack-Level)
| Item | Size | Power | Cooling |
|------|------|-------|---------|
| In-Row Cooler | 0U (floor) | 500W | +5kW |
| Rear-Door Heat Exchanger | 0U | 100W | +10kW |

### Racks (Must Purchase!)
| Item | Width | Height | Power | Cost | Available |
|------|-------|--------|-------|------|-----------|
| Standard 19" Rack | 1x | 42U | 10kW max | $2,000 | 2015+ |
| Deep 19" Rack | 1x | 42U | 15kW max | $3,500 | 2017+ |
| OCP Rack (21") | 1x | 48U | 20kW max | $5,000 | 2018+ |
| Double-Wide HPC | 2x | 42U | 40kW max | $15,000 | 2020+ |
| Liquid-Cooled Rack | 1x | 42U | 50kW max | $25,000 | 2022+ |

**Story Beats:**
- 2018: "Open Compute racks are here - more U's, better airflow, fits the new gear."
- 2020: "The AI team needs NVIDIA DGX. Those don't fit in standard racks. Time to go double-wide."
- 2022: "Air cooling can't keep up anymore. Liquid cooling is the future."

**Rack Mechanics:**
- Must buy racks before placing equipment
- Different racks support different power densities
- Double-wide racks: 2 floor tiles, but fit oversized HPC gear
- Liquid-cooled: Required for highest-end GPU nodes (Gen 5+)

---

## Datacenter View

### Default View
- Grid floor showing all racks
- Starts with 5 racks (1 row)
- Auto-zooms out as you expand
- Request queue on right
- Dock/staging on left
- Power/cooling meters at top

### Rack Zoom View
- Mouse scroll/pinch to zoom in on racks
- Zoom in far enough → see 42U detail view
- See every piece of equipment
- Drag to rearrange (costs worker time)
- Visual: LEDs blinking, fans spinning

---

## Story Timeline with Laws

### 2015 - The Beginning
> "Your datacenter: 5 racks, 50kW, and dreams. Moore's Law says you'll need more."

### 2016 - First Growth
> "The requests keep coming. Time to expand."

### 2017 - Skylake Era
> "Gen 2 hardware arrives! Double the cores, 50% more power draw. Dennard warned us."

### 2018 - Storage Explosion
> "Kryder's Law delivers cheap SSDs. But your cooling bill just doubled."

### 2019 - The Power Wall
> "Gen 3 chips are amazing. Your power grid... less so. Upgrade or melt."

### 2020 - Cloud Competition
> "Cloudscape offers 'infinite scale.' Your users are tempted. Keep up or lose them."

### 2021 - AI Arrives
> "The AI team has formed. They want GPUs. Lots of GPUs. Wirth's Law incarnate."

### 2022 - Efficiency Push
> "Koomey's Law helps - new chips do more per watt. But the AI team wants 10x more chips."

### 2023 - The Scaling Crisis
> "Someone finally read Amdahl's Law. Turns out 100 racks don't make training 100x faster."

### 2024 - Maturity
> "You've seen it all: Moore, Dennard, Wirth, Amdahl. Now use them."

### 2025 - Victory?
> "A decade of keeping up with the laws of computing. Legend."

---

## Queue Mechanics (Tetris-Style)

### Queue Behavior
- New requests appear at top
- Slide down as time passes
- Hit bottom of queue area = overflow = game over
- Larger jobs take more queue space
- Urgent jobs fall faster

### Visual
```
┌─────────────────┐
│ ░░ New Request  │ ← Appears here
│ ▓▓ ML Training  │
│ ▓▓ (8 racks)    │
│ ░░ Web Servers  │
│ ░░ (2 racks)    │
│ ▓▓ Database     │
│    Cluster      │
│ ▓▓ (4 racks)    │
│ ░░ CI Pipeline  │
├─────────────────┤
│ ▓▓ OVERFLOW ▓▓  │ ← Hit here = game over
└─────────────────┘
```

### Selection
- Click job to select
- Workers grab hardware from dock
- Install in racks you've designated
- Job leaves queue when hardware placed

---

## Tech Stack for v3.0

**Godot 4.x**
- GDScript for game logic
- 2D with isometric tilt
- Tween animations for workers
- Particle effects for cooling/power

**Assets Needed**
- Rack sprites (empty, various fill levels)
- Equipment sprites (servers, switches, storage)
- Worker sprites (4 colors for specialties)
- UI elements (queue, meters, dock)
- Effects (LED blinks, fan spin, heat shimmer)

---

## Migration from v2.0

Keep from v2:
- Budget system
- Staff specialties
- Hardware generations
- Random events
- Story milestones

Replace:
- Team satisfaction → Queue overflow
- Abstract "racks" → Visual 40U racks
- Instant placement → Worker animations
- Static view → Zoomable datacenter

Add:
- Power meter & upgrades
- Cooling meter & upgrades
- Equipment catalog
- Dock/staging area
- Tetris-style queue
