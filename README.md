# GEMINI.md
Archivo de convenciones Lidertech para usar dentro del editor firebase studio en proyectos Angular de Lidertech.

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
