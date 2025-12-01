# GEMINI.md
Archivo de convenciones Lidertech para usar dentro del editor firebase studio en proyectos Angular de Lidertech.

# NORMAS DE UI:
  * Todas nuestras apps usaran angular material para UI.
  * Sólo usamos css para estilos pero en el proyecto solo existirá un archivo custom-theme.scss instalado por Angular Material.

# ARQUITECTURA ÚNCA PARA TODA LA APP ArquiLiderL10:

  + Todo el proyecto contiene solo 10 directorios principales dentro del src/app.
  + Los directorios principales son:
    * auth: directorio donde residen toda la seguridad de la app, authts, autorizador.ts, enums de roles.
    * components: componentes reusables tontos y lógica simple.
    * config: directorio donde se guardan servicios de instancias de firebase y otros proveedores, conecciones a la infraestructura.
    * core: alberga directorios de infraestructura como firebase, aws, google, servicios acopladores.
    * css: manejamos una biblioteca propia estandar en src/app/css/lib. Los estilos personalizados del proyecto estarán en src/app/css/style
    * data: alberga archivos .json de datos estáticos, const de datos estaticos de la app.
    * global: directorio que almacena servicio de estados globales de la app (globalStates.ts) y enum de estados.
    * interfaces: todas las interfaces existente en el proyectos (modelos de datos).
    * tools: servicios, enums y const de utilidad que se requieren en cualquier componente.
    * views: guardará componentes con UI, es el unico liugar admitido para eso.
   + Recomendamos reviasar el glosario Lidertech.

   + El directorio assets siempre reside en src/.
   + El directio environments se creará con el comando:

         ng generate environments

  + El menú de navegación siempre será creado con angular material: para garantizar su ubicación en el directio views.

         ng generate @angular/material:navigation views/navigation

  + El directorio "views" siempre se dividira en internamente en 3 directorios:
      * admin: (para vistas de administradores).
      * pages: para vistas de usuarios externo no administradores o clientes de la empresa).
      * navigation: componente de navegacion de Angular Material.
  + 


+++++++++++++++++++++++++++++++++++
👑 Convención Definitiva de Formato Lidertech de codigo .ts (6 Reglas)
 
#	Regla	Descripción
1	Alineación Vertical de Asignación	En todas las declaraciones de propiedades, servicios inyectados y constantes consecutivas, el código debe usar indentación de columna para alinear verticalmente el signo de asignación (=).
2	Encapsulación Rigurosa de Datos	Todos los datos fijos, mensajes, rutas o parámetros utilizados dentro de un método deben ser extraídos y encapsulados como propiedades de clase (public readonly o private readonly).
3	Formato try/catch Compacto	Los bloques try y catch deben abrirse en la misma línea que la declaración (try { y catch (error) {).
4	Comentario de Cierre con Separación	Al finalizar la llave de cierre de la clase (}), se incluyen cinco líneas vacías y luego el comentario de cierre distintivo (ej. //********** FIN DEL COMPONENTE **********//).
5	Separación Visual por Acción	Dentro de los métodos, cada sentencia de acción principal (ej. await, this.estado.set(), this.servicio.metodo()) debe comenzar en su propia línea para visualizar el flujo de ejecución.
6	Estilo Estético con Emojis	Utilizar emojis contextualmente relevantes en los comentarios de bloque para mejorar la escaneabilidad visual: // 📝 ESTADO LOCAL, // ⚙️ INYECCIONES, // 🚀 MÉTODOS, etc.

###Ejemplo de Bloque Aplicado:

     
     // 📝 ESTADO LOCAL (Regla 6)
    // 1. Alineación Vertical de Asignación
    public readonly estado      = signal<StateEnum>(StateEnum.INICIAL);
    public readonly EstadoEnum  = StateEnum; 
  
    // ⚙️ INYECCIONES DE SERVICIOS (Regla 6)
    // 1. Alineación Vertical de Asignación
    private readonly authService     = inject(AuthService);
    private readonly snackBarService = inject(SnackBarService);
    private readonly router          = inject(Router);
  
    // 📝 CONSTANTES DE LÓGICA (Regla 6 y 2)
    // 1. Alineación Vertical de Asignación
    private readonly MENSAJE_EXITO   = '¡Inicio de sesión exitoso!';
    private readonly MENSAJE_ERROR   = 'Error al iniciar sesión.';
    private readonly DURACION_EXITO  = 3000;
    private readonly DURACION_ERROR  = 5000;
    private readonly RUTA_DESTINO    = ['/procesos'];
  
    // 🎨 CONSTANTES DE UI (Regla 6 y 2)
    // 1. Alineación Vertical de Asignación
    public readonly LOGO_URL         = 'https://lidertech.com/assets/logo.png';
    public readonly ALT_LOGO         = 'Lidertech Logo';
    public readonly TITULO_PAGINA    = 'Iniciar sesión';
    public readonly TEXTO_BOTON      = 'Iniciar sesión con Google';
    public readonly SVG_ICONO_GOOGLE = 'google';
    public readonly SPINNER_DIAMETRO = 24;
  
  
    // 🚀 MÉTODO DE ACCIÓN (Regla 6)
    async iniciarSesion(): Promise<void> {
      this.estado.set(StateEnum.CARGANDO);
    
      // 3. Formato try/catch Compacto
      try { 
        // 5. Separación Visual por Acción
        await this.authService.iniciarSesionConGoogle(); 
        
        this.estado.set(StateEnum.EXITO);
        this.snackBarService.mostrarMensaje(this.MENSAJE_EXITO, 'exito', { duracion: this.DURACION_EXITO });
        this.router.navigate(this.RUTA_DESTINO); 
        
      } catch (error) { 
        // 5. Separación Visual por Acción
        this.estado.set(StateEnum.ERROR);
        this.snackBarService.mostrarMensaje(this.MENSAJE_ERROR, 'error', { duracion: this.DURACION_ERROR });
      }
      }
    }
    
    // 4. Comentario de Cierre con Separación Visual (5 líneas)
    
    
    
    
    
    
    //********** FIN DEL COMPONENTE **********//






+++++++++++++++++++++++++++++++++++
📐 Convención Definitiva de Formato HTML Lidertech


Regla	Descripción	Ejemplo de Aplicación
1. Indentación Estándar	Utilizar una indentación estándar (dos o cuatro espacios, se recomienda dos) en toda la jerarquía de etiquetas.	<main> usa 0 espacios, <mat-card> usa 2, <header> usa 4, etc.
2. Atributos en una Sola Línea	Mantener la declaración de la etiqueta y todos sus atributos en la misma línea. Evitar la división de atributos en múltiples líneas (a menos que sean excesivamente largos).	<button mat-raised-button color="primary" type="submit" [disabled]="estado() === EstadoEnum.CARGANDO">
3. Líneas Vacías para Separación de Bloques	Utilizar una línea vacía para separar bloques lógicos importantes (e.g., entre </header> y <mat-card-content>, o entre un bloque @if y la siguiente etiqueta).	Se usan dos saltos de línea (\n\n) después del cierre de un bloque principal (</header>).
4. Separación de Directivas de Control	Los bloques de control de flujo (@if, @for, @switch) se separan del contenido circundante con una línea vacía.	Línea vacía antes y después del bloque @if (...) { ... }.
5. Cierre de Etiquetas Contenedoras	Las etiquetas contenedoras principales (<mat-card>, <form>) deben tener su etiqueta de cierre (</mat-card>) separada por una línea vacía del contenido que la precede.





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
# ESTADOS UNICOS EN ENUM DE ESTADOS PARA TODA LA APP:

  El uso del diccionario de estado StateEnum está estrictamente delimitado por la función del servicio:

  * Servicios Operacionales con Estado Interno: Siempre deben usar el StateEnum para gestionar y exponer su ciclo de vida (ej., CARGANDO, PROCESANDO, EXITO). 

  * Servicios de Utilidad (Stateless): Nunca deben usar el StateEnum. Estos servicios son herramientas funcionales cuya ejecución es inmediata y directa.
    El estado de la operación (si una funcionalidad falla o no) es responsabilidad del componente o del Servicio Operacional que invoca a la utilidad.
    Los servicios de utilidad siempre serán guardados dentro del directorio src/app/tools.





+++++++++++++++++++++++++++++++++++
# SERVICIO DE UTILIDAD GENERICA:

  Los servicios de Utilidad se guardarán siempre en src/app/tools.

- **Excepción de Ubicación para Servicios de Dominio:** Los servicios de utilidad que pertenecen a un dominio de negocio específico (como `auth` o `pagos`) deben permanecer en su directorio de dominio correspondiente (ej: `src/app/auth/autorizador.service.ts`). Esto prevalece sobre la regla general de mover todos los servicios de utilidad a `src/app/tools/`, ya que mantiene la cohesión lógica del dominio.





+++++++++++++++++++++++++++++++++++
# SERVICIOS OPERACIONALES DE INFRAESTRUCTURA LIDERTECH

  Los servicios operacionales serán guardaos en directorios según su uso de origen ejemplo:
  
  * Los servicios que trabajan con firebase se crearan en el directorio src/app/core/firebase. Ejemplo si usamos aws quedaria asi: src/app/core/aws
  * Los Servicios de APIS de Google se guardarán en el directorio src/app/core/google.
  * Los Servicios operacionales de APIS de Redes sociales se guardarán en src/app/core/rrss.






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


### 4. `perfil-interface.ts` (El Guardián de Rutas)

---


+++++++++++++++++++++++++++++++++++
# CONVENCION UNIVERSAL: El Patrón de Signal de Estado Dual

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
















# AUDITORIAS DE CÓDIGO AUTOMATIZADAS

Para interactuar con el sistema de auditorías, utiliza las siguientes frases clave, diseñadas para ser claras y flexibles:

### 1. Auditoría General
Para ejecutar una revisión completa de todo el proyecto en busca de desviaciones de las convenciones de este documento:
- ***`Lider audita el proyecto`***

### 2. Auditoría por Archivo Específico
Para auditar un archivo concreto, sin importar si es un componente, servicio u otro tipo de archivo:
- **`lider audita mis archivos abiertos`**
- La auditoria tomará en cuenta los archivos que se encuentren abiertos en ese momento.

### 3. Auditoría Contextual (Archivo Activo)
Para auditar rápidamente el archivo o conjunto de archivos en los que estás trabajando actualmente:
- **`lider audita este archivo`**: Revisa el archivo que tienes abierto en el editor.
- **`lider audita este componente`**: Revisa el conjunto de archivos (`.ts`, `.html`, `.css`) del componente activo.
- **`lider audita este servicio`**: Revisa el archivo del servicio que tienes abierto.

