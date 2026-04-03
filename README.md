# FilamentPHP Auto Logout

Plugin para FilamentPHP que cierra la sesión de los usuarios de forma automática cuando están inactivos. Funciona correctamente con múltiples pestañas abiertas.

> **Este paquete es un fork de [niladam/filament-auto-logout](https://github.com/niladam/filament-auto-logout).**
> Se han incorporado compatibilidad con Filament v5 y traducción al español.

---

## Compatibilidad

| Versión del paquete | Filament |
|---|---|
| v3 (este fork) | [v3](https://filamentphp.com/docs/3.x/panels/installation) · [v4](https://filamentphp.com/docs/4.x/introduction/overview) · [v5](https://filamentphp.com/docs/5.x/introduction/overview) |

---

## ¿Qué hace este plugin?

- Detecta la inactividad del usuario (sin movimiento de ratón, teclado ni interacción).
- Muestra una **notificación de advertencia** antes de cerrar sesión.
- Muestra un **contador de tiempo restante** en la barra superior del panel.
- Sincroniza el temporizador entre múltiples pestañas del mismo navegador.
- Totalmente configurable: duración, advertencia, color, ícono y ubicación del badge.
- Soporte de traducciones: `en`, `es`, `ar`, `ro`.

---

## Instalación

```bash
composer require joseforozco/auto-logout
```

Ejecuta el instalador del paquete:

```bash
php artisan filament-auto-logout:install
```

Publica los assets de Filament:

```bash
php artisan filament:assets
```

---

## Configuración

Puedes publicar el archivo de configuración con:

```bash
php artisan vendor:publish --tag="filament-auto-logout-config"
```

Contenido del archivo de configuración:

```php
use Carbon\Carbon;
use Filament\View\PanelsRenderHook;

return [
    // Habilitar o deshabilitar el plugin
    'enabled' => env('FILAMENT_AUTO_LOGOUT_ENABLED', true),

    // Tiempo de inactividad en segundos antes de cerrar sesión (por defecto: 15 minutos)
    'duration_in_seconds' => env('FILAMENT_AUTO_LOGOUT_DURATION_IN_SECONDS', Carbon::SECONDS_PER_MINUTE * 15),

    // Segundos antes del cierre de sesión en los que se muestra la advertencia
    'warn_before_in_seconds' => env('FILAMENT_AUTO_LOGOUT_WARN_BEFORE_IN_SECONDS', 30),

    // Mostrar el contador de tiempo restante en el panel
    'show_time_left' => env('FILAMENT_AUTO_LOGOUT_SHOW_TIME_LEFT', true),

    // Texto que aparece antes del contador
    'time_left_text' => env('FILAMENT_AUTO_LOGOUT_TIME_LEFT_TEXT', 'Time left:'),

    // Ubicación del badge dentro del panel
    'location' => env('FILAMENT_AUTO_LOGOUT_LOCATION', PanelsRenderHook::GLOBAL_SEARCH_BEFORE),
];
```

También puedes configurar variables de entorno en tu `.env`:

```env
FILAMENT_AUTO_LOGOUT_ENABLED=true
FILAMENT_AUTO_LOGOUT_DURATION_IN_SECONDS=900
FILAMENT_AUTO_LOGOUT_WARN_BEFORE_IN_SECONDS=30
FILAMENT_AUTO_LOGOUT_SHOW_TIME_LEFT=true
FILAMENT_AUTO_LOGOUT_TIME_LEFT_TEXT="Tiempo restante:"
```

---

## Uso

### Básico

En tu `PanelProvider` (`app/Providers/Filament/AdminPanelProvider.php`):

```php
use Joseforozco\FilamentAutoLogout\AutoLogoutPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        // ...
        ->plugins([
            AutoLogoutPlugin::make(),
        ]);
}
```

### Personalizado

```php
use Carbon\Carbon;
use Filament\Support\Colors\Color;
use Joseforozco\FilamentAutoLogout\AutoLogoutPlugin;

->plugins([
    AutoLogoutPlugin::make()
        ->color(Color::Emerald)                              // Color del badge (por defecto: Color::Stone)
        ->icon('heroicon-o-arrow-right-start-on-rectangle')  // Ícono del badge (por defecto: heroicon-o-clock)
        ->logoutAfter(Carbon::SECONDS_PER_MINUTE * 5)        // Cerrar sesión tras 5 minutos de inactividad
        ->warnBefore(60)                                     // Advertir 60 segundos antes
        ->withoutWarning()                                   // Deshabilitar la notificación de advertencia
        ->withoutTimeLeft()                                  // Ocultar el contador de tiempo
        ->timeLeftText('Tiempo restante:')                   // Personalizar el texto del contador
        ->disableIf(fn () => auth()->id() === 1)             // Deshabilitar para el usuario con ID 1
        ->enableIf(fn () => auth()->user()->hasRole('admin')) // Habilitar solo para admins
])
```

---

## Traducciones

El plugin incluye soporte para múltiples idiomas: `en`, `es`, `ar`, `ro`.

Para publicar y personalizar las traducciones:

```bash
php artisan vendor:publish --tag="filament-auto-logout-translations"
```

Los archivos se publicarán en `lang/vendor/filament-auto-logout/`.

---

## Changelog

Ver [CHANGELOG](CHANGELOG.md) para el historial de cambios.

## Créditos

- [Madalin Tache](https://github.com/niladam) — autor original
- [joseforozco](https://github.com/joseforozco) — fork con soporte Filament v5 y traducción ES

## Licencia

MIT. Ver [LICENSE](LICENSE.md) para más información.
