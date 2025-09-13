# Migraciones y Seeders

## ¿Qué es?

Las migraciones y seeders son herramientas fundamentales para el control de
versiones del esquema de base de datos y la gestión de datos iniciales. Las
migraciones permiten evolucionar la estructura de la base de datos de forma
controlada y reproducible, mientras que los seeders facilitan la carga de datos
de prueba y datos maestros necesarios para el funcionamiento de la aplicación.

En el contexto de Clean Architecture con Laravel, estas herramientas deben
mantener la separación de responsabilidades y facilitar el desarrollo
colaborativo y el despliegue consistente entre diferentes entornos.

## ¿Por qué es importante?

- **Control de versiones del schema**: Permite rastrear y aplicar cambios de
  estructura de base de datos
- **Colaboración efectiva**: Facilita que múltiples desarrolladores trabajen con
  el mismo esquema
- **Despliegues consistentes**: Garantiza que todos los entornos tengan la misma
  estructura de datos
- **Datos de prueba reproducibles**: Facilita la creación de entornos de testing
  consistentes
- **Rollback seguro**: Permite revertir cambios de schema cuando sea necesario
- **Documentación implícita**: Las migraciones documentan la evolución del
  esquema
- **Automatización de despliegues**: Integra cambios de BD en pipelines de CI/CD

## ¿Qué debe incluir?

### Migraciones

#### Estructura de Migraciones

- **Creación de tablas**: Scripts para nuevas entidades
- **Modificación de tablas**: Alteraciones de estructura existente
- **Índices y constraints**: Optimizaciones y restricciones de integridad
- **Datos maestros**: Información base requerida por la aplicación

#### Convenciones de Nomenclatura

- **Timestamp + descripción**: `2024_09_12_143000_create_users_table.php`
- **Verbos descriptivos**: create, add, modify, drop, update
- **Orden cronológico**: Refleja la evolución temporal del esquema

### Seeders

#### Tipos de Seeders

- **DatabaseSeeder**: Seeder principal que orquesta todos los demás
- **MasterDataSeeder**: Datos maestros esenciales (roles, permisos, países)
- **TestDataSeeder**: Datos de prueba para desarrollo y testing
- **DemoDataSeeder**: Datos de demostración para presentaciones

#### Gestión de Dependencias

- **Orden de ejecución**: Respeta las relaciones entre entidades
- **Validación de existencia**: Evita duplicación de datos
- **Entornos específicos**: Diferentes datos según el ambiente

## ¿Qué debo hacer?

### 1. Configuración Inicial de Migraciones

#### Estructura Base de Directorio

```bash
database/
├── migrations/
│   ├── 2024_01_01_000000_create_users_table.php
│   ├── 2024_01_01_000001_create_roles_table.php
│   ├── 2024_01_01_000002_create_permissions_table.php
│   ├── 2024_01_01_000003_create_user_roles_table.php
│   └── 2024_01_01_000004_create_role_permissions_table.php
├── seeders/
│   ├── DatabaseSeeder.php
│   ├── MasterData/
│   │   ├── RoleSeeder.php
│   │   ├── PermissionSeeder.php
│   │   └── CountrySeeder.php
│   ├── TestData/
│   │   ├── UserSeeder.php
│   │   ├── ProductSeeder.php
│   │   └── OrderSeeder.php
│   └── Factories/
│       ├── UserFactory.php
│       ├── ProductFactory.php
│       └── OrderFactory.php
└── schema/
    └── mysql-schema.sql
```

#### Template de Migración Estándar

```php
<?php
// database/migrations/2024_09_12_143000_create_users_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            // Primary Key
            $table->uuid('id')->primary();
            
            // Business Attributes
            $table->string('email')->unique();
            $table->string('name');
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->boolean('is_active')->default(true);
            
            // Audit Fields
            $table->timestamps();
            $table->softDeletes();
            
            // Foreign Keys
            // $table->uuid('role_id')->nullable();
            // $table->foreign('role_id')->references('id')->on('roles');
            
            // Indexes
            $table->index(['email', 'is_active']);
            $table->index(['created_at']);
            $table->index(['deleted_at']);
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('users');
    }
};
```

### 2. Migraciones Avanzadas

#### Migración de Modificación de Tabla

```php
<?php
// database/migrations/2024_09_12_150000_add_profile_fields_to_users_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::table('users', function (Blueprint $table) {
            // Añadir nuevas columnas
            $table->string('phone')->nullable()->after('email');
            $table->date('birth_date')->nullable()->after('phone');
            $table->text('bio')->nullable()->after('birth_date');
            $table->string('avatar_url')->nullable()->after('bio');
            
            // Añadir nuevos índices
            $table->index(['phone']);
            $table->index(['birth_date']);
        });
    }

    public function down(): void
    {
        Schema::table('users', function (Blueprint $table) {
            // Eliminar índices primero
            $table->dropIndex(['phone']);
            $table->dropIndex(['birth_date']);
            
            // Eliminar columnas
            $table->dropColumn(['phone', 'birth_date', 'bio', 'avatar_url']);
        });
    }
};
```

#### Migración con Datos Maestros

```php
<?php
// database/migrations/2024_09_12_160000_create_roles_table_with_data.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
use Illuminate\Support\Facades\DB;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('roles', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->string('name')->unique();
            $table->string('description');
            $table->json('permissions')->nullable();
            $table->boolean('is_active')->default(true);
            $table->timestamps();
            $table->softDeletes();
            
            $table->index(['name', 'is_active']);
        });

        // Insertar roles básicos
        $this->insertBasicRoles();
    }

    public function down(): void
    {
        Schema::dropIfExists('roles');
    }

    private function insertBasicRoles(): void
    {
        $roles = [
            [
                'id' => $this->generateUuid(),
                'name' => 'super_admin',
                'description' => 'Super Administrator with full access',
                'permissions' => json_encode(['*']),
                'is_active' => true,
                'created_at' => now(),
                'updated_at' => now(),
            ],
            [
                'id' => $this->generateUuid(),
                'name' => 'admin',
                'description' => 'Administrator with management access',
                'permissions' => json_encode(['users.manage', 'content.manage']),
                'is_active' => true,
                'created_at' => now(),
                'updated_at' => now(),
            ],
            [
                'id' => $this->generateUuid(),
                'name' => 'user',
                'description' => 'Regular user with basic access',
                'permissions' => json_encode(['profile.view', 'profile.edit']),
                'is_active' => true,
                'created_at' => now(),
                'updated_at' => now(),
            ],
        ];

        DB::table('roles')->insert($roles);
    }

    private function generateUuid(): string
    {
        return (string) \Illuminate\Support\Str::uuid();
    }
};
```

### 3. Sistema de Seeders Avanzado

#### DatabaseSeeder Principal

```php
<?php
// database/seeders/DatabaseSeeder.php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\App;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        $this->call([
            // 1. Datos maestros (siempre se ejecutan)
            MasterData\RoleSeeder::class,
            MasterData\PermissionSeeder::class,
            MasterData\CountrySeeder::class,
            MasterData\CurrencySeeder::class,
        ]);

        // 2. Datos de desarrollo y testing
        if (App::environment(['local', 'testing', 'staging'])) {
            $this->call([
                TestData\UserSeeder::class,
                TestData\ProductSeeder::class,
                TestData\CategorySeeder::class,
                TestData\OrderSeeder::class,
            ]);
        }

        // 3. Datos de demostración (solo en staging)
        if (App::environment('staging')) {
            $this->call([
                DemoData\CompanySeeder::class,
                DemoData\SampleContentSeeder::class,
            ]);
        }
    }
}
```

#### Seeder de Datos Maestros

```php
<?php
// database/seeders/MasterData/RoleSeeder.php

namespace Database\Seeders\MasterData;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Carbon\Carbon;

class RoleSeeder extends Seeder
{
    public function run(): void
    {
        $roles = $this->getRolesData();

        foreach ($roles as $role) {
            $this->createRoleIfNotExists($role);
        }
    }

    private function getRolesData(): array
    {
        return [
            [
                'name' => 'super_admin',
                'display_name' => 'Super Administrador',
                'description' => 'Acceso completo al sistema',
                'permissions' => ['*'],
                'is_system_role' => true,
            ],
            [
                'name' => 'admin',
                'display_name' => 'Administrador',
                'description' => 'Administrador del sistema',
                'permissions' => [
                    'users.view', 'users.create', 'users.edit', 'users.delete',
                    'roles.view', 'roles.create', 'roles.edit', 'roles.delete',
                    'content.view', 'content.create', 'content.edit', 'content.delete',
                ],
                'is_system_role' => true,
            ],
            [
                'name' => 'manager',
                'display_name' => 'Gerente',
                'description' => 'Gestión de contenido y usuarios',
                'permissions' => [
                    'users.view', 'users.edit',
                    'content.view', 'content.create', 'content.edit',
                    'reports.view',
                ],
                'is_system_role' => false,
            ],
            [
                'name' => 'user',
                'display_name' => 'Usuario',
                'description' => 'Usuario estándar del sistema',
                'permissions' => [
                    'profile.view', 'profile.edit',
                    'content.view',
                ],
                'is_system_role' => true,
            ],
        ];
    }

    private function createRoleIfNotExists(array $roleData): void
    {
        $existingRole = DB::table('roles')
            ->where('name', $roleData['name'])
            ->first();

        if (!$existingRole) {
            DB::table('roles')->insert([
                'id' => (string) \Illuminate\Support\Str::uuid(),
                'name' => $roleData['name'],
                'display_name' => $roleData['display_name'],
                'description' => $roleData['description'],
                'permissions' => json_encode($roleData['permissions']),
                'is_system_role' => $roleData['is_system_role'],
                'is_active' => true,
                'created_at' => Carbon::now(),
                'updated_at' => Carbon::now(),
            ]);

            $this->command->info("✅ Role '{$roleData['name']}' created");
        } else {
            $this->command->info("ℹ️  Role '{$roleData['name']}' already exists");
        }
    }
}
```

#### Factory para Datos de Prueba

```php
<?php
// database/factories/UserFactory.php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    protected $model = User::class;

    /**
     * Define the model's default state.
     */
    public function definition(): array
    {
        return [
            'id' => Str::uuid(),
            'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(),
            'email_verified_at' => now(),
            'password' => Hash::make('password'), // password
            'phone' => $this->faker->phoneNumber(),
            'birth_date' => $this->faker->date(),
            'bio' => $this->faker->optional(0.7)->paragraph(),
            'avatar_url' => $this->faker->optional(0.5)->imageUrl(200, 200, 'people'),
            'is_active' => $this->faker->boolean(90), // 90% activos
            'remember_token' => Str::random(10),
        ];
    }

    /**
     * Indicate that the model's email address should be unverified.
     */
    public function unverified(): static
    {
        return $this->state(fn (array $attributes) => [
            'email_verified_at' => null,
        ]);
    }

    /**
     * Create an admin user.
     */
    public function admin(): static
    {
        return $this->state(fn (array $attributes) => [
            'name' => 'Admin User',
            'email' => 'admin@example.com',
            'is_active' => true,
        ]);
    }

    /**
     * Create a super admin user.
     */
    public function superAdmin(): static
    {
        return $this->state(fn (array $attributes) => [
            'name' => 'Super Admin',
            'email' => 'superadmin@example.com',
            'is_active' => true,
        ]);
    }

    /**
     * Create inactive users.
     */
    public function inactive(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_active' => false,
        ]);
    }
}
```

#### Seeder de Datos de Prueba

```php
<?php
// database/seeders/TestData/UserSeeder.php

namespace Database\Seeders\TestData;

use Illuminate\Database\Seeder;
use App\Models\User;
use Database\Factories\UserFactory;
use Illuminate\Support\Facades\DB;

class UserSeeder extends Seeder
{
    public function run(): void
    {
        $this->createSystemUsers();
        $this->createTestUsers();
        $this->assignRolesToUsers();
    }

    private function createSystemUsers(): void
    {
        // Super Admin
        $superAdmin = User::factory()->superAdmin()->create();
        $this->command->info("✅ Super Admin created: {$superAdmin->email}");

        // Admin
        $admin = User::factory()->admin()->create();
        $this->command->info("✅ Admin created: {$admin->email}");

        // Test User
        $testUser = User::factory()->create([
            'name' => 'Test User',
            'email' => 'user@example.com',
        ]);
        $this->command->info("✅ Test User created: {$testUser->email}");
    }

    private function createTestUsers(): void
    {
        // Usuarios activos
        $activeUsers = User::factory()
            ->count(50)
            ->create();

        // Usuarios inactivos
        $inactiveUsers = User::factory()
            ->inactive()
            ->count(10)
            ->create();

        // Usuarios sin verificar email
        $unverifiedUsers = User::factory()
            ->unverified()
            ->count(15)
            ->create();

        $this->command->info("✅ Created {$activeUsers->count()} active users");
        $this->command->info("✅ Created {$inactiveUsers->count()} inactive users");
        $this->command->info("✅ Created {$unverifiedUsers->count()} unverified users");
    }

    private function assignRolesToUsers(): void
    {
        // Obtener roles
        $superAdminRole = DB::table('roles')->where('name', 'super_admin')->first();
        $adminRole = DB::table('roles')->where('name', 'admin')->first();
        $userRole = DB::table('roles')->where('name', 'user')->first();

        if (!$superAdminRole || !$adminRole || !$userRole) {
            $this->command->error("❌ Roles not found. Run RoleSeeder first.");
            return;
        }

        // Asignar roles a usuarios específicos
        $superAdmin = User::where('email', 'superadmin@example.com')->first();
        $admin = User::where('email', 'admin@example.com')->first();
        $testUser = User::where('email', 'user@example.com')->first();

        if ($superAdmin) {
            $this->assignRoleToUser($superAdmin->id, $superAdminRole->id);
        }

        if ($admin) {
            $this->assignRoleToUser($admin->id, $adminRole->id);
        }

        if ($testUser) {
            $this->assignRoleToUser($testUser->id, $userRole->id);
        }

        // Asignar rol de usuario a usuarios aleatorios
        $randomUsers = User::whereNotIn('email', [
            'superadmin@example.com',
            'admin@example.com',
            'user@example.com'
        ])->take(20)->get();

        foreach ($randomUsers as $user) {
            $this->assignRoleToUser($user->id, $userRole->id);
        }

        $this->command->info("✅ Roles assigned to users");
    }

    private function assignRoleToUser(string $userId, string $roleId): void
    {
        DB::table('user_roles')->insert([
            'user_id' => $userId,
            'role_id' => $roleId,
            'assigned_at' => now(),
            'assigned_by' => $userId, // Self-assigned for seeding
        ]);
    }
}
```

### 4. Comandos Artisan Personalizados

#### Comando para Gestión de Migraciones

```php
<?php
// app/Console/Commands/DatabaseManagement.php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\Artisan;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;

class DatabaseManagement extends Command
{
    protected $signature = 'db:manage 
                            {action : Action to perform (status|fresh|seed|reset|backup)}
                            {--env= : Environment (local|testing|staging|production)}
                            {--force : Force action without confirmation}';

    protected $description = 'Manage database operations safely';

    public function handle(): int
    {
        $action = $this->argument('action');
        $environment = $this->option('env') ?? app()->environment();
        
        if (!$this->validateEnvironment($environment)) {
            return 1;
        }

        match ($action) {
            'status' => $this->showStatus(),
            'fresh' => $this->freshDatabase(),
            'seed' => $this->seedDatabase(),
            'reset' => $this->resetDatabase(),
            'backup' => $this->backupDatabase(),
            default => $this->error("❌ Unknown action: {$action}")
        };

        return 0;
    }

    private function validateEnvironment(string $environment): bool
    {
        if ($environment === 'production' && !$this->option('force')) {
            $this->error("❌ Production operations require --force flag");
            return false;
        }

        return true;
    }

    private function showStatus(): void
    {
        $this->info("📊 Database Status");
        $this->line("===================");
        
        // Mostrar conexión actual
        $connection = DB::connection();
        $this->line("Connection: " . $connection->getName());
        $this->line("Database: " . $connection->getDatabaseName());
        
        // Mostrar estado de migraciones
        try {
            $pendingMigrations = Artisan::call('migrate:status');
            $this->line("\n📋 Migration Status:");
            $this->line(Artisan::output());
        } catch (\Exception $e) {
            $this->error("❌ Error checking migrations: " . $e->getMessage());
        }

        // Mostrar tablas existentes
        $tables = $this->getTables();
        $this->line("\n📦 Tables ({" . count($tables) . "}):");
        foreach ($tables as $table) {
            $count = DB::table($table)->count();
            $this->line("  • {$table}: {$count} records");
        }
    }

    private function freshDatabase(): void
    {
        if (!$this->confirmAction("recreate the database")) {
            return;
        }

        $this->info("🔄 Recreating database...");
        
        Artisan::call('migrate:fresh', ['--force' => true]);
        $this->line(Artisan::output());
        
        $this->info("✅ Database recreated successfully");
    }

    private function seedDatabase(): void
    {
        if (!$this->confirmAction("seed the database")) {
            return;
        }

        $this->info("🌱 Seeding database...");
        
        Artisan::call('db:seed', ['--force' => true]);
        $this->line(Artisan::output());
        
        $this->info("✅ Database seeded successfully");
    }

    private function resetDatabase(): void
    {
        if (!$this->confirmAction("reset the database (fresh + seed)")) {
            return;
        }

        $this->info("🔄 Resetting database...");
        
        Artisan::call('migrate:fresh', ['--seed' => true, '--force' => true]);
        $this->line(Artisan::output());
        
        $this->info("✅ Database reset successfully");
    }

    private function backupDatabase(): void
    {
        $this->info("💾 Creating database backup...");
        
        $filename = 'backup_' . date('Y_m_d_His') . '.sql';
        $path = storage_path("app/backups/{$filename}");
        
        // Crear directorio si no existe
        if (!file_exists(dirname($path))) {
            mkdir(dirname($path), 0755, true);
        }

        // Ejecutar mysqldump
        $command = sprintf(
            'mysqldump -h%s -u%s -p%s %s > %s',
            config('database.connections.mysql.host'),
            config('database.connections.mysql.username'),
            config('database.connections.mysql.password'),
            config('database.connections.mysql.database'),
            $path
        );

        exec($command, $output, $returnCode);

        if ($returnCode === 0) {
            $this->info("✅ Backup created: {$filename}");
        } else {
            $this->error("❌ Backup failed");
        }
    }

    private function confirmAction(string $action): bool
    {
        if ($this->option('force')) {
            return true;
        }

        $environment = app()->environment();
        $database = DB::connection()->getDatabaseName();
        
        return $this->confirm(
            "Are you sure you want to {$action}?\n" .
            "Environment: {$environment}\n" .
            "Database: {$database}"
        );
    }

    private function getTables(): array
    {
        return DB::connection()
            ->getDoctrineSchemaManager()
            ->listTableNames();
    }
}
```

### 5. Testing de Migraciones y Seeders

#### Test de Migraciones

```php
<?php
// tests/Feature/Database/MigrationTest.php

namespace Tests\Feature\Database;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Support\Facades\Schema;
use Tests\TestCase;

class MigrationTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function users_table_has_expected_structure(): void
    {
        $this->assertTrue(Schema::hasTable('users'));
        
        $columns = [
            'id', 'email', 'name', 'password', 'phone', 
            'birth_date', 'bio', 'avatar_url', 'is_active',
            'email_verified_at', 'created_at', 'updated_at', 'deleted_at'
        ];

        foreach ($columns as $column) {
            $this->assertTrue(
                Schema::hasColumn('users', $column),
                "Column '{$column}' does not exist in users table"
            );
        }
    }

    /** @test */
    public function users_table_has_expected_indexes(): void
    {
        $indexes = Schema::getConnection()
            ->getDoctrineSchemaManager()
            ->listTableIndexes('users');

        $expectedIndexes = [
            'users_email_unique',
            'users_email_is_active_index',
            'users_created_at_index',
            'users_deleted_at_index'
        ];

        foreach ($expectedIndexes as $expectedIndex) {
            $this->assertArrayHasKey(
                $expectedIndex,
                $indexes,
                "Index '{$expectedIndex}' does not exist"
            );
        }
    }

    /** @test */
    public function roles_table_has_correct_foreign_keys(): void
    {
        $this->assertTrue(Schema::hasTable('user_roles'));
        
        $foreignKeys = Schema::getConnection()
            ->getDoctrineSchemaManager()
            ->listTableForeignKeys('user_roles');

        $this->assertCount(2, $foreignKeys, 'user_roles should have 2 foreign keys');
    }

    /** @test */
    public function migration_rollback_works(): void
    {
        // Verificar que las tablas existen
        $this->assertTrue(Schema::hasTable('users'));
        $this->assertTrue(Schema::hasTable('roles'));

        // Hacer rollback de la última migración
        \Artisan::call('migrate:rollback', ['--step' => 1]);

        // Verificar que la operación fue exitosa
        $this->assertEquals(0, \Artisan::call('migrate:status'));
    }
}
```

#### Test de Seeders

```php
<?php
// tests/Feature/Database/SeederTest.php

namespace Tests\Feature\Database;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Support\Facades\DB;
use Tests\TestCase;

class SeederTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function role_seeder_creates_system_roles(): void
    {
        $this->seed(\Database\Seeders\MasterData\RoleSeeder::class);

        $expectedRoles = ['super_admin', 'admin', 'manager', 'user'];
        
        foreach ($expectedRoles as $roleName) {
            $this->assertDatabaseHas('roles', [
                'name' => $roleName,
                'is_active' => true
            ]);
        }

        $this->assertEquals(4, DB::table('roles')->count());
    }

    /** @test */
    public function user_seeder_creates_system_users(): void
    {
        // Primero crear roles
        $this->seed(\Database\Seeders\MasterData\RoleSeeder::class);
        
        // Luego crear usuarios
        $this->seed(\Database\Seeders\TestData\UserSeeder::class);

        $systemUsers = [
            'superadmin@example.com',
            'admin@example.com',
            'user@example.com'
        ];

        foreach ($systemUsers as $email) {
            $this->assertDatabaseHas('users', [
                'email' => $email,
                'is_active' => true
            ]);
        }

        // Verificar que se crearon usuarios adicionales
        $totalUsers = DB::table('users')->count();
        $this->assertGreaterThan(3, $totalUsers);
    }

    /** @test */
    public function seeder_respects_environment(): void
    {
        config(['app.env' => 'production']);
        
        $this->seed(\Database\Seeders\DatabaseSeeder::class);

        // En producción, solo deberían existir datos maestros
        $this->assertDatabaseHas('roles', ['name' => 'super_admin']);
        
        // No deberían existir datos de prueba
        $this->assertDatabaseMissing('users', ['email' => 'user@example.com']);
    }

    /** @test */
    public function seeder_handles_existing_data(): void
    {
        // Ejecutar seeder dos veces
        $this->seed(\Database\Seeders\MasterData\RoleSeeder::class);
        $this->seed(\Database\Seeders\MasterData\RoleSeeder::class);

        // No debería duplicar roles
        $this->assertEquals(1, DB::table('roles')->where('name', 'super_admin')->count());
    }
}
```

## Tips

### Mejores Prácticas

- **Nomenclatura descriptiva**: Usa nombres claros que describan el propósito de
  la migración
- **Migraciones atómicas**: Cada migración debe hacer una sola cosa
- **Always rollbackeable**: Siempre implementa el método `down()` correctamente
- **Validar antes de ejecutar**: Usa condicionales para evitar errores en
  re-ejecuciones
- **Seeders idempotentes**: Los seeders deben poder ejecutarse múltiples veces
  sin problemas
- **Separar por entornos**: Usa diferentes seeders según el ambiente
- **Factories flexibles**: Crea factories reutilizables y configurables

### Herramientas de Desarrollo

- **Laravel Migration Generator**: Para generar migraciones desde BD existentes
- **Doctrine DBAL**: Para operaciones avanzadas de schema
- **Laravel Telescope**: Para debugging de queries durante seeding
- **MySQL Workbench**: Para validar estructura generada

### Errores Comunes a Evitar

- **Foreign keys sin orden**: Crear FKs antes que las tablas referenciadas
- **Rollbacks incompletos**: No revertir todos los cambios en `down()`
- **Seeders con datos hardcodeados**: Usar factories en lugar de datos fijos
- **No validar existencia**: Intentar crear datos que ya existen
- **Ignorar entornos**: Ejecutar datos de prueba en producción
- **Migraciones muy grandes**: Dividir cambios complejos en múltiples
  migraciones

## Ejemplos

### Migración de Relación Muchos a Muchos

```php
<?php
// database/migrations/2024_09_12_170000_create_product_categories_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('product_categories', function (Blueprint $table) {
            $table->uuid('product_id');
            $table->uuid('category_id');
            $table->integer('sort_order')->default(0);
            $table->boolean('is_primary')->default(false);
            $table->timestamps();

            // Primary key compuesta
            $table->primary(['product_id', 'category_id']);

            // Foreign keys
            $table->foreign('product_id')
                  ->references('id')->on('products')
                  ->onDelete('cascade');
                  
            $table->foreign('category_id')
                  ->references('id')->on('categories')
                  ->onDelete('cascade');

            // Índices adicionales
            $table->index(['category_id', 'sort_order']);
            $table->index(['product_id', 'is_primary']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('product_categories');
    }
};
```

### Seeder con Relaciones Complejas

```php
<?php
// database/seeders/TestData/ProductSeeder.php

namespace Database\Seeders\TestData;

use Illuminate\Database\Seeder;
use App\Models\Product;
use App\Models\Category;
use App\Models\ProductCategory;

class ProductSeeder extends Seeder
{
    public function run(): void
    {
        $categories = Category::all();
        
        if ($categories->isEmpty()) {
            $this->command->error("❌ No categories found. Run CategorySeeder first.");
            return;
        }

        // Crear productos con categorías
        Product::factory()
            ->count(100)
            ->create()
            ->each(function (Product $product) use ($categories) {
                // Asignar 1-3 categorías aleatorias
                $randomCategories = $categories->random(rand(1, 3));
                
                $product->categories()->attach(
                    $randomCategories->pluck('id')->toArray(),
                    [
                        'sort_order' => 0,
                        'is_primary' => true,
                        'created_at' => now(),
                        'updated_at' => now(),
                    ]
                );
            });

        $this->command->info("✅ Products created and linked to categories");
    }
}
```

## Navegación

[⬅️ Autenticación Laravel Sanctum](./autenticacion-laravel-sanctum.md) |
[🏠 README Principal](../../README.md) |
[Testing TDD Backend ➡️](./testing-tdd-backend.md)
