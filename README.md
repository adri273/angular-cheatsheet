# angular-cheatsheet

## Create app
`npm install -g @angular/cli`
`ng new my-app`

Si falla al hacer el install, porque no puede resolver alguna dependencia como karma-jasmine-html-reporter, lo solucionamos de esta forma, dentro del proyecto
`npm i --legacy-peer-deps --force`

### Creamos el repositorio en github
Cuando tengamos la url del repo, abrimos en VSCode el directorio que hemos creado para el proyecto
- Abrimos la terminal y dentro del directorio hacemos `git init`
- Para vincularlo con el repo de github lo hacemos con `git remote add <remote name> <repository url>`, quedará algo así `remote add origin https://....`
- subimos los cambios al repo y comprovamos que esté todo bien `git push -u origin -all`

## Serve app
`ng serve`

## Create component
`ng generate component shared/components/header` o `ng g c shared/components/header`

## Two way data-binding
### app.component.html
`{{title}} <input type="text" [(ngModel)]="title"`
### app.module.ts
`imports: [ ..., FormsModule]`

## Añadir angular material
`ng add @angular/material`

## Instalar Json Server
`npm i -g json-server`
### Mock data
- Guardamos el fichero .json con los datos dentro de /server/db.json
- Vamos al package.json, y en scripts añadimos:
  `"serverAPI":"json-server --watch server/db.json --port 4001"`
- Vamos a la terminal y levantamos el servidor
  `npm run serverAPI`

## Routing
https://github.com/domini-code/curso-angular-12
Existe la directiva `<router-outlet></router-outlet>` que carga dinámicamente un componente u otro en base a una ruta. Esto lo ponemos en app.component.html, por ejemplo.
Los componentes dentro de las rutas, suelen ir en /pages/login (por ej.) . Generamos el `ng g c pages/login` .
Y vamos a app-routing.module.ts, y la constante routes tiene que quedar algo así
```
const routes : Routes = [
  {path: 'login', component: LoginComponent}, /*Componente dinámico*/
  {path: '**', redirectTo: '', pathMatch: 'full'},  /*cuando no existe la ruta, hacemos un redirect a la home*/
  /*otra opción es poner página 404*/
  /*{path: '**', component: Err404Component}*/
];
```
Para crear directamente una nueva página con el CLI, se hace con `ng g m pages/products -m=app --route products`
el -m importa el módulo directamente en app. Y el --route añade el path de la ruta para este componente

Dentro de este módulo necesitamos poner nuevo componente de Product, lo creamos con el CLI `ng g c pages/products/product --skip-tests`
El --skip-tests no genera fichero de tests para este componente

También dentro de la carpeta de este módulo pages/products creamos el directorio interfaces, y creamos el fichero product.interface.ts
```
export interface Product {
  id: number;
  name: string;
  price: number;
}
```

Ahora creamos un service (para manejar los datos y llamadas de la api) a través del CLI `ng g s pages/products/services/products --skip-tests`
Para poder hacer peticiones http, vamos al app.module.ts (modulo principal) e importamos el HttpClientModule `import {HttpClientModule} from '@angular/common/http';` y lo injectamos en el apartado de imports de este fichero

el servicio de products /pages/products/services/products/products.service.ts debe quedar algo así, cuando esté acabado
```
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { Product } from '../interfaces/product.interface';  /*Importamos la interface de producto*/

@Injectable({
  providedIn: 'root'
})
export class ProductsService {
  private apiURl = 'http://localhost:4001/products';  /*URL API*/
  constructor(private http: HttpClient) { } 

  getProducts(): Observable<Product[]> {  /*Utilizamos la interface de producto, y este método retornará un array de Product*/
    return this.http.get<Product[]>(this.apiURl); /*Obtenemos por get los productos, utilizando el this.http.get*/
  }

  updateStock(productId: number, stock: number): Observable<any> {
    const body = { "stock": stock };
    return this.http.patch<any>(`${this.apiURl}/${productId}`, body);
  }
}
```

Para consumir estas funciones y hacer uso del servicio, vamos al products.component.ts, importamos el servicio `import {ProductsService} from './services/products.service'`
en el constructor le pasamos el parámetro de ProductsService, y luego en el ngOnInit hacemos la llamada a ese método
```
constructor(private productSvc: ProductsService){}

ngOnInit(): void{
  this.productSvc.getProducts()
  .pipe(  /*llamada al observable*/
    tap(res => console.log(res) /*operador tap, se tendrá que importar con "import {tap} from 'rxjs/operators';" */
  ).subscribe()
}
```
Como estamos utilizando el ejemplo de products, vamos a mostrar estos datos en la vista, para ello, creamos la variable products en este mismo fichero, así quedará el código
```
products!: Product[];  /*ponemos la exclamación para que no pete, porque no está inicializado*/
constructor(private productSvc: ProductsService){}

ngOnInit(): void{
  this.productSvc.getProducts()
  .pipe(  /*llamada al observable*/
    tap((products: Product[]) => this.products = products
  ).subscribe()
}
```
Y en la vista, con un simple `{{products | json}}` ya se muestra el contenido de products (el "| json" lo muestra como json)
Ahora vamos a mostrarlo bien utilizando material design
```
<section class="products">
  <mat-card class="card" *ngFor="let product of products">
    <mat-card-header>
      <mat-card-title>
        {{product.name}}
      </mat-card-title>
     </mat-card-header>
    <mat-card-content>{{product.description}}</mat-card-content>
    <mat-card-actions>
      <button mat-flat-button color="primary">
        Buy {{product.price | currency}}
        <mat-icon>add_shopping_cart</mat-icon>
      </button>
    </mat-card-actions>
  </mat-card>
</section>
```

Si da errores de compilación es porque tenemos que importar/exportar las librerías necesarias de material. Para ello vamos al material.module.ts
```
import {MatButtonModule} from '@angular/material/button';
import {MatIconModule} from '@angular/material/icon';

@NgModule({
  exports: [......., MatButtonModule, MatIconModule]
})
```

### Routing hacia páginas dentro de componente (crear enlaces)
Para crear un enlace interno, se puede hacer de 2 maneras:
```
<ul>
  <li [routerLink]="['/general'] [routerLinkActive]="['active']">General</li>  <!-- El routerLinkActive, cuando detecta que es la pagina activa, añade la clase active al elemento -->
  <li (click)="goToLogin()">Login</a>
</ul>
```
Con el onclick, tendremos que crear una función en el componente y quedará algo así (importarlo y pasarlo por el construct)
```
...
import { Router } from '@angular/router';
...
export class ... {
  constructir(private router: Router){}
  goToLogin(){
    this.router.navigate(['dashboard']);
  }
}
```

## Separar componentes y pasarle valores
En el products.component.html estabamos mostrando con el `*ngFor` los product. Pero esta estructura la vamos a poner en la vista del componente product.component.html y en listado solo renderizamos el componente app-product pasandole la variable del loop

products.component.html
```
<section class="products">
  <app-product [product]="product" *ngFor="let product of products" ></app-product>
</section>
```

product.component.html
```
<mat-card class="card">
  <mat-card-header>
    <mat-card-title>
      {{product.name}}
    </mat-card-title>
   </mat-card-header>
  <mat-card-content>{{product.description}}</mat-card-content>
  <mat-card-actions>
    <button mat-flat-button color="primary">
      Buy {{product.price | currency}}
      <mat-icon>add_shopping_cart</mat-icon>
    </button>
  </mat-card-actions>
</mat-card>
```

y para que la vista del componente hijo Product, reciba el valor product por parámetro a traves del loop, abrimos el product.component.ts y añadimos lo siguiente:
```
......
export class ProductComponent Implements OnInit {
  @Input() product!: Product;   /*añadimos esto para pasarle la variable product. El @Input es para pasarle ese valor a la vista*/
  constructor(){}
  .....
}
```
Este componente hijo "Product" es un componente "tonto", porque solo lo utilizaremos para la vista.

## Eventos en los componentes "tontos"
Para pasarle valores al padre al hacer un evento, primero en la vista debemos poner lo siguiente, por ejemplo al hacer clic boton queremos pasale el producto al padre:
```
<button (click)="onClick">Comprar</button>
```
y en el product.component.ts añadimos el handleEvent y se lo pasamos al padre con el @Output
```
export class ProductComp.... {
  @Input() product!: Product;
  @Output() addToCartClick = new EventEmitter<Product>();
  .....
  onClick(): void {
    this.addToCartClick.emit(this.product);
  }
}
```
Ahora, para escuchar el evento en el componente padre Products hacemos lo siguiente:
En la vista products.component.html añadimos el evento onclick al componente
```
<section class="products">
  <app-product (addToCartClick)="addToCart($event)" /*Cuando se ejecute el evento addToCartClick, llamamos al método addToCart del products pasandole el valor del evento, en este caso le hemos dicho que sea un Product*/
    [product]="product" 
     *ngFor="let product of products" >
   </app-product>
</section>
```
Ahora, en products.component.ts añadimos el nuevo método
```
addToCartClick(product:Product):void {
  console.log('Producto: ',product);
}
```

## Formularios
Importamos los modulos necesarios

La vista del componente queda algo así
```
<form #projectForm="ngForm" (ngSubmit)="onSubmit(projectForm)">
  <p>
    <label for="name">Nombre</label>
    <input type="text" name="name" #name="ngModel" [(ngModel)]="project.name" (keyup.enter)="comprobarAlgo()" />
    <span *ngIf="name.touched && !name.valid">
      Campo obligatorio
    </span>
  </p>
  <p>
    <label for="description">Descripción</label>
    <textarea name="description" #description="ngModel" [(ngModel)]="project.description" ></textarea>
  </p>
  <input type="submit" value="Enviar" [disabled]="!projectForm.form.valid" /> 
</form>
```
Detallando un poco todo: 
- `#projectForm="ngForm"` con el hashtag le definimos el nombre del formulario, y le decimos que es un formulario de angular
- `(ngSubmit)="onSubmit(projectForm)"` cuando se lance el evento submit del form de angular, ejecutamos un método nuestro y le pasamos los datos del formulario por param
- `#name="ngModel` le estamos diciendo que el nombre del input del formulario de angular es "name" y que pertenece a un ngModel
- `[(ngModel)]="project.name"` con el two-way data-binding le estamos diciendo que cuando se modifique este dato, cambie el valor del objeto project.name
- `*ngIf="name.touched && !name.valid"` si el input name ha sido tocado, y no tiene un valor válido, mostramos el span con el mensaje de error
- `[disabled]="!projectForm.form.valid` si hay algún campo no válido en el formulario, el atributo disabled estará a true
- `(keyup.enter)="comprobarAlgo()"` ejecuta la función comprobarAlgo() cuando se presiona la tecla "enter"

## Comunicación entre componentes
### De padre a hijo
En el component del padre tenemos la variable widthSlider para pasarle al hijo
```
export class ... {
  public widthSlider: number;
  ...
  (y le damos el valor a esa variable como nos convenga)
}
```
Este es el html del padre, pasandole el parámetro "ancho" con el valor de la variable "widthSlider"
```
<app-slider [ancho]="widthSlider"></app-slider>
```
Y este es el componente hijo, que recibe el parametro "ancho"
```
export class ... {
  @Input() ancho: number;
  //Si queremos cambiar el nombre de la variable que viene
  @Input('ancho') anchoSlider: number;
}
```
Para utilizarlo en el component, se recoge con `this.ancho` y en el html se muestra con `{{ancho}}`

## De hijo a padre
Por ejemplo tenemos un formulario, y el submit pues ejecuta el método "enviar($event)", queremos enviar los datos (mocked), el código del component hijo queda algo así
```
import {EventEmitter} from '@angular/core';
...
export class... {
  @Output() conseguirDatos = new EventEmitter();
  
  public datos: any;
  constructor(){
    this.datos = {
      nombre: "Adri",
      username: "adriav"
    };
  }
  ...
  enviar(event){
    console.log(event);
    this.conseguirDatos.emit(this.datos); //enviamos los datos al padre
  }
}

```
Y en el html del padre, añadimos el nuevo método `(conseguirDatos)="getCustomDatos($event)"`
```
<app-slider [ancho]="widthSlider" (conseguirDatos)="getCustomDatos($event)"></app-slider>
```
En el component padre, cremos el método getCustomDatos para mostrar la info del hijo
```
...
public datos: any;
...
getCustomDatos(event){
  console.log(event);
  this.datos = event;
}
```

## ViewChild
Esto es el reemplazo a querySelector de js para obtener los elementos del dom. En el html ponemos en cualquier elemento `#sidebar` por ejemplo, con esto le ponemos una especia de ".sidebar" que entiende angular.
Y para acceder al elemento, en el codigo del component ponemos algo así
```
...
export class... {
  @ViewChild('sidebar') sidebarDom;   //seleccionamos el elemento sidebar y lo ponemos en la variable sidebarDom (por ejemplo)
  ...
  ngOnInit(){
    //en el onInit por ejemplo, ponemos el console log para ver el elemento
    console.log(this.sidebar);
    console.log(this.sidebar.nativeElement.textContent); //obtenemos el texto de ese div o lo que fuera (como un .value o .text())
  }
}
```

## Directivas personalizadas
Vamos a aplicarle un estilo personalizado a un componente mediante una directiva.
Creamos la directiva con `ng g d directives/resaltado` 
Abrimos el resaltado.directive.js y añadimos ElementRef para obtener el elemento del dom que contiene esa directiva, y le aplicamos un border red
```
import {Directive, ElementRef} from '@angular/core';
@Directive({
  selector: '[appResaltado]'
})
export class ResaltadoDirective {
  constructor(el: ElementRef){
    console.log(el.nativeElement);
    el.nativeElement.style.border = "1px solid red";
  }
}
```
Para aplicar esta directiva a un componente, lo hacemos añadiendo
```
<span resaltado>¡Resaltado!</span>
```

# Recursos
Angular 12 Dominicode - https://www.youtube.com/watch?v=i-oYrcNtc2s
Mock data JSON - https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbXJNaUVVa0hLZ2hFc3kwOHM5Y3RPWjlpOHB2d3xBQ3Jtc0ttX2JnOGtBRjNvUzJ4Y1ZaY2xCampuZHRraTY0U1F3aW03cDVCZkJxcjFLSC1fMVNCWkhkN19BbGFURnBnYkZ2MjBpTy16ZkRob1ZOZ0dCZlZSLWp5am1WWlNDall1QTZQNFVJeDlaWUp4d2NpTnZ3MA&q=https%3A%2F%2Fgist.github.com%2Fbezael%2F4c2fefbad406f9004ae1afeef53560d8
Angular Udemy Victor Robles - https://www.udemy.com/course/master-en-javascript-aprender-js-jquery-angular-nodejs-y-mas
