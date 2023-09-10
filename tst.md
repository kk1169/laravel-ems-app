## Create Project
```
laravel new laravel-ems-app

composer require laravel/breeze --dev
php artisan breeze:install
php artisan migrate
npm install
npm run dev

composer require filament/filament:"^3.0-stable" -W
php artisan filament:install --panels
php artisan make:filament-user

```

## Create Migration and Models
```
php artisan make:model Country -m
php artisan make:model State -m
php artisan make:model City -m

Schema::create('countries', function (Blueprint $table) {
    $table->id();
    $table->char("code");
    $table->string("name");
    $table->timestamps();
});

Schema::create('states', function (Blueprint $table) {
    $table->id();
    $table->foreignId("country_id")->constrained()->cascadeOnDelete();
    $table->string("name");
    $table->timestamps();
});

Schema::create('cities', function (Blueprint $table) {
    $table->id();
    $table->foreignId("state_id")->constrained()->cascadeOnDelete();
    $table->string("name");
    $table->timestamps();
});
```
## Modify Models

City.php
```
class City extends Model
{
    use HasFactory;

    protected $fillable = ['state_id', 'name'];

    public function state()
    {
        return $this->belongsTo(State::class);
    }

    public function employees()
    {
        return $this->hasMany(Employee::class);
    }
}
```
Country.php
```
class Country extends Model
{
    use HasFactory;
    protected $fillable = ['code', 'name'];
    public function states()
    {
        return $this->hasMany(State::class);
    }

    public function employees()
    {
        return $this->hasMany(Employee::class);
    }
}
```
State.php
```
class State extends Model
{
    use HasFactory;
    protected $fillable = ['country_id', 'name'];

    public function country()
    {
        return $this->belongsTo(Country::class);
    }

    public function cities()
    {
        return $this->hasMany(City::class);
    }

    public function employees()
    {
        return $this->hasMany(Employee::class);
    }
}
```
Department.php
```
class Department extends Model
{
    use HasFactory;
    protected $fillable = ['name'];

    public function employees()
    {
        return $this->hasMany(Employee::class);
    }
}
```
Employee.php
```
class Employee extends Model
{
    use HasFactory;
    protected $fillable = ['first_name', 'last_name', 'address', 'department_id', 'city_id', 'country_id', 'state_id', 'zip_code', 'dob', 'doj'];


    public function state()
    {
        return $this->belongsTo(State::class);
    }


    public function country()
    {
        return $this->belongsTo(Country::class);
    }


    public function city()
    {
        return $this->belongsTo(City::class);
    }


    public function department()
    {
        return $this->belongsTo(Department::class);
    }
}
```

## Country CRUD
```
php artisan make:filament-resource Country

<?php

namespace App\Filament\Resources;

use App\Filament\Resources\CountryResource\Pages;
use App\Filament\Resources\CountryResource\RelationManagers;
use App\Models\Country;
use Filament\Forms;
use Filament\Forms\Components\Card;
use Filament\Forms\Components\Section;
use Filament\Forms\Components\TextInput;
use Filament\Forms\Form;
use Filament\Resources\Resource;
use Filament\Tables;
use Filament\Tables\Columns\TextColumn;
use Filament\Tables\Filters\Filter;
use Filament\Tables\Table;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\SoftDeletingScope;

class CountryResource extends Resource
{
    protected static ?string $model = Country::class;

    protected static ?string $navigationIcon = 'heroicon-o-rectangle-stack';

    public static function form(Form $form): Form
    {
        return $form
            ->schema([
                Section::make()->schema([
                    TextInput::make('name'),
                    TextInput::make('code')
                ])
            ]);
    }

    public static function table(Table $table): Table
    {
        return $table
            ->columns([
                TextColumn::make('id')->sortable(),
                TextColumn::make('name')->sortable()->searchable(),
                TextColumn::make('code')->sortable()->searchable(),
                TextColumn::make('created_at')->dateTime()
            ])
            ->filters([
                // Filter::make('name')
                //     ->label('Country Name')
            ])
            ->actions([
                Tables\Actions\EditAction::make(),
                Tables\Actions\DeleteAction::make(),
            ])
            ->bulkActions([
                Tables\Actions\BulkActionGroup::make([
                    Tables\Actions\DeleteBulkAction::make(),
                ]),
            ])
            ->emptyStateActions([
                Tables\Actions\CreateAction::make(),
            ]);
    }

    public static function getRelations(): array
    {
        return [
            //
        ];
    }

    public static function getPages(): array
    {
        return [
            'index' => Pages\ListCountries::route('/'),
            'create' => Pages\CreateCountry::route('/create'),
            'edit' => Pages\EditCountry::route('/{record}/edit'),
        ];
    }
}

```
## State CRUD
