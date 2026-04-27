## Aquí tienes un ejemplo claro de cómo podrías diseñar una **base de datos documental (tipo NoSQL, como MongoDB)** para una app de agencia de tours. En este modelo, cada **colección** contiene documentos (JSON) con atributos y tipos de datos.

---

## 📁 Colección: `usuarios`

Representa a los clientes que usan la app.

```json
{
  "_id": ObjectId,
  "nombre": String,
  "correo": String,
  "telefono": String,
  "password": String,
  "fecha_registro": Date,
  "rol": String, 
  "favoritos": [ObjectId]
}
```

---

## 📁 Colección: `tours`

Información de los tours disponibles.

```json
{
  "_id": ObjectId,
  "titulo": String,
  "descripcion": String,
  "precio": Number,
  "duracion_horas": Number,
  "ubicacion": {
    "ciudad": String,
    "pais": String,
    "coordenadas": {
      "lat": Number,
      "lng": Number
    }
  },
  "imagenes": [String],
  "categoria": String,
  "calificacion_promedio": Number,
  "activo": Boolean,
  "fecha_creacion": Date
}
```

---

## 📁 Colección: `reservas`

Registra las reservaciones hechas por los usuarios.

```json
{
  "_id": ObjectId,
  "usuario_id": ObjectId,
  "tour_id": ObjectId,
  "fecha_reserva": Date,
  "fecha_tour": Date,
  "numero_personas": Number,
  "estado": String, 
  "total_pagado": Number
}
```

---

## 📁 Colección: `pagos`

Información de pagos realizados.

```json
{
  "_id": ObjectId,
  "reserva_id": ObjectId,
  "metodo_pago": String,
  "monto": Number,
  "fecha_pago": Date,
  "estado_pago": String,
  "transaccion_id": String
}
```

---

## 📁 Colección: `reseñas`

Opiniones de los usuarios sobre los tours.

```json
{
  "_id": ObjectId,
  "usuario_id": ObjectId,
  "tour_id": ObjectId,
  "calificacion": Number,
  "comentario": String,
  "fecha": Date
}
```

---

## 📁 Colección: `guias`

Información de los guías turísticos.

```json
{
  "_id": ObjectId,
  "nombre": String,
  "idiomas": [String],
  "experiencia_anios": Number,
  "telefono": String,
  "correo": String,
  "tours_asignados": [ObjectId]
}
```

---

## 📁 Colección: `categorias`

Para clasificar los tours.

```json
{
  "_id": ObjectId,
  "nombre": String,
  "descripcion": String
}
```

---

## 🔗 Relaciones (referencias)

* `usuario_id` → referencia a `usuarios`
* `tour_id` → referencia a `tours`
* `reserva_id` → referencia a `reservas`
* `tours_asignados` → relación con `tours`

---

## 💡 Notas importantes

* En bases documentales puedes **embeder datos** (ej: ubicación dentro de tours).
* O usar **referencias** (como IDs) cuando la relación es grande o reutilizable.
* Puedes añadir índices en:

  * `correo` (usuarios)
  * `ubicacion.ciudad` (tours)
  * `calificacion_promedio`

---

Si quieres, puedo:

* Convertir este diseño a **MongoDB real (con comandos)**
* O adaptarlo a **Firebase / Firestore**
* O incluso hacerte el equivalente en **base de datos relacional (SQL)**
