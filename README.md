# GEMINI.md
Archivo de convenciones Lidertech para usar dentro del editor firebase studio en proyectos Angular de Lidertech.

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
      VACIO = 'vacio',          
      SIN_RESULTADOS = 'sin_resultados', 
      
      // --- Interacción / Autorización ---
      DESHABILITADO = 'deshabilitado', AUTENTICANDO = 'autenticando',AUTORIZADO = 'autorizado',  NO_AUTORIZADO = 'no_autorizado',
  
      // --- Conectividad / Hardware ---
      EN_LINEA = 'en_linea',    FUERA_LINEA = 'fuera_linea', CONECTADO = 'conectado',  DESCONECTADO = 'desconectado',MONITOREANDO = 'monitoreando',FALLO_SENSOR = 'fallo_sensor',
      CALIBRANDO = 'calibrando',// Para setup de hardware o sistemas (LiderAuto, LiderAutomate).
  
      // --- Flujo / Multimedia ---
      REPRODUCIENDO = 'reproduciendo', PAUSADO = 'pausado', LISTO = 'listo',   TERMINADO = 'terminado',  
  }




# ESTADOS GLOBALES DE LA APLICACIÓN:

  El servicio de estado global se llamará exclusivamente GlobalState. Su alcance es estrictamente limitado a gestionar la Identidad, la Sesión y la Disponibilidad de la aplicación (ej., rolDeUsuario, sesionActiva). 
  Se prohíbe que este servicio almacene o gestione estados operacionales genéricos (como CARGANDO, PAGINANDO, PROCESANDO), los cuales deben ser manejados por los servicios o componentes locales correspondientes. 
  Para garantizar la inmutabilidad y el control de la escritura (QI 1000%), el estado interno se almacena en WritableSignal privados y se expone públicamente solo a través de propiedades de Signal<T> de solo lectura, asegurando que los cambios solo puedan realizarse a través de los métodos definidos dentro del GlobalState. Los datos deben estar siempre fuertemente tipados, utilizando el enum RolUsuario para la información de permisos.

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

# ESTADOS EN COMPONENTES:

  Crearemos siempre la variable signal que contiene el estado local del componente, 
  utilizada para acceder al estado en las plantillas (templates), se llamará exclusivamente stateEnum.

  * Ejemplo de Declaración en Componente:

        // En el TypeScript del componente:
        public readonly stateEnum = signal<StateEnum>(StateEnum.INICIAL);
    
        // En la plantilla (template):
        // @if (stateEnum() === StateEnum.CARGANDO) { ... }



# SERVICOOS DE APLICACIONES LIDERTECH Y PITOS Y USOS:

  En la arquitectura Lidertech, los servicios se dividen en dos categorías basadas en su gestión de estado, para asegurar el Principio de Responsabilidad Única:

* Servicios Operacionales con Estado Interno: Responsables de ejecutar flujos de trabajo asíncronos (lectura, escritura, transacciones). Deben gestionar su propio ciclo de vida utilizando un signal tipificado con StateEnum (ej. ReadService, WriteService).

* Servicios de Utilidad (Stateless): Responsables de funcionalidades puntuales, enfocadas y sin estado persistente. No gestionan el StateEnum ya que su ejecución es directa (ej. AutorizadorService, CodificadorService).




# Uso de StateEnum por Tipo de Servicio:

  El uso del diccionario de estado StateEnum está estrictamente delimitado por la función del servicio:

  * Servicios Operacionales con Estado Interno: Siempre deben usar el StateEnum para gestionar y exponer su ciclo de vida (ej., CARGANDO, PROCESANDO, EXITO).

  * Servicios de Utilidad (Stateless): Nunca deben usar el StateEnum. Estos servicios son herramientas funcionales cuya ejecución es inmediata y directa.
    El estado de la operación (si una funcionalidad falla o no) es responsabilidad del componente o del Servicio Operacional que invoca a la utilidad.
    Los servicios de utilidad siempre serán guardados dentro del directorio src/app/tools.

# Servicios de Utilidad de Lidertech:

  Los servicios de Utilidad se guardarán siempre en src/app/tools.

# Servicios Operacionales de Lidertech:

  Los servicios operacionales serán guardaos en directorios según su uso de origen ejemplo:
  
  * Los servicios que trabajan con firebase se crearan en el directorio src/app/firebase.
  * Los Servicios de APIS de Google se guardarán en el directorio src/app/google.
  * Los Servicios operacionales de APIS de Redes sociales se guardarán en src/app/rrss.

  
