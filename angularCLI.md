Angular CLI 
===========


Como se ha visto en el vídeo anterior, **Angular CLI** es la interfaz de línea de comandos (*Command Line Interface*) oficial que Angular ha desarrollado **para crear y mantener proyectos** con este *framework*.

La utilizarás a lo largo de todo el curso, así que lo ideal será que te vayas familiarizando un poco con ella desde ahora, si bien, posteriormente verás su uso en la práctica.

A continuación vamos a presentar sus principales comandos y opciones de configuración.

**Todo comando en Angular CLI comienza con el prefijo `ng`**, que es lo que usa la terminal para identificar que se va a ejecutar un comando de Angular CLI.

Como toda herramienta de línea de comandos que se precie, Angular CLI implementa el comando `help` para obtener ayuda sobre su uso. ¡Arrancamos con éste!

ng help
-------

Si ejecutas `ng help` se te mostrará una lista de los comandos habilitados en Angular CLI entre los que destacan:

-   `add`
-   `build`
-   `deploy`
-   `generate`
-   `new`
-   `serve`
-   `update`

> **Truco**: Además, el comando `help` puede ejecutarse como argumento de otros comandos, mostrándote más información del comando en cuestión. Por ejemplo: `ng new --help`. Esto es muy útil para visualizar la lista de opciones que se pueden añadir a un comando.

Primeros pasos: ng new, ng add, ng update
-----------------------------------------

Toda aventura empieza con un primer paso y en Angular ocurre lo mismo. Para crear tu aplicación de Angular tienes **dos caminos**: crear la estructura a mano, o dejar que Angular CLI la cree por ti. Ambas formas tienen pros y contras:

-   **Arquitectura propia**: te permite decidir sobre absolutamente todo, pero tendrás que crear tú el propio flujo para compilar, servir el proyecto... una tarea para nada trivial y que requiere de mucha experiencia para ejecutarla correctamente.
-   **Arquitectura de Angular CLI**: te ofrece una arquitectura por defecto, funcional y ya aplicando buenas prácticas. Puede haber casos de uso donde esta configuración de proyecto se te quede corta, es cierto. Sin embargo, para la mayor parte de los casos te resultará adecuada. Además, ofrece todos los archivos apropiados para que, si tuvieses necesidad, pudieses modificar algo en alguno de los procesos.

**`ng new` es el comando que debes ejecutar** para generar la estructura que recomendamos en este curso a la hora de iniciar cualquier nueva aplicación. Al inicio del comando te preguntará por varias opciones de configuración, que en el caso de este curso hemos siempre dejado por defecto.

> **Nota**: en el proyecto de ejemplo Tweempus hemos aceptado añadir el módulo de enrutado (escribir `y` en la segunda opción), pero no te preocupes, que te avisaré llegado el momento. Por ahora, con las opciones por defecto nos arreglamos.

Una vez finalizado el proceso de creación la estructura generada es la siguiente (a partir de Angular 11):

![Estructura inicial de carpetas y archivos de Angular CLI](/campus/Campus_campusmvp/Courses/Angular/010-Introducción/Images/Arquitectura_Inicial_Angular_CLI.png)

A partir de esta estructura, hay varias secciones que merecen una explicación para entender bien cómo funcionan las entrañas de un proyecto en Angular. En este punto del curso, todavía muy inicial, vamos a diferenciar tan solo entre los archivos que se encuentran dentro y fuera de la carpeta `src`:

-   **Archivos dentro de la carpeta `src`**: son los archivos relacionados con la aplicación (HTML, CSS, TypeScript, JavaScript...) y con los que estarás en contacto la mayor parte del tiempo. En el siguiente módulo empezarás a trabajar con Angular y es entonces cuando veremos más en detalle esta carpeta.
-   **Archivos en la raíz del proyecto**: son los archivos de configuración del proyecto. Cuestiones como la gestión de dependencias o la compilación del proyecto se configuran a este nivel, dejando el código cliente dentro de la carpeta `src`.

> **Nota**: La otra carpeta `e2e` está pensada para incluir las pruebas de código, desde test unitarios simples hasta pruebas funcionales (o *End-To-End*, de ahí el nombre de la carpeta). Dado que el *testing* de aplicaciones se sale por completo del ámbito del curso, haremos caso omiso de esa carpeta.

Como en posteriores módulos estudiaremos en detalle la carpeta `src`, ahora nos centraremos en los archivos de la carpeta raíz del proyecto.

En la imagen puedes ver varios archivos, de los cuales los que debes conocer son los siguientes:

-   **`.browserslistrc`**: Angular hace uso de [browserlist](https://github.com/browserslist/browserslist) para indicar al proceso de compilación de CSS y JS/TS a qué navegadores quieres dar soporte. Es muy importante echar un vistazo a la lista contenida en este archivo ya que, por ejemplo, a partir de Angular 10 se dejó de dar soporte a IE 11, por lo que no lo verás en ella. Si quisieras añadir dicho soporte tendrías que añadir la línea `IE 11` al archivo.
-   **`.editorconfig`**: [EditorConfig](https://editorconfig.org/) es otra herramienta muy conocida para configurar entornos de trabajo y asegurarse que todos ellos generan un código parecido. Con este archivo puedes controlar cosas como el uso que se debe hacer de las comillas dobles o comillas simples, el tamaño máximo de una línea, etc.
-   **`package.json`**: archivo donde se gestionan principalmente las dependencias y tareas de npm (el gestor de paquetes de Node.js, muy utilizado en la actualidad para gestión de paquetes y tareas Front-End).
-   **`tsconfig.*.json`**: son los archivos de configuración de TypeScript (TS para abreviar). En concreto, el archivo *tsconfig.base.json* es el que indica a Angular a qué versión de JavaScript/ECMAScript compilar el proyecto. Por defecto Angular compila TS a **ES2015**, pero no sería raro querer mejorar el peso y concreción de la aplicación compilando a una versión más reciente de ES (reduciendo por tanto soporte a navegadores antiguos, ojo).
-   **`tslint.json`**: archivo en el que se configura el *linter* de TypeScript (herramienta que detecta fallos y malas prácticas en el código).
-   **`angular.json`**: es el archivo de configuración propio de Angular. Aquí el nodo más importante es `projects`, que contiene la configuración de tu proyecto. Por ejemplo, si utilizásemos una librería CSS como Bootstrap o similar, tendríamos que añadir los archivos `.js` correspondientes dentro de su propiedad `scripts`. De este modo, Angular los tendrá en cuenta a la hora de hacer el proceso de *build* de la aplicación, y los optimizara con el resto de ésta.

> **Nota**: para este proceso es recomendable usar el comando `ng add` seguido del nombre del paquete de npm de nuestra dependencia, de esta forma Angular CLI añade los archivos necesarios automáticamente (siempre y cuando le sea posible).

Las principales opciones del comando `ng new` y quizás las que se suelen utilizar con mayor frecuencia, son:

-   **`--directory`**: para los casos en los que quieras especificar el nombre del directorio donde vas a trabajar. Creará la estructura en el directorio que le indiques.
-   **`--legacy-browsers`**: para los casos en los que necesitas dar soporte a IE 11 desde el principio del proyecto.
-   **`--skipGit`**, **`--skipInstall`**, **`--skipTests`**: son autoexplicativos. Evitan, respectivamente, generar un repositorio de Git, instalar las dependencias del proyecto (de npm) al inicio o pasar los test al proyecto, respectivamente.
-   **`--strict`**: a partir de Angular 10, obliga a *tipar* correctamente el proyecto, es decir, te fuerza esencialmente a usar en el código TypeScript los tipos correctos en lugar de *any* (ya veremos qué es esto). La parte buena es que te obliga a generar un código bastante sólido y menos propenso a errores. La parte mala es que a veces puede ser engorroso, sobre todo cuando tienes que usar librerías de terceros que no estén correctamente *tipadas*. Personalmente te recomiendo su uso.

Con esto ya conoces a vista de pájaro los archivos de proyecto que genera Angular CLI.

Otra gran ventaja de generar tus proyectos con esta herramienta es que te puedes beneficiar de **actualizaciones automáticas entre las diferentes versiones de Angular** ejecutando el comando especializado `ng update`. Este comando es tremendamente útil (sobre todo si sigues los estándares), ya que hace migraciones automáticas del proyecto de una versión inferior a una versión superior, sin necesidad de tocar ni una línea de código.

Una duda muy razonable que se te puede estar pasando por la cabeza es: "vale, pero ¿qué pasa si `ng update` me introduce una actualización que no quiero?". No te preocupes, `ng update` tan solo va a seguir ciertas reglas con las que se sabe con seguridad que ese código se puede cambiar sin provocar "daños colaterales". Si durante la migración se encuentra con un caso fuera de lo normal, simplemente te avisará de que se requiere de acción manual, pero no tocará nada.

> **Nota**: recuerda también que, salvo en pruebas, vas a estar siempre trabajando sobre un control de versiones (Git seguramente), así que en caso necesario puedes descartar los cambios y volver a repasar la actualización si algo falla.

Servir tu aplicación: ng serve, ng build, ng deploy
---------------------------------------------------

Ya has visto cómo crear una aplicación y cómo mantenerla a lo largo del tiempo, pero ahora queremos empezar a trabajar con ella, desplegarla.

Para ello, el primer comando que debes ejecutar es **`ng serve`**. Como su propio nombre indica, este comando ejecutará tu aplicación desde un servidor local. Así desarrollarás con mayor facilidad y obtendrás refrescos automáticos del navegador con cada cambio que hagas para ver sus efectos.

Para lograrlo, hará una compilación inicial del proyecto para pasar tu código TypeScript a código ECMAScript (para la versión que tengas definida en tu configuración de TS), que ya entiende cualquier navegador común.

> **Nota**: fíjate en que no señalo ningún otro paso adicional, como el de minificación u ofuscación del código. Esto se debe a que ese proceso se realiza posteriormente, cuando haces un *build* completo de la aplicación, y no ahora, durante el desarrollo. En el proceso de *build* se llevan a cabo todos los pasos necesarios, se prepara la aplicación para producción.

Las opciones habituales que se utilizan con `ng serve` son:

-   **`--hmr`**: para el uso del reemplazo de módulos en caliente, lo que te permitirá ver los cambios que hagas en tus componentes sin necesidad de recargar el navegador.
-   **`--host`** y **`--port`**: sirven para definir dónde quieres desplegar la aplicación. Por defecto se sirve en `localhost:4200`, pero puedes requerir del uso de un puerto diferente si ya lo tienes ocupado, por ejemplo.
-   **`--verbose=true|false`**: por si quieres que te muestre más o menos *logs* a la hora de trabajar con ella.
-   **`--browserTarget`**: aunque no es frecuente su uso, puede ser interesante conocerla, por eso la he incluido. En caso de que necesites probar algo muy específico en un navegador antiguo, como IE 11, usarías esta opción con `ng serve` y el compilador añadirá automáticamente los *polyfill* necesarios para que el código funcione en ese navegador en concreto.

> **Nota**: Angular trae por defecto muchos *polyfill* listos para usar, por lo que sería raro que tuvieras que añadir tú alguno manualmente. Pero, por si acaso lo necesitas, tan solo instalando el *polyfill* con npm e importándolo en `src/polyfills.ts` será suficiente para que Angular CLI ya lo tenga en cuenta en su proceso de compilación.

Si quieres ir un paso más allá y **desplegar la aplicación fuera de tu máquina local**, tienes dos formas de hacerlo:

-   **Despliegue manual, externo a Angular CLI**: el proceso es muy sencillo. Consiste en ejecutar `ng build` para compilar la aplicación y luego subirla de la forma que tengamos disponible (FTP, despliegue continuo...) al servidor de producción. La verdad es que, esta suele ser la forma más común de despliegue, ya que al final cada empresa es un mundo y cada una tiene sus propios sistemas o formas de ejecutar este proceso.
-   **Despliegue con Angular CLI**: la CLI de Angular te ofrece un sistema automático y, *a priori*, fácil de configurar para desplegar tu aplicación. Digo *a priori* porque, como veremos, no es algo que dependa solo de Angular. Esta opción no se usa más a menudo porque está limitada a tan sólo un puñado de servicios comunes: [Amazon Cloud](https://www.npmjs.com/package/@jefiozie/ngx-aws-deploy), [Azure](https://www.npmjs.com/package/@azure/ng-deploy), [GitHub pages](https://www.npmjs.com/package/angular-cli-ghpages), [Firebase hosting](https://www.npmjs.com/package/@angular/fire), [Netlify](https://www.npmjs.com/package/@netlify-builder/deploy) y [Now](https://www.npmjs.com/package/@zeit/ng-deploy). Si no usas alguno de estos servicios entonces tendrás que usar la opción manual. Además, dependiendo del servicio que utilices, cada una de estas librerías (que a excepción de la Firebase ninguna es mantenida por Angular) usan diferentes formas de configuración.

Por último `ng build` tiene un par de opciones interesantes que merecen una breve explicación:

-   **`--configuration development`**: esta opción es muy útil para configurar el proceso de *build* para indicar el entorno para el que vas a aplicar la compilación (por defecto es producción, y con esta opción se haría para desarrollo, menos optimizado pero más fácil de depurar).
-   **`--output-hashing none|all|media|bundles`**: sirve para hacer que el compilador de angular añada un *hash* a cada archivo (de código o de recursos) generado en el proceso de compilación. Muy útil para evitar problemas de caché.
-   **`--allowedCommonJsDependencies`**: a partir de Angular 10, por defecto, `ng build` te mostrará un *warning* si utilizas alguna librería que se base en el sistema de módulos CommonJS, en lugar del sistema estándar de módulos de ECMAScript (ESM). El motivo es que este tipo de módulos producen aplicaciones de mayor tamaño y más lentas, y te advierte de ello con la idea de que migres las librerías afectadas a ESM. Esto no siempre es posible, así que para evitar estas advertencias, simplemente añades a esta opción el nombre de las librerías afectadas y te desaparecerán.

> **Acerca de los sistemas de módulos**: el mundo del desarrollo web a día de hoy está sufriendo una importantísima migración, los módulos. Hasta hace unos años no había una forma estándar de importar métodos o clases entre código JavaScript, por lo que se empleaban especificaciones como CommonJS (de Node.js) o AMD (cuyo representante más conocido es RequireJS), con amplia aceptación, pero no estandarizadas. En concreto CommonJS era muy popular, pero estaba incompleto y conllevaba otros problemas por lo que al final, con ES2015, se creó un estándar para módulos JavaScript: ES modules o ESM. Aunque la especificación es de 2015, no fue hasta hace relativamente poco que se adoptó por parte de todos los navegadores, y a partir de la versión 12.17.0 de Node está disponible en modo experimental. Por el momento, ambas tecnologías de módulos están conviviendo y, mientras que ESM se soporta en todos los navegadores modernos, en Node.js no, por eso muchos siguen utilizándola. De ahí la existencia de este *switch* de `ng build`.
