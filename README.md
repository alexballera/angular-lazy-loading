# Lazy Loading
## ¿Qué es Lazy Loading?
Lazy Load es un patrón de diseño que consiste en retrasar la carga o inicialización de un objeto hasta el momento de su utilización.

Esto significa que obtiene los datos o procesa los objetos solamente cuando se necesitan, no antes. Esto se debe a que si se obtiene todos los datos antes de usarlos puede tener como resultado una mala experiencia de usuario, esto es muy importante del lado del frontend, porque sabemos que toda la carga es del lado del cliente, modularizar y diferir cargas ayuda a la aplicación a enfocarse en un código en especifico.

Según lo que encontramos en **Wikipedia**

La carga diferida (en inglés lazy loading), es un patrón de diseño comúnmente usado en la programación informática que consiste en retrasar la carga o inicialización de un objeto hasta el mismo momento de su utilización. Esto contribuye a la eficiencia de los programas, evitando la precarga de objetos que podrían no llegar a utilizarse. El opuesto de la carga diferida es la carga previa, precarga o eager loading.

Esta técnica usada en Angular nos permite cargar sólo, el o los componentes que necesitemos al inicio de nuestra aplicación, estos componentes no cargan cada vez que entres, sino que solo cargan una sola vez.

Cuando usamos Lazy Loading hacemos llamado de un módulo mediante el sistema de rutas de Angular y este módulo a su vez tiene rutas hijas que se encargan de cargar el componente solicitado por el usuario.

## Creamos el proyecto
```
ng new angular-lazy-loading --routing
```

## Crear los componentes / módulos para el proyecto
Vamos a crear los componentes llamados home, about, contact con sus módulos que se encargarán de gestionar la carga de la información mediante la gestión de sus rutas hijas.

```
ng g m pages/home --routing
ng g m pages/about --routing
ng g m pages/contact --routing
```

Esto nos crea 2 archivos por carpeta uno de ellos es el módulo principal donde vamos a cargar los componentes, módulos que necesitemos y el modulo de enrutamiento.

El otro fichero es el que corresponde al módulo de enrutamiento donde vamos a cargar el componente con una ruta hija.

### Creación de los componentes
```
ng g c pages/home --skipTests=true
ng g c pages/about --skipTests=true
ng g c pages/contact --skipTests=true
```

## Añadir rutas hijas en los módulos para las páginas

Ahora vamos a trabajar sobre el módulo **about-routing.module.ts**.
Dentro de este archivo creamos la ruta hija que servirá para cargar el componente about.component.ts desde el módulo about.module.ts
```js
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { AboutComponent } from './about.component';

const routes: Routes = [
 {
   path: '', component: AboutComponent
 }
];

@NgModule({
 imports: [RouterModule.forChild(routes)],
 exports: [RouterModule]
})
export class AboutRoutingModule { }
```

Primero importamos los componentes que van a depender de ese módulo, creamos las rutas y luego dentro de imports hacemos uso de forChild para especificar que ese módulo va a servir rutas hijas.

En el fichero about.module.ts ya tenemos importado el AboutRoutingModule.

Hacemos los mismo pero con las páginas home y contact.
Configurando el fichero principal de rutas

Ahora vamos a nuestro módulo de rutas principales app-routing.module.ts que quedará de la siguiente manera:
```js
const routes: Routes = [
 {
   path: `home`, loadChildren: () =>
     import('./pages/home/home.module').then(m => m.HomeModule)
 },
 {
   path: `about`, loadChildren: () =>
     import('./pages/about/about.module').then(m => m.AboutModule)
 },
 {
   path: `contact`, loadChildren: () =>
     import('./pages/contact/contact.module').then(m => m.ContactModule)
 },
 { path: ``, redirectTo: `home`, pathMatch: `full` }
];
```

Dentro de nuestra variable routes vamos almacenar todas las rutas principales de nuestra aplicación.

Ruta home, aquí le decimos a Angular, cuando la ruta sea igual a home carga el hijo haciendo referencia al HomeModule. Si os fijáis aquí ya está aplicando el Lazy Loading.

Ruta about y contact, hacemos lo mismo pero haciendo las referencias a los módulos que les corresponden.

La última ruta le dice a Angular, cuando no especifique una url, llévame a la ruta home.

El último paso es visualizar la información navegando entre diferentes páginas.

## Comprobar rutas y visualizar las páginas
Accedemos al fichero app.component.html y borramos todo el contenido para añadir el siguiente:
```html
<div style="text-align:center">
  <h1>
    {{title}}
  </h1>
  <button routerLink="/home">Home</button>
  <button routerLink="/about">About</button>
  <button routerLink="/contact">Contact</button>
</div>
<router-outlet></router-outlet>
```
