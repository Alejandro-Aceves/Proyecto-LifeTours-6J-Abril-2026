¡Excelente iniciativa! Vamos a construir la estructura de este agente global de automatización bajo el estándar `.agents` y aplicarlo directamente al desarrollo de tu app **LifeTours**.

Cabe aclarar que, aunque mencionaste un "proyecto veterinario" al final, me centraré en la **Agencia de Tours** y el **CRUD de Destinos** que solicitaste como núcleo del proyecto.

---

## 1. Estructura de la Habilidad Agente Global: `.agents`

Primero, definimos el cerebro de tu automatización. Esta carpeta vivirá en la raíz de tu espacio de trabajo para guiar a los sub-agentes (diseño, código y scraping).

**Estructura de carpetas:**
* `.agents/`
    * `SKILL.md` (Documentación y reglas)
    * `scripts/` (Automatizaciones de terminal)
    * `examples/` (Templates de UI y Lógica)
    * `resources/` (Assets y configuraciones Firebase)

### Contenido de `.agents/SKILL.md`
```markdown
# Skill: LifeTours Automation Agent
**Propósito:** Automatizar la creación de UI y lógica CRUD para Flutter/Firestore.

## Sub-Skills
1. **Design Skill:** Generación de widgets Material 3 y navegación.
2. **Code Skill:** Integración con Firestore y modelos de datos Dart.
3. **Scraping Skill:** Extracción de datos de destinos turísticos (opcional).

## Estándares
- Framework: Flutter
- State Management: Provider o Riverpod (opcional)
- Database: Cloud Firestore
```

---

## 2. Prerrequisitos y Configuración del Entorno

Antes de escribir código, aseguremos que tu terminal está lista.

### Verificación de Herramientas
Ejecuta estos comandos en tu terminal (VS Code o AntiGravity):
1.  **Flutter:** `flutter --version` (Si no está instalado, descarga el SDK en [flutter.dev](https://docs.flutter.dev/get-started/install)).
2.  **Firebase CLI:** `firebase --version`.
    * Si no lo tienes: `npm install -g firebase-tools`.
3.  **Login:** `firebase login`.

### Inicialización del Proyecto
```bash
# Crear el proyecto
flutter create lifetours
cd lifetours

# Agregar dependencias necesarias
flutter pub add cloud_firestore firebase_core
```

---

## 3. Configuración de Firebase y `pubspec.yaml`

En tu consola de Firebase, crea un proyecto llamado **LifeTours**, añade una app de Android y descarga el archivo `google-services.json` (colócalo en `android/app/`).

### Configuración de `pubspec.yaml`
Asegúrate de que tus dependencias se vean así:
```yaml
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.6
  firebase_core: latest_version
  cloud_firestore: latest_version
```

---

## 4. Implementación del Proyecto "LifeTours"

A continuación, generamos la lógica lógica y los archivos `.dart` para el CRUD de **Destinos**.

### Estructura de Archivos (lib/)
* `main.dart`: Punto de entrada.
* `models/destino_model.dart`: Modelo de datos.
* `services/firebase_service.dart`: Lógica CRUD.
* `screens/home_screen.dart`: UI Principal.
* `screens/destino_form.dart`: Formulario Crear/Editar.

---

### Código: Modelo de Datos (`lib/models/destino_model.dart`)
```dart
class Destino {
  String id;
  String nombre;
  String ubicacion;
  double precio;

  Destino({required this.id, required this.nombre, required this.ubicacion, required this.precio});

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'ubicacion': ubicacion,
      'precio': precio,
    };
  }

  factory Destino.fromFirestore(Map<String, dynamic> data, String id) {
    return Destino(
      id: id,
      nombre: data['nombre'] ?? '',
      ubicacion: data['ubicacion'] ?? '',
      precio: (data['precio'] ?? 0).toDouble(),
    );
  }
}
```

### Código: Servicio CRUD (`lib/services/firebase_service.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/destino_model.dart';

class FirebaseService {
  final CollectionReference destinosRef = FirebaseFirestore.instance.collection('destinos');

  // Leer Destinos
  Stream<List<Destino>> getDestinos() {
    return destinosRef.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Destino.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  // Crear Destino
  Future<void> addDestino(Destino destino) => destinosRef.add(destino.toMap());

  // Actualizar Destino
  Future<void> updateDestino(Destino destino) => destinosRef.doc(destino.id).update(destino.toMap());

  // Borrar Destino
  Future<void> deleteDestino(String id) => destinosRef.doc(id).delete();
}
```

### Código: UI Principal (`lib/screens/home_screen.dart`)
```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/destino_model.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("LifeTours - Destinos")),
      body: StreamBuilder<List<Destino>>(
        stream: service.getDestinos(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          final destinos = snapshot.data!;
          return ListView.builder(
            itemCount: destinos.length,
            itemBuilder: (context, index) {
              final d = destinos[index];
              return ListTile(
                title: Text(d.nombre),
                subtitle: Text(d.ubicacion),
                trailing: IconButton(
                  icon: Icon(Icons.delete, color: Colors.red),
                  onPressed: () => service.deleteDestino(d.id),
                ),
                onTap: () { /* Navegar a edición */ },
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () { /* Navegar a formulario de creación */ },
      ),
    );
  }
}
```

---

## 5. Pruebas de Verificación
Para asegurar que todo funcione bajo la lógica del agente:
1.  **Ejecución:** `flutter run`.
2.  **Verificación Firestore:** Abre la consola de Firebase y observa cómo se crean los documentos al interactuar con la App.
3.  **Logs:** Usa el Debug Console de VS Code para monitorear errores de conexión.

> **Nota del Agente:** He estructurado el proyecto para que sea escalable. La carpeta `.agents` servirá como el manual de instrucciones para que cualquier IA o colaborador entienda cómo expandir **LifeTours**.

¿Deseas que profundice en el código del formulario de edición o prefieres configurar primero el login de Firebase?
