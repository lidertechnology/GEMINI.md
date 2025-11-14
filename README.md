# GEMINI.md
Archivo de convenciones Lidertech para usar dentro del editor firebase studio en proyectos Angular de Lidertech.

# ESTADOS EN COMPONENTES:

  Crearemos siempre la variable signal que contiene el estado local del componente, 
  utilizada para acceder al estado en las plantillas (templates), se llamará exclusivamente stateEnum.

  * Ejemplo de Declaración en Componente:

        // En el TypeScript del componente:
        public readonly stateEnum = signal<StateEnum>(StateEnum.INICIAL);
    
        // En la plantilla (template):
        // @if (stateEnum() === StateEnum.CARGANDO) { ... }
