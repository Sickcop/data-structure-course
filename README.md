# data-structure-course
Interactive C program that simulates an airline ticketing system, allowing users to buy, modify, list, search, and cancel flights with seat assignment, passenger management, and automatic arrival time calculation.

# GOLONDRINA VELOZ - Sistema de Gestión de Tiquetes Aéreos ✈️

Este proyecto es un programa interactivo en C que simula el sistema básico de venta y administración de tiquetes de una aerolínea ficticia llamada "Golondrina Veloz".

Permite registrar pasajeros, asignar sillas, calcular horas de llegada y generar pases de abordar, todo desde consola.

---

## Características principales

El sistema soporta:

1. Comprar tiquete

   * Vuelo nacional (Pereira - Bogotá) o internacional (Bogotá - Madrid).
   * Primera clase (sillas 1-20) o clase económica (sillas 21-250).
   * Registro de datos personales del pasajero.
   * Validación de fechas (nacimiento en el pasado, vuelo en presente/futuro).
   * Asignación automática de silla disponible aleatoria según clase.
   * Cálculo automático de hora y fecha de llegada.

2. Modificar pasajero

   * Permite actualizar nombre, apellido, teléfono, fecha de nacimiento y género.

3. Listar pasajeros

   * Muestra todos los pasajeros en el sistema (sin detalles de vuelo).

4. Buscar pasajero

   * Busca por documento y muestra toda la información del pasajero, incluyendo itinerario.

5. Cambiar silla

   * Muestra las sillas libres dentro de la misma clase del pasajero.
   * Libera la silla anterior y asigna la nueva si está disponible.

6. Imprimir pase de abordar

   * Genera en consola un pase de abordar con:

     * Código de vuelo
     * Tipo de vuelo
     * Datos del pasajero
     * Clase
     * Silla
     * Fecha/hora de salida y llegada

7. Cancelar tiquete

   * Elimina al pasajero del sistema y libera la silla.

8. Salir

   * Libera memoria y termina el programa.

---

## Estructura de datos

### Pasajero (`struct Passenger`)

Cada pasajero registrado contiene:

* Tipo de vuelo (`flightType`)

  * Nacional (`FLIGHT_NATIONAL`)
  * Internacional (`FLIGHT_INTERNATIONAL`)

* Código de vuelo (`flightCode`)

  * "GOPLA01" para vuelo nacional
  * "GOPLA02" para vuelo internacional

* Datos personales

  * Documento (`document`)
  * Nombre (`firstName`)
  * Apellido (`lastName`)
  * Teléfono (`phone`)
  * Fecha de nacimiento (`birthDate`)
  * Género (`gender`) [F/M/O]

* Información de vuelo

  * Clase de tiquete (`ticketClass`)

    * Primera clase (`CLASS_FIRST`)
    * Clase económica (`CLASS_ECONOMY`)
  * Fecha y hora de salida (`flightDate`, `departureTime`)
  * Fecha y hora de llegada calculada automáticamente (`arrivalDate`, `arrivalTime`)
  * Número de silla asignada (`seatNumber`)

* Enlace al siguiente pasajero (`next`)

  * Se usa para mantener una lista enlazada de pasajeros.

### Mapa de sillas

```c
static bool seatMap[2][ECONOMY_CLASS_END + 1];
```

* Primera dimensión: tipo de vuelo (nacional / internacional).
* Segunda dimensión: número de silla (1 a 250).
* `true` significa ocupada, `false` disponible.

Rangos por clase:

* Primera clase: sillas 1 a 20
* Económica: sillas 21 a 250

---

## Lógica de vuelos y tiempos

* Vuelo nacional:

  * Duración fija: 50 minutos
  * Sin cambio de huso horario

* Vuelo internacional:

  * Duración base: 11 horas
  * Diferencia horaria adicional: +7 horas
  * Total aplicado: 18 horas (11h vuelo + 7h diferencia)

La función `compute_arrival` suma esa duración total a la fecha/hora de salida para calcular llegada.

```c
static void compute_arrival(
    FlightType type,
    Date departureDate,
    TimeOfDay departureTime,
    Date *arrivalDate,
    TimeOfDay *arrivalTime
);
```

---

## Validaciones importantes

* Fecha de nacimiento:

  * Debe estar en el pasado.

* Fecha y hora de vuelo:

  * Deben ser presentes o futuras respecto al reloj del sistema (`time(NULL)`).

* Género:

  * Debe ser F, M u O (otro).

* Documento:

  * Debe ser único. No se permite registrar dos pasajeros con el mismo documento.

* Cambio de silla:

  * Solo se puede elegir una silla disponible de la misma clase.

---

## Menú principal

El programa corre en bucle hasta que el usuario elige salir.

Menú mostrado:

```text
/////////////GOLONDRINA VELOZ//////////////////////////////
///////////////////////TIQUETES///////////////////////////////////////////
1. Comprar Tiquete
2. Modificar Pasajero
3. Listar Pasajeros
4. Buscar pasajero
5. Cambiar Silla
6. Imprimir pase de abordar
7. Cancelar Tiquete
8. Salir
```

---

## Cómo compilar

Necesitas un compilador C estándar (por ejemplo gcc).

Compilación:

```bash
gcc -Wall -Wextra -pedantic -std=c11 -o golondrina main.c
```

Donde `main.c` es el archivo que contiene el código fuente mostrado.

---

## Cómo ejecutar

Ejecuta el binario:

```bash
./golondrina
```

Después navegas con las opciones numéricas del menú.

Ejemplo rápido para comprar un tiquete:

1. Opción 1 (Comprar Tiquete)
2. Seleccionar tipo de vuelo (01 nacional / 02 internacional)
3. Ingresar documento, nombre, teléfono, etc.
4. Seleccionar clase (1 primera / 2 económica)
5. Ingresar fecha y hora del vuelo
6. El sistema asigna silla y calcula hora de llegada

---

## Organización interna del código

Funciones principales agrupadas por responsabilidad:

1. Entrada y validación

   * `read_line`
   * `parse_date`, `parse_time`
   * `read_birth_date`
   * `read_flight_datetime`
   * `read_gender`
   * `read_ticket_class`
   * `read_flight_type`

2. Manejo de fechas y horas

   * `days_in_month`
   * `datetime_to_time_t`, `time_t_to_datetime`
   * `format_date`, `format_time`
   * `is_future_or_present`
   * `is_past`
   * `compute_arrival`

3. Gestión de pasajeros

   * `buy_ticket`
   * `modify_passenger`
   * `list_passengers`
   * `search_passenger`
   * `cancel_ticket`
   * `print_boarding_pass`

4. Sillas

   * `assign_random_seat`
   * `show_available_seats`
   * `change_seat`
   * `release_seat`

5. Lista enlazada de pasajeros

   * `add_passenger`
   * `find_passenger`
   * `free_passengers`

6. Interfaz de usuario

   * `print_menu`
   * `main`

---

## Limitaciones conocidas

* Los datos no se guardan en archivo ni base de datos. Al salir del programa, se pierde todo.
* No hay control de sobreventa por vuelo más allá del mapa de sillas. El sistema asume un solo vuelo nacional y uno internacional, no múltiples horarios simultáneos.
* No hay precios ni cobro, solo registro.
* No hay validación de formato del teléfono ni del documento.

---

## Ideas de mejora futura

* Guardar y cargar pasajeros desde disco (por ejemplo .csv o .json).
* Reportes de ocupación por clase.
* Control por número específico de vuelo en una fecha/hora, no solo por tipo nacional/internacional.
* Validación más estricta de entrada (por ejemplo número de teléfono).
* UI gráfica o versión web.

---

## Créditos

Proyecto académico en C orientado a practicar:

* Manejo de estructuras (`struct`)
* Listas enlazadas
* Validación de entrada de usuario
* Manejo de fechas y horas con `time.h`
* Simulación de un sistema de reservas aéreas en consola

Golondrina Veloz agradece su preferencia 🕊️✈️
