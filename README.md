# Rack & Ruin

[![CI](https://github.com/8do-abehn/rack-and-ruin/actions/workflows/lint.yml/badge.svg)](https://github.com/8do-abehn/rack-and-ruin/actions/workflows/lint.yml)

**[Play Now](https://rnr.8devops.com)**

A datacenter management simulation game. Manage rack allocations, hire staff, upgrade hardware, and keep competing teams happy across a multi-year campaign (2015-2025+).

## Features

- **4 Teams**: R&D, Sales, Benchmark, and AI/ML (joins mid-game)
- **Staff Management**: Hire specialists (Network, Storage, Compute) or Generalists
- **Hardware Upgrades**: Expand racks, increase capacity, upgrade generations
- **Random Events**: Hardware failures, budget windfalls, security audits
- **Cloud Pressure**: Keep satisfaction up or teams threaten to migrate
- **Variable Campaigns**: Sprint (3yr), Standard (5yr), Marathon (10yr), or Custom

## How to Play

1. Select a campaign length to start
2. Teams submit requests for rack space with deadlines
3. Click a request to select it, then click racks to place it
4. Hire staff to increase daily capacity
5. Upgrade hardware to handle growing demands
6. Keep all teams' satisfaction above 20% or it's game over

## Rules

- **Same-row placement**: Sales and Benchmark jobs must be placed in a single row (same network switch)
- **Staff capacity**: Each specialist adds +4 daily capacity for their domain
- **Deprovisioning**: Completed jobs need cleanup before racks are available again
- **Budget**: Annual allocation for staff salaries and upgrades

## Development

```bash
npm install
npm run lint         # Check for issues
npm run format       # Auto-format code
npm run format:check # CI formatting check
```

## License

MIT
