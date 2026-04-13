# /migration - Data Migration Strategy

Plan zero-downtime schema migrations and data transformations.

## Usage
```
/migration plan <change>       # Plan zero-downtime migration
/migration rollback <version>  # Design rollback strategy
/migration seed <entity>       # Design seed data
```

## /migration plan <change>

### Expand-Contract Pattern (Zero Downtime)
```
Phase 1 - EXPAND: Add new column/table (backward compatible)
  -> Deploy code that writes to BOTH old + new
Phase 2 - MIGRATE: Backfill existing data to new structure
  -> Verify data consistency
Phase 3 - CONTRACT: Remove old column/table
  -> Deploy code that only uses new structure
```

Each phase = separate deployment. Database always works between steps.

### Migration Checklist
- [ ] Migration script written and tested on staging
- [ ] Rollback script written and tested
- [ ] Data volume estimated (how long will migration take?)
- [ ] Lock impact assessed (will it block reads/writes?)
- [ ] Backup taken before migration
- [ ] Monitoring in place during migration
- [ ] Communication plan (if downtime expected)

### Common Patterns
- **Rename column**: Add new -> copy data -> update code -> drop old
- **Change type**: Add new column -> migrate data -> update code -> drop old
- **Split table**: Create new table -> dual write -> backfill -> switch reads -> remove old writes
- **Merge tables**: Create target -> copy from both -> update code -> drop sources

## /migration rollback <version>
Every migration MUST have a rollback:
- DOWN migration that reverses the UP
- Data restoration plan (from backup if needed)
- Code rollback (previous version compatible with both schemas)
- Maximum rollback window (after which rollback is no longer safe)

## /migration seed <entity>
Design seed data:
- **Lookup tables**: countries, currencies, roles, statuses
- **Default config**: system settings, default tenant
- **Test data**: realistic but fake data for development
- **Demo data**: curated data for demos/presentations

## Examples
```
/migration plan add-tenant-id-to-all-tables
/migration rollback 005
/migration seed Country
```
