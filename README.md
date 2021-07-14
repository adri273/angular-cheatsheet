# angular-cheatsheet

## Create app
`npm install -g @angular/cli`
`ng new my-app`

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







# Recursos
Angular 12 Dominicode - https://www.youtube.com/watch?v=i-oYrcNtc2s
Mock data JSON - https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbXJNaUVVa0hLZ2hFc3kwOHM5Y3RPWjlpOHB2d3xBQ3Jtc0ttX2JnOGtBRjNvUzJ4Y1ZaY2xCampuZHRraTY0U1F3aW03cDVCZkJxcjFLSC1fMVNCWkhkN19BbGFURnBnYkZ2MjBpTy16ZkRob1ZOZ0dCZlZSLWp5am1WWlNDall1QTZQNFVJeDlaWUp4d2NpTnZ3MA&q=https%3A%2F%2Fgist.github.com%2Fbezael%2F4c2fefbad406f9004ae1afeef53560d8

