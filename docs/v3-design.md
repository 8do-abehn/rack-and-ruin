# Rack & Ruin v3.0 - Design Document

## Vision
A time-management datacenter sim where computing laws drive the physics and story.

---

## Core Gameplay Loop

1. **Requests queue up** (right side) - like orders in a kitchen
2. **Queue overflow = game over** (replaces team satisfaction)
3. **Select job** → hardware appears at dock (left)
4. **Workers carry equipment** → install in racks
5. **Manage budget, staff, power, cooling** to keep up with demand

---

## Computing Laws as Game Physics

### Moore's Law (Compute doubles every ~2 years)
**Mechanic:** Hardware generations across 30 years

| Year | Gen | Compute/U | Era | Story Beat |
|------|-----|-----------|-----|------------|
| 1995 | 1 | 1x | Pentium Pro | "Welcome to the web era. Sun boxes and Cisco routers." |
| 1997 | 2 | 2x | Pentium II | "Slot 1 arrives. The web is hungry." |
| 1999 | 3 | 4x | Pentium III | "Dotcom money flows. Everyone wants servers." |
| 2001 | 4 | 6x | Pentium 4 | "The bubble burst. Do more with less." |
| 2003 | 5 | 8x | Opteron | "Crimson enters the server market. 64-bit is here." |
| 2005 | 6 | 12x | Dual-Core | "Two cores, one socket. Moore delivers again." |
| 2007 | 7 | 20x | Core 2 Quad | "Four cores! But Dennard's ghost awakens..." |
| 2009 | 8 | 30x | Nehalem | "Memory controllers on-die. The cloud is coming." |
| 2011 | 9 | 45x | Sandy Bridge | "Cloudscape launches. Should you be worried?" |
| 2013 | 10 | 70x | Ivy Bridge | "Containers are the new VMs. Everything changes." |
| 2015 | 11 | 100x | Broadwell | "NVMe arrives. Storage will never be the same." |
| 2017 | 12 | 150x | Skylake | "Crimson EPYC: 32 cores per socket. Indigo sweats." |
| 2019 | 13 | 250x | EPYC Rome | "64 cores. The power wall looms." |
| 2021 | 14 | 400x | Milan/Alder | "The AI team forms. They want everything." |
| 2023 | 15 | 700x | Emerald/Genoa | "LLMs devour your capacity. Moore can't keep up." |
| 2025 | 16 | 1000x | Next Gen | "Three decades. You've witnessed the entire arc." |

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
| 1995 | 1 rack | "A web server. Maybe two for redundancy." |
| 1998 | 1-2 racks | "E-commerce needs databases now" |
| 2001 | 2-3 racks | "Java app servers eat memory for breakfast" |
| 2004 | 2-4 racks | "Three-tier architecture is mandatory" |
| 2007 | 3-5 racks | "SOA means more servers, not fewer" |
| 2010 | 4-6 racks | "Big data! Hadoop clusters everywhere" |
| 2013 | 4-8 racks | "Microservices: 100 containers per app" |
| 2016 | 5-10 racks | "Kubernetes clusters need room to grow" |
| 2019 | 8-15 racks | "ML pipelines: training + inference + data" |
| 2022 | 15-30 racks | "LLMs are hungry beasts" |
| 2025 | 30-50 racks | "The AI team wants a dedicated wing" |

**Story Beat (2010):** "Remember when one rack was 'a lot'? Those were the days."

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

| Year | Cost per GB | Media |
|------|-------------|-------|
| 1995 | $10.00 | SCSI drives |
| 1998 | $2.00 | IDE drives |
| 2001 | $0.50 | ATA drives |
| 2004 | $0.20 | SATA arrives |
| 2007 | $0.10 | SATA II |
| 2010 | $0.05 | SATA III |
| 2013 | $0.03 | Early SSD |
| 2016 | $0.25/GB SSD, $0.02/GB HDD | Flash premium |
| 2019 | $0.10/GB SSD | NVMe mainstream |
| 2022 | $0.05/GB SSD | NVMe Gen4 |
| 2025 | $0.02/GB SSD | Flash wins |

**But:** Denser storage = more heat per U (SSDs run hot under load)

**Story Beat (1998):** "Terabyte storage is science fiction. We measure in gigabytes."
**Story Beat (2012):** "SSDs cost 10x more than disk. Worth it?"
**Story Beat (2020):** "100TB of NVMe needs its own AC unit."

---

## Revenue System

### v1 Revenue Sources

**Rack Utilization** - Paid per rack-day occupied
```
Base rate: $100/rack/day (1995)
Scales with inflation/demand over time
Premium for high-density racks
```

**Job Completion** - Flat fee per job completed
```
Fee scales with job size and complexity
Bonus for on-time delivery
Penalty for SLA misses (partial payment)
```

### Future Revenue Sources (Extensible)
| Source | Description | Ties To |
|--------|-------------|---------|
| **Bandwidth** | Charge per GB egress | Metcalfe's Law |
| **Compute hours** | TFLOPS-hours delivered | Moore's Law |
| **Storage** | Per TB-month | Kryder's Law |
| **Premium SLA** | Higher rates for guaranteed uptime | Risk/reward |
| **Consulting** | Staff expertise sold to teams | Staff system |

### Bandwidth (Future)
Could be both a **constraint** and **revenue source**:

**As Constraint:**
```
Starting (1995): T1 line (1.5 Mbps) - "The whole internet fits through a straw"
Upgrades:
  - T3 (45 Mbps) - 1998+
  - OC-3 (155 Mbps) - 2001+
  - 1 Gbps - 2005+
  - 10 Gbps - 2010+
  - 100 Gbps - 2018+
```

**As Revenue:**
- Charge teams for data transfer
- Egress fees (like cloud providers)
- Ties into Metcalfe's Law - more connected = more valuable

---

## Infrastructure Systems

### Power (DC-Level)
```
Starting (1995): 15 kW capacity - "Enough for 3 racks"
Upgrades:
  - 30 kW panel ($15k) - 1995+
  - 50 kW transformer ($30k) - 1998+
  - 100 kW transformer ($60k) - 2002+
  - 200 kW transformer ($120k) - 2006+
  - 500 kW service ($250k) - 2010+
  - 1 MW substation ($500k) - 2015+
  - 2 MW substation ($900k) - 2020+
  - 5 MW campus power ($2M) - 2023+
```

**Failure mode:** Exceed capacity → breakers trip → random racks go offline

### Cooling (DC-Level)
```
Starting (1995): 10 tons capacity - "Window AC units, basically"
Upgrades:
  - 20 ton CRAC ($20k) - 1995+
  - 40 ton CRAC ($35k) - 1998+
  - 80 ton CRAC ($60k) - 2003+
  - 150 ton chiller ($100k) - 2008+
  - 300 ton chiller plant ($200k) - 2012+
  - 500 ton + free cooling ($350k) - 2016+
  - 1000 ton + liquid assist ($600k) - 2020+
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
| Standard 19" Rack | 1x | 42U | 5kW max | $1,500 | 1995+ |
| Telco Rack (23") | 1x | 42U | 5kW max | $2,000 | 1995+ |
| Deep 19" Rack | 1x | 42U | 10kW max | $3,000 | 2003+ |
| High-Density Rack | 1x | 42U | 15kW max | $4,500 | 2010+ |
| OCP Rack (21") | 1x | 48U | 20kW max | $6,000 | 2016+ |
| Double-Wide HPC | 2x | 42U | 40kW max | $18,000 | 2018+ |
| Liquid-Cooled Rack | 1x | 42U | 50kW max | $30,000 | 2021+ |

**Story Beats:**
- 1995: "Standard 42U racks. EIA-310 compliant. The foundation of everything."
- 2003: "Deeper racks for blade servers. Better cable management."
- 2010: "High-density racks for virtualization. More power, more cooling."
- 2016: "Open Compute racks are here - more U's, better airflow."
- 2018: "The AI team needs DGX boxes. Those don't fit in standard racks."
- 2021: "Air cooling can't keep up. Liquid cooling is the future."

**Rack Mechanics:**
- Must buy racks before placing equipment
- Different racks support different power densities
- Double-wide racks: 2 floor tiles, but fit oversized HPC gear
- Liquid-cooled: Required for highest-end GPU nodes (Gen 5+)

---

## Datacenter View

### Stats Sidebar
**Teraflop Counter** - Running total of datacenter compute power
| Era | Typical TFLOPS | Reference |
|-----|----------------|-----------|
| 1995 | 0.001 | "A gigaflop feels fast" |
| 2000 | 0.01 | "Approaching teraflop territory" |
| 2005 | 0.1 | "A teraflop datacenter!" |
| 2010 | 1.0 | "One teraflop. Cray would be proud." |
| 2015 | 10 | "Ten teraflops. A supercomputer from 2005." |
| 2020 | 100 | "A hundred teraflops. Petascale dreams." |
| 2025 | 1000+ | "A petaflop. You've built a national lab." |

**Other Stats:**
- Total power draw (kW/MW)
- Cooling capacity vs usage
- Budget remaining
- Staff count by specialty
- Jobs completed this year

### Default View
- Grid floor showing all racks
- Starts with 3 racks (1995 starting point)
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

### Act 1: The Web Era (1995-2000)

#### 1995 - The Beginning
> "Your first datacenter: 3 racks, 10kW, and a T1 line. The web awaits."

#### 1996 - Growing Pains
> "Netscape is king. Everyone wants a website. Everyone wants servers."

#### 1997 - E-Commerce Dawn
> "They want to sell things online. Time for redundancy."

#### 1998 - The Boom
> "VC money flows like water. Build it and they will come."

#### 1999 - Y2K Prep
> "Two-digit years were a mistake. Upgrade everything. NOW."

#### 2000 - Peak Madness
> "Pets.com has more servers than customers. This can't last."

### Act 2: The Crash & Recovery (2001-2006)

#### 2001 - The Reckoning
> "The bubble burst. Half your customers vanished overnight."

#### 2002 - Survival Mode
> "Do more with less. Consolidation is the word of the year."

#### 2003 - AMD Rising
> "Crimson Opteron arrives. 64-bit changes everything."

#### 2004 - Virtualization
> "One physical server, many virtual ones. Why didn't we do this sooner?"

#### 2005 - Dual Core
> "Two cores per socket. Moore's Law delivers again."

#### 2006 - Dennard's End
> "The engineers call it 'the power wall.' Clocks stopped scaling."

### Act 3: The Cloud Era (2007-2014)

#### 2007 - AWS Arrives
> "Cloudscape offers servers by the hour. Is this the future?"

#### 2008 - Crisis Mode
> "The economy crashes. Budgets freeze. Make it work."

#### 2009 - Recovery
> "Nehalem arrives. Memory on-die. Everything gets faster."

#### 2010 - Big Data
> "Hadoop, MapReduce, 'data lakes.' Buzzwords everywhere."

#### 2011 - Cloud Pressure
> "Cloudscape is cheap. Your users are tempted."

#### 2012 - SSD Revolution
> "Flash storage changes the game. Spinning rust looks ancient."

#### 2013 - Containers
> "Docker launches. VMs suddenly feel heavy."

#### 2014 - Kubernetes
> "Container orchestration. The new virtualization."

### Act 4: The Modern Era (2015-2020)

#### 2015 - NVMe Arrives
> "PCIe storage. Your SANs look like dinosaurs."

#### 2016 - Crimson's Comeback
> "EPYC is coming. Indigo won't have the market to itself."

#### 2017 - Core Wars
> "32 cores per socket. The server wars heat up."

#### 2018 - Security Crisis
> "Spectre. Meltdown. Patch everything. Performance tanks."

#### 2019 - GPU Awakening
> "ML teams want GPUs. Not one. Dozens."

#### 2020 - Remote Everything
> "The world works from home. Your datacenter is critical infrastructure."

### Act 5: The AI Age (2021-2025)

#### 2021 - AI Team Arrives
> "They've formed an AI team. Brace yourself."

#### 2022 - GPU Wars
> "GPU allocations are now political. Everyone wants Emerald cards."

#### 2023 - LLM Explosion
> "Language models consume everything. Amdahl weeps."

#### 2024 - Scale Crisis
> "The AI team wants 100 racks for one model. Someone do the math."

#### 2025 - Victory
> "Three decades. From a T1 to a teraflop titan. Legend."

---

## Queue Mechanics (Order Queue Style)

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
- Abstract "racks" → Visual 42U racks
- Instant placement → Worker animations
- Static view → Zoomable datacenter

Add:
- Power meter & upgrades
- Cooling meter & upgrades
- Equipment catalog
- Dock/staging area
- Order queue (time management style)

---

## Future DLC: The Cray Years (1976-1994)

**Unlocked after:** Completing the main campaign (1995-2025)

### Concept
Go back to the era before standard racks. Manage a supercomputer center through the golden age of vector processing.

### Timeline

#### 1976 - The Beginning
> "Seymour's masterpiece arrives. The Cray-1. 160 MFLOPS. It looks like a couch."

#### 1982 - Vector Wars
> "The X-MP: two processors, shared memory. Parallel computing begins."

#### 1985 - Liquid Dreams
> "The Cray-2. Four processors. Submerged in Fluorinert. 1.9 GFLOPS."

#### 1988 - Competition
> "The Y-MP arrives. But so do the Japanese. Fujitsu and NEC want the crown."

#### 1993 - The End of an Era
> "Seymour's gone independent. The T3D is the future. Massively parallel."

### Unique Mechanics

**No Standard Racks**
- Custom installations for each machine
- Liquid cooling from day one
- Raised floor mandatory (cable management nightmare)

**Floating Point Focus**
- MFLOPS/GFLOPS as primary metric
- Vector vs scalar workloads
- Weather simulation, nuclear physics, oil exploration

**Government Contracts**
- Labs and agencies as customers
- Security clearances required
- Budget cycles tied to fiscal year

### Equipment

| Machine | Year | FLOPS | Power | Cooling | Cost |
|---------|------|-------|-------|---------|------|
| Cray-1 | 1976 | 160M | 115kW | Freon | $8M |
| Cray X-MP | 1982 | 400M | 145kW | Freon | $15M |
| Cray-2 | 1985 | 1.9G | 195kW | Fluorinert | $17M |
| Cray Y-MP | 1988 | 2.7G | 150kW | Freon | $20M |
| Cray C90 | 1991 | 16G | 200kW | Freon | $25M |
| Cray T3D | 1993 | 150G | 350kW | Air+Liquid | $30M |

### Story Arc
Follow the rise and challenges of running a national lab computing center through:
- The Cray dominance era
- Japanese supercomputer competition
- The shift from vector to parallel
- Seymour Cray's departure and tragic end

### Victory Condition
> "1994. The T3D cluster hums. Massively parallel is the future. But those beautiful vector machines... they were something special."

---

## Future: Scoreboard System

### Design Goals
- **Extensible**: Easy to add new metrics without breaking existing scores
- **Backwards Compatible**: Old saves work with new metric additions
- **Comparable**: Scores meaningful across different campaign lengths

### Score Schema (v1)
```json
{
  "version": 1,
  "timestamp": "ISO8601",
  "campaign": {
    "startYear": 1995,
    "endYear": 2025,
    "length": 30
  },
  "metrics": {
    "jobsCompleted": 0,
    "jobsMissed": 0,
    "peakTFLOPS": 0,
    "peakRacks": 0,
    "peakStaff": 0,
    "totalRevenue": 0,
    "totalSpent": 0,
    "avgSatisfaction": 0,
    "minSatisfaction": 100,
    "eventsHandled": 0,
    "upgradesPurchased": 0,
    "teamsRetained": 4
  },
  "achievements": [],
  "extensionData": {}
}
```

### Backwards Compatibility Rules
1. **New metrics default to null/0** - Missing fields get sensible defaults
2. **Never remove fields** - Deprecate instead (prefix with `_deprecated_`)
3. **Version field required** - Schema migrations based on version number
4. **extensionData bag** - Future additions can use this without schema changes

### Potential Future Metrics
- Power efficiency (TFLOPS/kW)
- Carbon footprint
- Uptime percentage
- Staff turnover rate
- Cloud migrations prevented
- Hardware failures survived
- Speed run time (real-world minutes)

### Leaderboard Categories
| Category | Metric | Description |
|----------|--------|-------------|
| Efficiency King | TFLOPS/kW | Best power efficiency at end |
| Job Machine | jobsCompleted | Most jobs completed |
| Staff Minimalist | peakStaff | Lowest staff at victory |
| Budget Hero | totalSpent | Lowest spending at victory |
| Satisfaction Pro | avgSatisfaction | Highest average satisfaction |
| Speed Runner | realTimeMinutes | Fastest completion |
| Survivor | eventsHandled | Most crises weathered |
