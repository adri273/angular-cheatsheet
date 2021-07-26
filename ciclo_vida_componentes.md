Ciclo de vida {#componentes-avanzados-ciclo-de-vida}
=====================================

Acabamos de ver los principales conceptos que debemos comprender para
crear un componente: el decorador `@Component`, atributos, clases… Pero
todavía no sabemos cómo se comporta este componente a lo largo de su
ciclo de vida.

¿Qué quiere decir esto último? Pues bien, **desde que se carga un
componente hasta que se destruye existen varios estados intermedios**
por los que pasa éste y en los cuales realiza diferentes acciones a
través de eventos asociados a dichos estados.

> **Nota**: por poner un símil, lo mismo pasa con el DOM de una página
> Web en el navegador, y podemos realizar acciones antes y después de
> que se cargue.

Por tanto, al trabajar con los componentes de Angular también podremos
utilizar los siguientes eventos para ajustar su comportamiento y
realizar acciones dentro de un momento concreto:

  Nombre del Evento         Definición del evento
  ------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  `ngOnChanges`             Este evento se ejecuta cada vez que ocurre un cambio después de que se hayan verificado los atributos del componente. Se ejecuta siempre antes de ngOnInit y antes de los eventos de check. Este evento produce un objeto con los valores previos y actuales.
  `ngOnInit`                Sólo se ejecuta una vez al instanciar el componente.
  `ngDoCheck`               Su comportamiento es muy parecido al de ngOnChanges y de hecho sólo se debe de usar uno de ellos para validar o modificar una entrada de datos determinada. Su principal diferencia es que este evento no compara los valores como el otro.
  `ngAfterContentInit`      Se ejecuta una vez se ha terminado de inicializar el componente.
  `ngAfterContentChecked`   Se ejecuta tras cada chequeo dentro del componente.
  `ngAfterViewInit`         Se ejecuta una vez se ha terminado de inicializar la vista del componente.
  `ngAfterViewChecked`      Se ejecuta tras cada chequeo dentro de la vista del componente.
  `ngOnDestroy`             Se ejecuta justo antes de que Angular destruya el componente.

Esta lista de eventos nos será muy útil sobre todo para realizar ciertas
comprobaciones y acciones en momentos concretos de nuestra aplicación.
Los eventos más importantes son, `ngOnChanges`, `ngOnInit` y
`ngOnDestroy`. Por ahora quédate con estos tres.

Cómo configurar eventos {#como-configurar-eventos}
-----------------------

Lo primero que deberemos hacer es importar la interfaz que maneja ese
evento dentro del componente:

``` {.line-numbers .language-ts}
import { Component, OnInit } from '@angular/core';
```

Copiar

Una vez importado deberemos implementar el evento dentro del componente.
Para ello debemos definir la clase del componente de la siguiente forma:

``` {.line-numbers .language-ts}
export class Component implements OnInit {
  ngOnInit() { ... }
}
```

Copiar

Muy importante definir el `implements` al declarar la clase, de esa
forma especificas que la clase **Components** implementa la interfaz del
OnInit. Una vez hecho todo lo que declares dentro de los puntos
suspensivos se ejecutará al instanciar el componente.
