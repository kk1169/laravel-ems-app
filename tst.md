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
