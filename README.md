# Factory Method

Este patrón se basa en una interfaz para crear objetos de una superclase, siendo sus subclases las que modifican y especifican el tipo de objeto a crear.

Imaginemos que tenemos una aplicación cuyo código gira entorno a una única clase. Si necesitasemos incluir nuevas clases habría que modificar por completo el código y quedaría bastante sucio y lleno de condicionales que cambian el comportamiento de la aplicación en función de la clase de los objetos.

El patrón Factory Method sugiere que la creación de objetos no sea directa, sino invocándola de un método "fábrica" especial. Los objetos devueltos por el método fábrica a menudo se denominan productos.

Aunque parezca que sólo hemos cambiado de el lugar desde donde invocamos al constructor, este patrón nos permite sobreescribir el método fábrica en una subclase y cambiar la clase de los productos creados por el método. 

Pero tiene una limitación: las subclases sólo pueden devolver productos de distintos tipos si dichos productos tienen una clase base o interfaz común. Además, el método fábrica en la clase debe tener su tipo de retorno declarado como dicha interfaz.

# ¿Cuándo es útil aplicar este patrón?
- Cuando no conozcamos de antemano las dependencias y los tipos exactos de los objetos con los que deba funcionar tu código.
- Cuando una clase quiera que sean las subclases las que especifiquen los objetos a crear. 
- Para ofrecer a los usuarios de nuestra biblioteca o framework una forma de extender sus componentes internos.
- Para ahorrar recursos del sistema mediante la reutilización de objetos existentes en lugar de reconstruirlos otra vez.


# Estructura


![image](https://github.com/Txamet/patrones-de-dise-o/assets/171684793/30fecd0f-d01f-4f73-a510-523d2d72b924)


 1. El Producto declara la interfaz, que es comun a todos los objetos.
 2. Los Productos Concretos son implementaciones de la interfaz de producto.
 3. La clase Creadora declara el método fábrica, siendo importante que el tipo de retorno de este método coincida con la interfaz de producto.
 4. Los Creadores Concretos sobreescriben el Factory Method base, de manera que devuelva un tipo diferente de producto.
 

 # Implementación

1. Todos los productos han de seguir la misma interfaz, la cual ha de declarar métodos que tengan sentido en todos los productos.
2. Añadimos un patrón Factory Method vacío dentro de la clase creadora. El tipo de retorno del método deberá coincidir con la interfaz común de los productos.
3. Cogemos todas las referencias a constructores de producto en el código de la clase creadora y las sustituimos por invocaciones al Factory Method, mientras extraemos el código de creación de productos para colocarlo dentro del Factory Method.
4. Creamos un grupo de subclases creadoras para cada tipo de producto enumerado en el Factory Method. Sobreescribimos el Factory Method en las subclases y extraemos las partes adecuadas de código constructor del método base.
5. Podemos reutilizar el parámetro de control de la base en las subclases en caso de haber demasiados tipos de producto.
6. Si, tras todas las extraccionmes, el Factory Method base queda vacío, podemos hacerlo abstracto. Si queda algo dentro, podemos convertirlo en un comportamiento por defecto del método.


# Ejemplo con pseudocódigo

![image](https://github.com/Txamet/patrones-de-dise-o/assets/171684793/c7a4525c-b73f-46c4-b2bc-09c62bab087e)



          // La clase creadora declara el método fábrica que debe devolver
          // un objeto de una clase de producto. Normalmente, las
          // subclases de la creadora proporcionan la implementación de
          // este método.
   
          class Dialog is
   
              // La creadora también puede proporcionar cierta
              // implementación por defecto del método fábrica.
   
              abstract method createButton():Button
          
              // Observa que, a pesar de su nombre, la principal
              // responsabilidad de la creadora no es crear productos.
              // Normalmente contiene cierta lógica de negocio que depende
              // de los objetos de producto devueltos por el método
              // fábrica. Las subclases pueden cambiar indirectamente esa
              // lógica de negocio sobrescribiendo el método fábrica y
              // devolviendo desde él un tipo diferente de producto.
   
              method render() is
   
                  // Invoca el método fábrica para crear un objeto de
                  // producto.
   
                  Button okButton = createButton()
   
                  // Ahora utiliza el producto.
   
                  okButton.onClick(closeDialog)
                  okButton.render()
          
          
          // Los creadores concretos sobrescriben el método fábrica para
          // cambiar el tipo de producto resultante.
   
          class WindowsDialog extends Dialog is
              method createButton():Button is
                  return new WindowsButton()
          
          class WebDialog extends Dialog is
              method createButton():Button is
                  return new HTMLButton()
          
          
          // La interfaz de producto declara las operaciones que todos los
          // productos concretos deben implementar.
   
          interface Button is
              method render()
              method onClick(f)
          
          // Los productos concretos proporcionan varias implementaciones
          // de la interfaz de producto.
          
          class WindowsButton implements Button is
              method render(a, b) is
   
                  // Representa un botón en estilo Windows.
   
              method onClick(f) is
   
                  // Vincula un evento clic de OS nativo.
          
          class HTMLButton implements Button is
              method render(a, b) is
   
                  // Devuelve una representación HTML de un botón.
   
              method onClick(f) is
   
                  // Vincula un evento clic de navegador web.
          
          class Application is
              field dialog: Dialog
          
              // La aplicación elige un tipo de creador dependiendo de la
              // configuración actual o los ajustes del entorno.
   
              method initialize() is
                  config = readApplicationConfigFile()
          
                  if (config.OS == "Windows") then
                      dialog = new WindowsDialog()
                  else if (config.OS == "Web") then
                      dialog = new WebDialog()
                  else
                      throw new Exception("Error! Unknown operating system.")
          
              // El código cliente funciona con una instancia de un
              // creador concreto, aunque a través de su interfaz base.
              // Siempre y cuando el cliente siga funcionando con el
              // creador a través de la interfaz base, puedes pasarle
              // cualquier subclase del creador.
   
              method main() is
                  this.initialize()
                  dialog.render()
