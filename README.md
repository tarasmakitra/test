SpaceTrak Migrations
====================

I. Available commands
----------------------

1. **`migrate-production`** - migrates all databases, found in `st1_meta` database `accounts` table.
However, some databases from `st1_meta` are excluded because they either don't exist, 
duplicates from `st1_app_dev` or they are legacy databases.
  - *Migration path*: `migrations/production`
  
  - *Affected databases*: All production databases from `st1_meta`, `accounts` table, except of excluded in the list above.
  
  - *Excluded databases*: All production databases from `st1_meta`, except of excluded in the list above:
      * `st1_PDT`
      * `st1_demo`
      * `st1_devapp`
      * `st1_OTA`
      * `st1_RSPi_SPACE`
      * `st1_RSPi_SPACE_test`
      * `st1_u4xko4r9e`

  > **NOTE**: We have some databases that don't follow default naming conventions for databases. Somehow.
  > In `meta` databases we have table `accounts`. It contains fields `account_slug` and `db_name`.
  > By default `db_name` = `st1_` + `account_slug`. For example `account_slug` = `demo`, so `db_name` = `st1_` + `demo` = `st1_demo`.
  > BUT somehow we have couple of databases that don't follow this convention. I don't know why, because this is so 
  > weird and stupid. Here is a list of this databases: `designcorp`, `rspi`. If we will have more such databases in `st1_meta`
  > in future, then we will need to update `config/params.php` file:
  ```php
  <?php
  //add it here in format ['account_slug' => 'db_name']
  $productionDbsWithIrregularSlugName = ['designcorp' => 'atr8rk3fb', 'rspi' => 'iistznr4z'];
  ```

2. **`migrate-dev`** - this should be clear. But this time we don't take anything from meta. Just one database. So simple.
  - *Migration path*: `migrations/dev`
  
  - *Affected databases*:  
      * `st1_app_dev`

3. **`migrate-demo`** - migrates demo databases.  
  - *Migration path*: `migrations/demo`
  
  - *Affected databases*:  
      * `st1_demo_s_shanstrom`  
      * `st1_demo_t_gaither`  
      * `st1_demo`

4. **`migrate-legacy`** - we still have this for our couple of legacy databases. Migration files for this command are stored in folder .
List of affected databases:
  - *Migration path*: `migrations/legacy`
  
  - *Affected databases*:  
      * `st1_OTA`
      * `st1_RSPi_SPACE`
      * `st1_RSPi_SPACE_test`
      * `st1_u4xko4r9e`

5. **`migrate-meta`** - process one of meta databases. This command is slightly different from others.
  - *Migration path*: `migrations/meta`
  
  - *Affected databases*:  
      * `st1_meta`
      * `st1_meta_dev`

II. Parameters
--------------

All commands share same list of parameters, and **`migrate-meta`** has one additional required parameter.

You are free to combine this parameters in any way you want.

  > **NOTE**: all database names should be passes WITHOUT `st1_` prefix.
  
  - **`--command="$value"`** - please refer to [Yii2 Migrations guide](https://github.com/yiisoft/yii2/blob/master/docs/guide/db-migrations.md) on 
  what available commands you have here. This parameter is always **REQUIRED** for every command.
  
  Place the Yii2 migration command to `$value` **WITHOUT** `php yii migrate/` part.
  
  *Example*: `--command="create test_migration` or `--command="up 1`.
  
  The only limitation here is that you **CAN NOT** use Yii2 `--db` and `--migrationPath` parameters since they are added automatically by wrapper.
  - **`--exclude="$value"`** - here `$value` is the list of names of databases which should not be migrated.
  
  *Example*:  `--exclude="demo, dev, OTA, u4xko4r9e"`
  - **`--only="$value"`** - here `$value` is the list of names of databases which should ONLY be migrated, all other databases will
  be ignored.
  
  *Example*:  `--only="app, demo"`

One additional parameter for **`migrate-meta`**:

  - **`--env="$value"`** - here `$value` is one of `dev|production`. This parameter is **REQUIRED** for this command only.
  This specify whether to migrate `st1_meta_dev` (`dev`) or `st1_meta` (`production`).
  
  *Example*:  `--env="dev"`


III. Examples
-------------

Create migration for production databases:

  `php yii migrate-production --command="create test_migration"`

Apply all new migrations for all production databases:

  `php yii migrate-production --command="up"`

Revert last migration for all production databases:

  `php yii migrate-production --command="down 1"`
  
Apply all new migrations for all demo databases except `st1_demo_t_gaither`:

  `php yii migrate-demo --command="up" --exclude="demo_t_gaither"`
  
Apply three new migrations only for legacy `st1_RSPi_SPACE_test`:

  `php yii migrate-legacy --command="up 3" --only="RSPi_SPACE_test"`
  
Apply three new migrations only for legacy `st1_RSPi_SPACE_test`:

  `php yii migrate-legacy --command="up 3" --only="RSPi_SPACE_test"`
  
Show last 5 applied migrations of `st1_app_dev` database:

  `php yii migrate-dev --command="history 5"`
  
Apply all new migrations for `st1_meta` database:

  `php yii migrate-meta --command="up" --env="production"`
  
Modify migration history for `st1_meta_dev` database:

  `php yii migrate-meta --command="mark "2015-01-01 18:54:01"" --env="dev"`
