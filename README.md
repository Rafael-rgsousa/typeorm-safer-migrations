---

# TypeORM Column Type Change Migration Script

## Problem

When altering column types in a TypeORM-managed database, TypeORM often drops and recreates the column, resulting in data loss or corruption if not handled properly.

## Solution

This migration script assists in preserving data integrity during column type changes by backing up and restoring specific columns before and after the migration.

## How It Works

1. **Backup Phase:** The script reads the specified columns' data and stores it in temporary tables.
2. **Migration Phase:** TypeORM performs the column type change, which deletes and recreates the column.
3. **Restore Phase:** The script retrieves the backed-up data and restores it to the new column.

## Usage

1. **Installation:**
   - Clone or download this repository to your local environment.

2. **Configuration:**
   - Edit your migration file and add the configuration for the `up` and `down` functions.
   - Set the `tableConfigs` array to specify the table name, primary key columns, and backup columns for each table you want to migrate.

3. **Run the Migration:**
   - Execute the migration script using TypeORM's migration command:
     ```bash
     yarn typeorm migration:run
     ```
     or
     ```bash
     npm run typeorm migration:run
     ```

4. **Example:**

```typescript
import { MigrationInterface, QueryRunner } from 'typeorm';
import { SaferMigrations, TableBackupConfig } from '../safer-migrations';

export class ExampleMigration implements MigrationInterface {
  name = 'MigrationName123123123';

  tableConfigs: TableBackupConfig[] = [
    {
      tableName: 'table_name',
      primaryKeyColumns: ['primary_key_column'],
      backupColumns: ['column1', 'column2', 'column3'],
    },
  ];

  public async up(queryRunner: QueryRunner): Promise<void> {
    await SaferMigrations.start(queryRunner, this.tableConfigs);

    // your migration code here...

    await SaferMigrations.done(queryRunner, this.tableConfigs);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await SaferMigrations.start(queryRunner, this.tableConfigs);

    // your migration code here...

    await SaferMigrations.done(queryRunner, this.tableConfigs);
  }
}
```

### Explanation:

- **Configuration:**
  - `tableConfigs` is an array of `TableBackupConfig` objects. Each object specifies:
    - `tableName`: The name of the table.
    - `primaryKeyColumns`: An array of primary key column names.
    - `backupColumns`: An array of column names to back up.

- **Migration Methods:**
  - `SaferMigrations.start(queryRunner, this.tableConfigs)`: Backs up the specified columns.
  - `SaferMigrations.done(queryRunner, this.tableConfigs)`: Restores the backed-up columns after the migration changes.

This setup ensures that your data is backed up before any column type changes and restored afterward, maintaining data integrity throughout the migration process.