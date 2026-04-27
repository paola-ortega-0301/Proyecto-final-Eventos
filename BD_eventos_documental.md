Claro. Si piensas en una base de datos NoSQL como MongoDB (usa **base de datos → colecciones → documentos**), una empresa de eventos podría estructurarse así:

## Base de datos: `empresa_eventos`

### Colección: `clientes`

Guarda información de los clientes que contratan eventos.

**Documento ejemplo:**

```json
{
  "_id": ObjectId("001"),
  "nombre": "María López",
  "telefono": "6561234567",
  "correo": "maria@email.com",
  "direccion": {
      "calle": "Av. Reforma 120",
      "ciudad": "Ciudad Juárez",
      "estado": "Chihuahua"
  },
  "fecha_registro": ISODate("2025-01-10"),
  "tipo_cliente": "Frecuente"
}
```

### Atributos y tipos de datos:

| Atributo       | Tipo de dato       |
| -------------- | ------------------ |
| _id            | ObjectId           |
| nombre         | String             |
| telefono       | String             |
| correo         | String             |
| direccion      | Documento embebido |
| fecha_registro | Date               |
| tipo_cliente   | String             |

---

## Colección: `eventos`

Información de cada evento organizado.

**Documento ejemplo:**

```json
{
 "_id": ObjectId("E001"),
 "nombre_evento": "Boda de Ana y Luis",
 "tipo_evento": "Boda",
 "fecha": ISODate("2025-08-20"),
 "ubicacion": {
    "salon": "Jardines del Sol",
    "direccion": "Av. Tecnológico 500"
 },
 "invitados": 180,
 "presupuesto": 95000.50,
 "cliente_id": ObjectId("001"),
 "estatus": "En planeación"
}
```

### Atributos:

| Atributo      | Tipo                  |
| ------------- | --------------------- |
| _id           | ObjectId              |
| nombre_evento | String                |
| tipo_evento   | String                |
| fecha         | Date                  |
| ubicacion     | Documento             |
| invitados     | Integer               |
| presupuesto   | Decimal               |
| cliente_id    | ObjectId (referencia) |
| estatus       | String                |

---

## Colección: `proveedores`

Información de proveedores.

**Documento ejemplo:**

```json
{
 "_id": ObjectId("P001"),
 "nombre_empresa": "Sonido Premium",
 "servicio": "Audio e iluminación",
 "telefono": "6569876543",
 "correo": "ventas@sonidopremium.com",
 "costo_servicio": 15000,
 "disponible": true
}
```

### Atributos:

| Atributo       | Tipo     |
| -------------- | -------- |
| _id            | ObjectId |
| nombre_empresa | String   |
| servicio       | String   |
| telefono       | String   |
| correo         | String   |
| costo_servicio | Number   |
| disponible     | Boolean  |

---

## Colección: `empleados`

Personal que trabaja en eventos.

**Documento ejemplo:**

```json
{
 "_id": ObjectId("EMP01"),
 "nombre": "Carlos Pérez",
 "puesto": "Coordinador",
 "telefono": "6561122334",
 "salario": 18000,
 "fecha_ingreso": ISODate("2024-06-15")
}
```

---

## Colección: `pagos`

Registro de pagos de clientes.

**Documento ejemplo:**

```json
{
 "_id": ObjectId("PG001"),
 "cliente_id": ObjectId("001"),
 "evento_id": ObjectId("E001"),
 "monto": 30000,
 "metodo_pago": "Transferencia",
 "fecha_pago": ISODate("2025-03-01"),
 "anticipo": true
}
```

---

## Relación entre colecciones

* **Clientes** → contratan **Eventos**
* **Eventos** → usan **Proveedores**
* **Eventos** → asignan **Empleados**
* **Eventos** → generan **Pagos**

---

## Ejemplo de documento más completo con arreglos (modelo NoSQL)

Podrías guardar un evento con proveedores y empleados embebidos:

```json
{
 "nombre_evento":"XV Años Sofía",
 "fecha":"2025-10-15",
 "cliente":{
    "nombre":"Laura García",
    "telefono":"6565554444"
 },
 "proveedores":[
    {
      "nombre":"Decoraciones Elite",
      "servicio":"Decoración",
      "costo":12000
    },
    {
      "nombre":"DJ Max",
      "servicio":"Música",
      "costo":8000
    }
 ],
 "empleados":[
    {
      "nombre":"Pedro",
      "puesto":"Coordinador"
    }
 ],
 "total":65000
}
```

Tipos usados:

* **String** → texto
* **Integer/Number** → números
* **Decimal** → montos
* **Boolean** → verdadero/falso
* **Date** → fechas
* **Array** → listas
* **Object/Documento** → subdocumentos
* **ObjectId** → identificador único

Si lo necesitas para un modelo relacional en MySQL (tablas) o para un proyecto escolar tipo diagrama, también puedo ayudarte.
