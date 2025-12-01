# GEMINI.md
Archivo de convenciones Lidertech para usar dentro del editor firebase studio en proyectos Angular de Lidertech.

# ARQUITECTURA ÚNCA PARA TODA LA APP ArquiLiderL10:

  + Todo el proyecto contiene solo 10 directorios principales dentro del src/app.
  + Los directorios principales son:
    * auth: directorio donde residen toda la seguridad de la app, authts, autorizador.ts, enums de roles.
    * components: componentes reusables tontos y lógica simple.
    * config: directorio donde se guardan servicios de instancias de firebase y otros proveedores, conecciones a la infraestructura.
    * core: alberga directorios de infraestructura como firebase, aws, google, servicios acopladores.
    * global: directorio que almacena servicio de estados globales de la app (globalStates.ts) y enum de estados.
    * interfaces: todas las interfaces existente en el proyectos (modelos de datos).
    * tools: servicios, enums y const de utilidad que se requieren en cualquier componente.
    * views: guardará componentes con UI, es el unico liugar admitido para eso.
   + Recomendamos reviasar el glosario Lidertech.

+++++++++++++++++++++++++++++++++++
# MANEJO DE ESTADOS MUTABILIDAD UNICA Y SEGREGACIÓN DE LA MUTABILIDAD DE ESTADOS.


Esta es la regla más importante para garantizar la arquitectura inmutable y prevenir colisiones de estado en todas las aplicaciones Lidertech.

La mutación (escritura) de cualquier estado es una responsabilidad exclusiva del servicio que lo gestiona:

Estados Globales de Identidad: El servicio GlobalState es el único punto donde se permite mutar el estado de la sesión o el rol de usuario (ej., llamar a establecerSesionActiva(true)).

Estados Operacionales: Cada Servicio Operacional (ej., ReadService, WriteService) es el único responsable de mutar su propio estado de flujo de trabajo tipificado con StateEnum (ej., llamar a this.estado.set(StateEnum.CARGANDO)).

## Regla de Oro:

Ningún componente, ni ningún otro servicio, puede mutar el estado de un servicio ajeno.

* Los componentes y los Servicios de Utilidad (Stateless) solo tienen permiso de acceso de solo lectura a los signals públicos de cualquier servicio.
  Esto garantiza que una acción en un componente no cause efectos secundarios inesperados o colisiones de estado en otra parte de la aplicación.


+++++++++++++++++++++++++++++++++++
# ENUM DE ESTADOS PARA SERVICIOS Y COMPONENTES:


  El diccionario de estados universal para todas las divisiones de Lidertech se llamará estrictamente StateEnum (singular, sin 's').
  Alcance: Este enum es inmutable y debe ser la única fuente de vocabulario para describir el estado operacional, de conectividad y de flujo de cualquier componente o servicio en todas las aplicaciones de Lidertech durante los próximos 30 años.
  Convención de Nombres: Las claves dentro del enum deben estar en MAYÚSCULAS y sus valores asociados en minúsculas (ej. CARGANDO = 'cargando').
  Ubicación: El archivo debe residir en un directorio central de utilidades, llamado preferiblemente states/state.enum.ts o similar.

  Contenido del Enum de estados único y universal.

    export enum StateEnum {
      // --- Ciclo de Vida General ---
      INICIAL = 'inicial',      
      CARGANDO = 'cargando',    
      PAGINANDO = 'paginando',  
      PROCESANDO = 'procesando',
      PENDIENTE_RED = 'pendiente_red', // Para sistemas asíncronos y Blockchain (LiderFinance).
      
      // --- Resultados ---
      EXITO = 'exito', ERROR = 'error', TIMEOUT = 'timeout',      
  
      // --- Contenido ---
      SIN_RESULTADOS = 'sin_resultados', // La consulta se ejecutó con éxito pero no arrojó resultados.
      VACIO = 'vacio', // El estado representa intencionadamente un conjunto de datos vacío (ej. una lista vaciada por el usuario), no es un resultado de una búsqueda.
      
      // --- Interacción / Autorización ---
      DESHABILITADO = 'deshabilitado', AUTENTICANDO = 'autenticando',AUTORIZADO = 'autorizado',  NO_AUTORIZADO = 'no_autorizado',
  
      // --- Conectividad / Hardware ---
      EN_LINEA = 'en_linea',    FUERA_LINEA = 'fuera_linea', CONECTADO = 'conectado',  DESCONECTADO = 'desconectado',MONITOREANDO = 'monitoreando',FALLO_SENSOR = 'fallo_sensor',
      CALIBRANDO = 'calibrando',// Para setup de hardware o sistemas (LiderAuto, LiderAutomate).
  
      // --- Flujo / Multimedia ---
      REPRODUCIENDO = 'reproduciendo', PAUSADO = 'pausado', LISTO = 'listo',   TERMINADO = 'terminado',  
  }







# ESTADOS GLOBALES DE LA APLICACIÓN:

  El servicio de estado global se llamará exclusivamente GlobalState. 
  Su alcance es estrictamente limitado a gestionar la Identidad, la Sesión y la Disponibilidad de la aplicación (ej., rolDeUsuario, sesionActiva). 
  Se prohíbe que este servicio almacene o gestione estados operacionales genéricos (como CARGANDO, PAGINANDO, PROCESANDO), los cuales deben ser manejados por los servicios o componentes locales correspondientes. 
  
  Para garantizar la inmutabilidad (QI 1000%), el estado interno se almacena en WritableSignal privados y se expone públicamente solo a través de propiedades de Signal<T> de solo lectura. 
  
  **Nota sobre Nomenclatura:** A diferencia de los servicios operacionales, los signals de GlobalState usan nombres descriptivos (ej. `sesionActiva`) en lugar del genérico `stateEnumGlobal`, porque cada signal gestiona una pieza de estado única y fuertemente tipada (`boolean`, `RolUsuario`), no el `StateEnum` general. El patrón de seguridad `private _variable` / `public variable` es idéntico.

      import { Injectable, signal, Signal, WritableSignal } from '@angular/core';
      import { RolUsuario } from 'src/app/interfaces/perfil-interface'; 
      
      @Injectable({ providedIn: 'root' })
      export class GlobalState {
      
        private readonly _sesionActiva:    WritableSignal<boolean>           = signal(false);
        private readonly _rolDeUsuario:    WritableSignal<RolUsuario | null> = signal(null);
        private readonly _aplicacionLista: WritableSignal<boolean>           = signal(false);
      
        public readonly sesionActiva:    Signal<boolean>           = this._sesionActiva.asReadonly();
        public readonly rolDeUsuario:    Signal<RolUsuario | null> = this._rolDeUsuario.asReadonly();
        public readonly aplicacionLista: Signal<boolean>           = this._aplicacionLista.asReadonly();
      
        public establecerSesionActiva   (valor: boolean): void         { this._sesionActiva.set(valor); }
        public establecerRolDeUsuario   (rol: RolUsuario | null): void { this._rolDeUsuario.set(rol); }
        public establecerAplicacionLista(valor: boolean): void         { this._aplicacionLista.set(valor); }
        
      }


+++++++++++++++++++++++++++++++++++
# ESTADOS EN COMPONENTES:

  Crearemos siempre la variable signal que contiene el estado local del componente, 
  utilizada para acceder al estado en las plantillas (templates), se llamará exclusivamente stateEnum.

  * Ejemplo de Declaración en Componente:

        // En el TypeScript del componente:
        public readonly stateEnum = signal<StateEnum>(StateEnum.INICIAL);
    
        // En la plantilla (template):
        // @if (stateEnum() === StateEnum.CARGANDO) { ... }




+++++++++++++++++++++++++++++++++++
# SERVICIOS DE APLICACIONES LIDERTECH Y tipos Y USOS:

  En la arquitectura Lidertech, los servicios se dividen en dos categorías basadas en su gestión de estado, para asegurar el Principio de Responsabilidad Única:

* Servicios Operacionales con Estado Interno: Responsables de ejecutar flujos de trabajo asíncronos (lectura, escritura, transacciones). Deben gestionar su propio ciclo de vida utilizando un signal tipificado con StateEnum (ej. ReadService, WriteService).

* Servicios de Utilidad (Stateless): Responsables de funcionalidades puntuales, enfocadas y sin estado persistente. No gestionan el StateEnum ya que su ejecución es directa (ej. AutorizadorService, CodificadorService).




+++++++++++++++++++++++++++++++++++
# Uso de StateEnum unico en la app:

  El uso del diccionario de estado StateEnum está estrictamente delimitado por la función del servicio:

  * Servicios Operacionales con Estado Interno: Siempre deben usar el StateEnum para gestionar y exponer su ciclo de vida (ej., CARGANDO, PROCESANDO, EXITO). 

  * Servicios de Utilidad (Stateless): Nunca deben usar el StateEnum. Estos servicios son herramientas funcionales cuya ejecución es inmediata y directa.
    El estado de la operación (si una funcionalidad falla o no) es responsabilidad del componente o del Servicio Operacional que invoca a la utilidad.
    Los servicios de utilidad siempre serán guardados dentro del directorio src/app/tools.



+++++++++++++++++++++++++++++++++++
# Servicios de Utilidad de Lidertech:

  Los servicios de Utilidad se guardarán siempre en src/app/tools.

- **Excepción de Ubicación para Servicios de Dominio:** Los servicios de utilidad que pertenecen a un dominio de negocio específico (como `auth` o `pagos`) deben permanecer en su directorio de dominio correspondiente (ej: `src/app/auth/autorizador.service.ts`). Esto prevalece sobre la regla general de mover todos los servicios de utilidad a `src/app/tools/`, ya que mantiene la cohesión lógica del dominio.


+++++++++++++++++++++++++++++++++++
# Servicios Operacionales de Lidertech:

  Los servicios operacionales serán guardaos en directorios según su uso de origen ejemplo:
  
  * Los servicios que trabajan con firebase se crearan en el directorio src/app/firebase.
  * Los Servicios de APIS de Google se guardarán en el directorio src/app/google.
  * Los Servicios operacionales de APIS de Redes sociales se guardarán en src/app/rrss.


+++++++++++++++++++++++++++++++++++
# SEGURIDAD EN LAS APPS LIDERTECH

Para garantizar una seguridad unificada, predecible y robusta en todas las aplicaciones de Lidertech, el directorio `src/app/auth` contendrá exclusivamente los siguientes tres (3) archivos maestros, que conforman el núcleo de la seguridad de la aplicación.

### 1. `auth.ts` (El Servicio de Autenticación)

*   **Responsabilidad Única:** Gestionar el ciclo de vida de la autenticación del usuario. Es el único servicio que puede comunicarse con un proveedor externo (Firebase Auth, Auth0, etc.) para realizar operaciones como `iniciarSesion()`, `cerrarSesion()` y escuchar los cambios en el estado de autenticación.
*   **Gestión de Estado:**
    *   **Estado Operacional:** Debe implementar el **Patrón de Signal de Estado Dual** (`_stateEnumAuth` / `stateEnumAuth`) para gestionar el estado de las operaciones (`CARGANDO`, `EXITO`, `ERROR`).
    *   **Datos de Usuario:** Debe almacenar la información cruda del usuario autenticado (ej. el objeto `User` de Firebase) y su perfil de la base de datos. Estos datos se expondrán como `signals` de solo lectura.
*   **Nomenclatura:** `AuthService`

### 2. `autorizador.ts` (El Servicio de Autorización)

*   **Responsabilidad Única:** Determinar los permisos de un usuario autenticado. Su función es traducir el "rol" o los permisos del usuario (obtenidos del `AuthService`) en `signals` booleanos, reactivos y fáciles de consumir.
*   **Implementación:** Este servicio debe ser **Stateless**. No gestiona su propio `StateEnum`. Depende reactivamente del estado expuesto por `AuthService`. Su lógica se basa en `signals` computados (`computed`).
*   **Nomenclatura:** `Autorizador` o `AutorizadorService`. Las propiedades que expone deben ser auto-descriptivas, como `esAdmin: Signal<boolean>`, `puedeVerFacturas: Signal<boolean>`.

### 3. `auth-guard.ts` (El Guardián de Rutas)

*   **Responsabilidad Única:** Proteger las rutas de la aplicación. Actúa como el punto de control para el router de Angular, decidiendo si un usuario puede o no acceder a una página específica.
*   **Implementación:** Debe ser una función `CanActivateFn` (el estándar moderno de Angular). Inyectará el `AutorizadorService` o el `AuthService` para tomar decisiones basadas en los `signals` de estado de sesión o de rol. Si el acceso es denegado, es responsable de redirigir al usuario a una página adecuada (ej. `/login` o `/acceso-denegado`).
*   **Nomenclatura:** `authGuard`

---


+++++++++++++++++++++++++++++++++++
# Convención Universal: El Patrón de Signal de Estado Dual

Para garantizar la seguridad, inmutabilidad y claridad en la gestión del estado de los **Servicios Operacionales** (aquellos que realizan tareas asíncronas como leer o escribir datos), implementaremos obligatoriamente el **Patrón de Signal de Estado Dual**.

Este patrón se basa en la separación de responsabilidades de escritura y lectura, utilizando dos `signals` que trabajan en conjunto:

1.  **El Signal Privado (El "Motor Interno")**
2.  **El Signal Público (El "Panel de Control")**

---


+++++++++++++++++++++++++++++++++++
### 1. El Signal Privado: `_stateEnum[NombreDelServicio]`

Este es el `signal` que el servicio utiliza para gestionar **su propio estado interno**.

*   **Propósito:** Es la única variable que el servicio puede **modificar**. Se usa dentro de los métodos del servicio (`obtenerDocumentos`, `crearUsuario`, etc.) para actualizar el ciclo de vida de la operación (`CARGANDO`, `EXITO`, `ERROR`).
*   **Visibilidad:** `private readonly`.
    *   `private`: Asegura que ningún componente o servicio externo pueda acceder a él. Es de uso exclusivo de la clase.
    *   `readonly`: Previene que la referencia al `signal` en sí misma sea reasignada.
*   **Tipo:** `WritableSignal<StateEnum>`. Necesita ser mutable para poder usar `.set()` y `.update()`.
*   **Nomenclatura:** `_` + `[Nombre del Signal Público]`. El guion bajo `_` es una convención universal para indicar que es una propiedad privada.
    *   **Ejemplo:** `private readonly _stateEnumRead = signal<StateEnum>(StateEnum.INICIAL);`

### 2. El Signal Público: `stateEnum[NombreDelServicio]`

Este es el `signal` que el servicio expone al mundo exterior para que otros puedan **observar su estado** de forma segura.

*   **Propósito:** Es el "panel de control" que los componentes, directivas y otros servicios consumen para reaccionar a los cambios de estado (por ejemplo, para mostrar un spinner o un mensaje de error).
*   **Visibilidad:** `public readonly`.
    *   `public`: Para que cualquier consumidor pueda inyectar el servicio y leer su estado.
*   **Tipo:** `Signal<StateEnum>`. Se obtiene aplicando `.asReadonly()` al `signal` privado. Esto elimina los métodos `.set()` y `.update()`, haciéndolo **inmutable** desde el exterior.
*   **Nomenclatura:** `stateEnum` + `[Nombre del Servicio en PascalCase]`. Esta convención es clara, descriptiva y evita colisiones de nombres cuando un componente inyecta múltiples servicios.
    *   **Ejemplo:** `public readonly stateEnumRead = this._stateEnumRead.asReadonly();`

---


### Ejemplos de Aplicación Universal:

#### Para `ReadService` (¡Ya implementado!):

```typescript
// Privado, para uso interno
private readonly _stateEnumRead = signal<StateEnum>(StateEnum.INICIAL);
// Público, para consumo externo
public readonly stateEnumRead = this._stateEnumRead.asReadonly();
```

#### Para un futuro `AuthService`:

```typescript
// Privado, para uso interno
private readonly _stateEnumAuth = signal<StateEnum>(StateEnum.INICIAL);
// Público, para consumo externo
public readonly stateEnumAuth = this._stateEnumAuth.asReadonly();
```

#### Para un futuro `WriteService`:

```typescript
// Privado, para uso interno
private readonly _stateEnumWrite = signal<StateEnum>(StateEnum.INICIAL);
// Público, para consumo externo
public readonly stateEnumWrite = this._stateEnumWrite.asReadonly();
```

Esta convención es la piedra angular de nuestra arquitectura QI 1000%. Garantiza que el estado de los servicios sea robusto, predecible y a prueba de manipulaciones accidentales.

---


+++++++++++++++++++++++++++++++++++
# INTERACCIÓN CON GEMINI (IA ASISTENTE)

- **Proactividad:** Espero que seas proactivo. Si identificas una mejora o un refactor que se alinea con estas guías, siéntete libre de proponerlo o aplicarlo directamente.
- **Contexto:** Antes de realizar una tarea, revisa los archivos relevantes para entender el contexto completo.
- **Confirmación:** No pidas confirmación para cada paso. Procede con el plan de acción y solo pregunta si la intención original es ambigua o el cambio es muy grande o destructivo.
















## Auditorías de Código Automatizadas

Para interactuar con el sistema de auditorías, utiliza las siguientes frases clave, diseñadas para ser claras y flexibles:

### 1. Auditoría General
Para ejecutar una revisión completa de todo el proyecto en busca de desviaciones de las convenciones de este documento:
- **`Lider audita el proyecto`**

### 2. Auditoría por Archivo Específico
Para auditar un archivo concreto, sin importar si es un componente, servicio u otro tipo de archivo:
- **`Lider audita [ruta/completa/al/archivo.ts]`**
- *Ejemplo: `Lider audita src/app/components/search/search.ts`*

### 3. Auditoría Contextual (Archivo Activo)
Para auditar rápidamente el archivo o conjunto de archivos en los que estás trabajando actualmente:
- **`Lider audita este archivo`**: Revisa el archivo que tienes abierto en el editor.
- **`Lider audita este componente`**: Revisa el conjunto de archivos (`.ts`, `.html`, `.css`) del componente activo.
- **`Lider audita este servicio`**: Revisa el archivo del servicio que tienes abierto.

