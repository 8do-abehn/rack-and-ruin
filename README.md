# Rack & Ruin

**[Play Now](https://8do-abehn.github.io/rack-and-ruin/)**

A datacenter scheduling simulation game. Manage rack allocations for competing teams (R&D, Sales, Benchmark) across a 10-year timeline (2015-2025).

## How to Play

1. Teams submit requests for rack space with deadlines
2. Click a request to select it, then click racks to place it
3. Keep all teams' satisfaction above 20% or it's game over
4. Survive until the end of 2025 to win

## Rules

- **Same-row placement**: Sales and Benchmark jobs must be placed in a single row (same network switch). R&D can span rows but takes a satisfaction penalty.
- **Sysadmin capacity**: Your team can rack/deprovision 8 racks per day total
- **Deprovisioning**: Completed jobs need cleanup before racks are available again

## Development

```bash
npm install
npm run lint      # Check for issues
npm run format    # Auto-format code
```

## License

MIT
