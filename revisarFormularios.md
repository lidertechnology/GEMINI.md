Aquí tienes la convención de formularios actualizada y definitiva para Lidertech.

---

## 📋 Convención Definitiva de Formularios Lidertech

Esta convención unifica el manejo de estado, la lógica de negocio y la **normalización de datos clave** (como `.toLowerCase()`) para asegurar la consistencia y la eficiencia de la búsqueda en toda la arquitectura.

### 1. TypeScript (Lógica del Componente)

* **Framework:** Se deben usar **Formularios Reactivos** (`ReactiveFormsModule`). El uso de `ngModel` está prohibido.
* **Nombres de Variables:**
    * La instancia de `FormGroup` **siempre** se llamará `formGroup`.
    * El `FormBuilder` inyectado **siempre** se llamará `formBuilder`.
* **Manejo de Estado:**
    * El componente **debe** gestionar su estado local usando un *signal* llamado `estadoActual`, tipado con el `StatesEnum`.
    * Se **debe** exponer el `StatesEnum` al template en una variable pública llamada `stateEnum`.
* **Lógica de Envío (Método `async`):**
    * Toda la lógica de envío **debe** estar envuelta en un bloque `try/catch`.
    * El `estadoActual` **debe** cambiar a `StatesEnum.CARGANDO` antes del `try`.
    * Se **debe** usar `snackBarService` para notificar al usuario el resultado (`LISTO` o `ERROR`).
* **Regla Clave de Normalización:**
    * Antes de enviar los datos al `WriteService`, todos los campos de texto destinados a la búsqueda (como `nombre`, `categoria`, `email`) **deben** ser normalizados usando el método **`.toLowerCase()`** de JavaScript.

### 2. HTML (Template)

* **Sintaxis:** Se **debe** usar la nueva sintaxis de control de flujo (`@if`, `@switch`).
* **Contenedor:** El contenedor principal del formulario **debe** usar las clases de `box.css` (ej. `box-responsive`).
* **Manejo de Carga:**
    * El formulario (o un `fieldset` que lo contenga) **debe** estar deshabilitado (`[disabled]`) cuando `estadoActual()` sea `StatesEnum.CARGANDO`.
    * Se **debe** mostrar un indicador de carga (`mat-progress-bar` o `mat-spinner`) durante el estado `CARGANDO`.
