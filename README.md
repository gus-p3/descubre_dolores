# Descubre Dolores Hidalgo 🏛️

Guía turística multimedia de Dolores Hidalgo, Guanajuato, construida con **Flutter** y **Clean Architecture**.

> Proyecto de la asignatura **Desarrollo Móvil Integral (GIDS6102)** — Arquitectura Limpia aplicada a una app con imagen, audio-guía y video.

---

## 📱 ¿Qué hace la app?

El usuario ve una lista de lugares emblemáticos de Dolores Hidalgo. Al entrar a cada uno puede:

- 🖼️ Ver una **fotografía** del lugar
- 🎧 Escuchar una **audio-guía narrada**
- 🎬 Reproducir un **video corto** (cuando esté disponible)

Lugares incluidos:
1. **Jardín Principal** — con imagen, audio y video
2. **Parroquia de Nuestra Señora de los Dolores** — con imagen y audio
3. **Museo Casa de Hidalgo** — con imagen y audio

---

## 🏗️ Arquitectura: Clean Architecture

El proyecto sigue la **Arquitectura Limpia** de Robert C. Martin, organizada en 3 capas concéntricas donde la dependencia siempre apunta hacia adentro:

```
Presentación ──► Dominio ◄── Datos
```

| Capa | Contenido | Depende de |
|---|---|---|
| **Dominio** | Entidades, casos de uso, interfaces de repositorio | Nada (Dart puro) |
| **Datos** | Implementaciones concretas de repositorios, fuentes de datos | Dominio |
| **Presentación** | ViewModels (MVVM) y pantallas Flutter | Dominio |

> ✅ **Regla de dependencia**: ningún archivo en `domain/` importa `package:flutter/material.dart`.

---

## 📂 Estructura de carpetas

```
lib/
├── domain/                          # ← Dart puro, sin Flutter
│   ├── entities/
│   │   └── lugar_turistico.dart     # Entidad con id, nombre, imagen, audio, video
│   ├── repositories/
│   │   └── lugares_repository.dart  # Interfaz abstracta (contrato)
│   └── usecases/
│       ├── obtener_lugares.dart     # Caso de uso: listar todos
│       └── obtener_lugar_por_id.dart # Caso de uso: buscar por ID
├── data/                            # ← El "cómo" de los datos
│   ├── datasources/
│   │   └── lugares_local_datasource.dart  # Lista fija en memoria
│   └── repositories/
│       └── lugares_repository_impl.dart   # Implementa la interfaz del dominio
├── presentation/                    # ← MVVM + Flutter
│   ├── viewmodels/
│   │   ├── lugares_view_model.dart  # ChangeNotifier para la lista
│   │   └── detalle_view_model.dart  # Controla audioplayers
│   └── views/
│       ├── lista_lugares_screen.dart     # Pantalla principal con ListView
│       └── detalle_lugar_screen.dart     # Pantalla de detalle multimedia
└── main.dart                        # Composition Root — conecta las 3 capas

assets/
├── images/   # Fotografías .jpg de cada lugar
├── audio/    # Narraciones .mp3 de cada lugar
└── video/    # Videos cortos .mp4 (opcionales)

test/
└── obtener_lugares_test.dart  # Prueba del Dominio con Fake (sin Flutter)
```

---

## 📦 Dependencias

```yaml
dependencies:
  audioplayers: ^6.8.1   # Reproducción de audio desde assets
  video_player: ^2.14.0  # Reproducción de video desde assets

dev_dependencies:
  flutter_test            # Pruebas unitarias del Dominio
  flutter_lints           # Reglas de estilo
```

---

## 🚀 Cómo ejecutar

### 1. Agrega tus archivos multimedia

Coloca tus archivos con estos nombres en las carpetas de `assets/`:

| Lugar | Imagen | Audio | Video |
|---|---|---|---|
| Jardín Principal | `images/jardin_principal.jpg` | `audio/jardin_principal.mp3` | `video/jardin_principal.mp4` |
| Parroquia | `images/parroquia.jpg` | `audio/parroquia.mp3` | — |
| Museo Casa de Hidalgo | `images/museo_hidalgo.jpg` | `audio/museo_hidalgo.mp3` | — |

> Si usas otros nombres, ajústalos en [`lugares_local_datasource.dart`](lib/data/datasources/lugares_local_datasource.dart).

### 2. Instala dependencias y corre la app

```bash
flutter pub get
flutter run
```

### 3. Ejecuta las pruebas del Dominio

```bash
flutter test
```

Resultado esperado:
```
+1: ObtenerLugares regresa la lista que entrega el repositorio
+1: All tests passed!
```

---

## 🧪 TDD — Prueba del Dominio

La prueba en `test/obtener_lugares_test.dart` usa un **Fake** del repositorio para verificar que `ObtenerLugares` delega correctamente, **sin necesidad de reproducir audio, video ni cargar imágenes reales**.

```dart
class FakeLugaresRepository implements LugaresRepository {
  @override
  Future<List<LugarTuristico>> obtenerTodos() async => const [
    LugarTuristico(id: '1', nombre: 'Lugar de prueba', ...),
  ];
}
```

Esta es la mayor ventaja de Clean Architecture: **toda la lógica de negocio se puede probar sin un emulador**.

---

## ✅ Checklist de Arquitectura Limpia

- [x] Ningún archivo en `domain/` importa `package:flutter/material.dart`
- [x] Las entidades del Dominio no tienen métodos `fromJson/toJson`
- [x] Cada caso de uso representa una sola acción del usuario
- [x] Los ViewModels reciben casos de uso por constructor
- [x] `main.dart` es el único archivo que conoce las clases concretas de las 3 capas
- [x] Existe al menos una prueba del Dominio usando un Fake
- [x] Los controladores de audio/video se liberan con `dispose()`

---

## 🔒 Permisos

Esta app **no requiere permisos peligrosos** porque reproduce archivos locales desde `assets/`. Si en el futuro se descarga contenido de internet o se graba audio del usuario, se deberán declarar los permisos correspondientes en tiempo de ejecución.

---

## 💡 Ideas para extender la app

- 🗺️ Agregar un mapa que muestre la ubicación de cada lugar turístico
- ☁️ Sustituir `LugaresLocalDataSource` por una implementación que lea desde una API en la nube
- ⭐ Agregar una pantalla de favoritos reutilizando el patrón MVVM
- ⏩ Agregar control de velocidad de reproducción en la audio-guía
