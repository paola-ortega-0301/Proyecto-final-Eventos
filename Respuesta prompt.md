¡Hola! Me parece un proyecto excelente. Estructurar tu automatización con una habilidad de agente global .agents te dará una base muy sólida para manejar tus flujos de diseño, código y web scraping.

He notado un pequeño detalle en tu solicitud: mencionas que la app es para una veterinaria, pero nombras la carpeta principal como proyectolavanderia. Para mantener la coherencia con los comandos, usaré proyectolavanderia como nombre del proyecto, pero el CRUD estará enfocado en un modelo de "Servicios" genérico que puedes adaptar fácilmente (por ejemplo: "Corte de pelo" para veterinaria, o "Lavado en seco" para lavandería).

Aquí tienes la guía lógica y el código para armar todo tu entorno de trabajo y el proyecto.

Paso 1: Estructura de la Habilidad Agente Global (.agents)
Primero, vamos a crear la estructura de carpetas para tu automatización. Puedes hacer esto en la raíz de tu espacio de trabajo.

mkdir .agents
cd .agents
touch SKILL.md
mkdir scripts ejemplos resources
SKILL.md sugerido:

# Habilidad Global: Automatización de Desarrollo
Esta habilidad integra herramientas de diseño, generación de código y scraping.
- **scripts/**: Contiene los scripts de automatización (Python, Bash, etc.).
- **ejemplos/**: Inputs y outputs de prueba para los agentes.
- **resources/**: Assets, plantillas de UI y bases de datos locales.
Paso 2: Prerrequisitos y Configuración del Entorno
Asegúrate de tener instalado Flutter y Node.js. Abre tu terminal (en VS Code o IDE Antigravity) y ejecuta las siguientes verificaciones:

1. Verificar Flutter:

flutter doctor
Si no está instalado, descárgalo desde la página oficial de Flutter y agrégalo a tu PATH.

2. Instalar herramientas de Firebase: Para conectar Firestore, necesitamos Firebase CLI y FlutterFire CLI (que actúa como tu base para enlazar el proyecto).

# Instalar Firebase CLI
npm install -g firebase-tools

# Iniciar sesión en Firebase Console
firebase login

# Instalar FlutterFire CLI
dart pub global activate flutterfire_cli
Paso 3: Creación del Proyecto Flutter (proyectolavanderia)
Sal de la carpeta .agents y crea tu aplicación:

cd ..
flutter create proyectolavanderia
cd proyectolavanderia
Conectar con Firebase: Asegúrate de haber creado tu proyecto en la Consola de Firebase y activado Firestore Database. Luego ejecuta:

flutterfire configure
Sigue las instrucciones en la terminal, selecciona tu proyecto de Firebase y las plataformas (Android/iOS).

Paso 4: Preparar el Entorno (pubspec.yaml)
Abre el proyecto en tu IDE y añade las dependencias necesarias en el archivo pubspec.yaml bajo la sección dependencies::

dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.30.0 # Verifica las versiones más recientes
  cloud_firestore: ^4.17.0
Ejecuta el siguiente comando para descargar las dependencias:

flutter pub get
Paso 5: Código Funcional del Proyecto (CRUD de Servicios)
Vamos a crear la estructura de carpetas dentro de lib/.

lib/
├── firebase_options.dart (Generado automáticamente por flutterfire)
├── main.dart
├── models/
│   └── servicio.dart
└── screens/
    ├── home_screen.dart
    └── servicio_form_screen.dart
1. Modelo de Datos (lib/models/servicio.dart)
Define la estructura del servicio.

class Servicio {
  String id;
  String nombre;
  String descripcion;
  double precio;

  Servicio({
    required this.id,
    required this.nombre,
    required this.descripcion,
    required this.precio,
  });

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'descripcion': descripcion,
      'precio': precio,
    };
  }

  factory Servicio.fromMap(Map<String, dynamic> map, String documentId) {
    return Servicio(
      id: documentId,
      nombre: map['nombre'] ?? '',
      descripcion: map['descripcion'] ?? '',
      precio: (map['precio'] ?? 0.0).toDouble(),
    );
  }
}
2. Pantalla Principal - Leer y Borrar (lib/screens/home_screen.dart)
Esta pantalla lee los datos en tiempo real de Firestore.

import 'package:flutter/material.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/servicio.dart';
import 'servicio_form_screen.dart';

class HomeScreen extends StatelessWidget {
  final CollectionReference _serviciosRef =
      FirebaseFirestore.instance.collection('servicios');

  HomeScreen({super.key});

  void _borrarServicio(String id) {
    _serviciosRef.doc(id).delete();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Gestión de Servicios'),
      ),
      body: StreamBuilder(
        stream: _serviciosRef.snapshots(),
        builder: (context, AsyncSnapshot<QuerySnapshot> snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          }
          if (!snapshot.hasData || snapshot.data!.docs.isEmpty) {
            return const Center(child: Text('No hay servicios disponibles.'));
          }

          return ListView.builder(
            itemCount: snapshot.data!.docs.length,
            itemBuilder: (context, index) {
              var doc = snapshot.data!.docs[index];
              var servicio = Servicio.fromMap(doc.data() as Map<String, dynamic>, doc.id);

              return Card(
                margin: const EdgeInsets.symmetric(horizontal: 10, vertical: 5),
                child: ListTile(
                  title: Text(servicio.nombre),
                  subtitle: Text('${servicio.descripcion} - \$${servicio.precio}'),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(
                        icon: const Icon(Icons.edit, color: Colors.blue),
                        onPressed: () {
                          Navigator.push(
                            context,
                            MaterialPageRoute(
                              builder: (context) => ServicioFormScreen(servicio: servicio),
                            ),
                          );
                        },
                      ),
                      IconButton(
                        icon: const Icon(Icons.delete, color: Colors.red),
                        onPressed: () => _borrarServicio(servicio.id),
                      ),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => const ServicioFormScreen()),
          );
        },
        child: const Icon(Icons.add),
      ),
    );
  }
}
3. Pantalla de Formulario - Crear y Actualizar (lib/screens/servicio_form_screen.dart)
import 'package:flutter/material.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/servicio.dart';

class ServicioFormScreen extends StatefulWidget {
  final Servicio? servicio;

  const ServicioFormScreen({super.key, this.servicio});

  @override
  State<ServicioFormScreen> createState() => _ServicioFormScreenState();
}

class _ServicioFormScreenState extends State<ServicioFormScreen> {
  final _formKey = GlobalKey<FormState>();
  final _nombreController = TextEditingController();
  final _descripcionController = TextEditingController();
  final _precioController = TextEditingController();

  @override
  void initState() {
    super.initState();
    if (widget.servicio != null) {
      _nombreController.text = widget.servicio!.nombre;
      _descripcionController.text = widget.servicio!.descripcion;
      _precioController.text = widget.servicio!.precio.toString();
    }
  }

  Future<void> _guardarServicio() async {
    if (_formKey.currentState!.validate()) {
      final data = {
        'nombre': _nombreController.text,
        'descripcion': _descripcionController.text,
        'precio': double.parse(_precioController.text),
      };

      if (widget.servicio == null) {
        // Crear
        await FirebaseFirestore.instance.collection('servicios').add(data);
      } else {
        // Actualizar
        await FirebaseFirestore.instance
            .collection('servicios')
            .doc(widget.servicio!.id)
            .update(data);
      }
      if (mounted) Navigator.pop(context);
    }
  }

  @override
  Widget build(BuildContext context) {
    final esEdicion = widget.servicio != null;
    return Scaffold(
      appBar: AppBar(title: Text(esEdicion ? 'Editar Servicio' : 'Nuevo Servicio')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(
                controller: _nombreController,
                decoration: const InputDecoration(labelText: 'Nombre del Servicio'),
                validator: (v) => v!.isEmpty ? 'Campo requerido' : null,
              ),
              TextFormField(
                controller: _descripcionController,
                decoration: const InputDecoration(labelText: 'Descripción'),
                validator: (v) => v!.isEmpty ? 'Campo requerido' : null,
              ),
              TextFormField(
                controller: _precioController,
                decoration: const InputDecoration(labelText: 'Precio'),
                keyboardType: TextInputType.number,
                validator: (v) => v!.isEmpty ? 'Campo requerido' : null,
              ),
              const SizedBox(height: 20),
              ElevatedButton(
                onPressed: _guardarServicio,
                child: Text(esEdicion ? 'Actualizar' : 'Crear'),
              )
            ],
          ),
        ),
      ),
    );
  }
}
4. Punto de Entrada (lib/main.dart)
Conectamos la inicialización de Firebase y lanzamos la aplicación.

import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';
import 'screens/home_screen.dart';

void main() async {
  // Asegura que los bindings de Flutter estén inicializados antes de Firebase
  WidgetsFlutterBinding.ensureInitialized();
  
  // Inicializa Firebase usando las opciones generadas por flutterfire cli
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'App Lavandería / Veterinaria',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: HomeScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}
Paso 6: Ejecución y Pruebas
Para probar la aplicación y verificar la funcionalidad completa del entorno:

Levanta un emulador de Android o conecta tu dispositivo.
Ejecuta en la terminal:
flutter run
Ahora tu estructura base automatizada, el proyecto de Flutter y Firebase están completamente conectados y listos. El CRUD funciona en tiempo real gracias a Firestore.
