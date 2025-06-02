# Angular alapok

## 0. Fejlesztői környezet beállítása

Ahhoz, hogy lokálisan tudjatok futtatni egy Angular-os alkalmazást az alábbiakra van szükségetek

* Node.js
* Node Package Manager
* Angular CLI telepítése
  
```bash
npm install -g @angular/cli@17
```

## 1. Mi az Angular?

* Angular egy nyílt forráskódú frontend keretrendszer, amit a Google fejleszt.
* Az AngularJS-t első verziója 2010-ben jelent meg, itt még  teljesen más volt a koncepció. Az Angular a ma ismert fomályát (Angular 2+) 2016-tól ismerhettük meg, teljesen újraírták és modern architektúrával készítettek el.
* Teljes megoldást nyújt nagyobb és strukturált SPA (single page application) fejlesztéshez. Korábban csak CSR (Client Side Rendering) de most már SSR (Server Side Rendering) megoldásuk is van.
* Komponens-alapú architektúrán alapul, Typescript nyelven készül.

## 2. Projektstruktúra és külső függőségek

### Külső függőségek telepítése

* Az Angular Node.js környezetben fut, így a külső csomagokat a npm (Node Package Manager) segítségével kezeljük.

```ts
npm install package-name
```

* Például

```ts
npm install playwright
```

### package.json tartalmazza:
* az alkalmazás nevét, verzióját
* a használt npm csomagokat (dependencies és devDependencies)
* a parancsokat (scripts), pl. npm start, npm test

### package-lock.json
* Ez a fájl rögzíti a pontos verziókat minden telepített csomagból, beleértve a transzitiv (függőségek függőségeit is). Ez biztosítja, hogy minden gépen pontosan ugyanaz a csomagstruktúra jöjjön létre.
* Nem kézzel szerkesztjük – az npm install automatikusan frissíti.

### angular.json

Az Angular projekt konfigurációs fájlja. Ez határozza meg

* milyen build folyamatok hogyan történjenek (pl. ng build)
* a fájlstruktúrát és fájlok elérési útjait (assets, styles, scripts)
* több alkalmazás vagy könyvtár is kezelhető benne (monorepo támogatás)

* Példa:

```json
"projects": {
  "my-app": {
    "architect": {
      "build": {
        "options": {
          "styles": ["src/styles.css"],
          "scripts": []
        }
      }
    }
  }
}
```

## 3. Angular alapfogalmak

### Komponensek

* Minden Angular alkalmazás komponensekből áll.
* Egy komponens HTML-t, CSS-t és logikát (TS fájlok) tartalmaz.

```ts
@Component({
  selector: 'app-hello',
  templateUrl: './hello.component.html'
})
export class HelloComponent {
  name = 'Világ';
}
```

### Modulok

* Az Angular alkalmazás modulokból áll.
* Minden alkalmazásnak van egy gyökér modulja: `AppModule`

```ts
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

### Adatkötés (Data Binding)

* Egy- és kétifányú adatkapcsolat:

  * `{{ title }}` interpoláció
  * `[value]="user.name"` (property binding)
  * `(click)="onClick()"` (event binding)
  * `[(ngModel)]="user.name"` (kétirányú)

### Direktívák

* HTML viselkedés bővítésére
* Strukturális direktívák: `*ngIf`, `*ngFor`
* Attribútum direktívák: `[ngClass]`, `[ngStyle]`

```html
<div [ngClass]="isSpecial ? 'special' : ''">Ez a div különleges</div>
```

### Pipes

* Adatformázás sablonban
* Beépített pipe-ok: `date`, `currency`, `uppercase`, `json`

```html
<p>{{ price | currency:'HUF' }}</p>
```

* Saját pipe:

```ts
@Pipe({name: 'capitalize'})
export class CapitalizePipe implements PipeTransform {
  transform(value: string): string {
    return value.charAt(0).toUpperCase() + value.slice(1);
  }
}
```

 * Használata

 ```html
<p>{{ 'nagybetűs' | capitalize }}</p>
```

### Lifecycle Hook-ok

1. **constructor** - komponens példányosítás
2. **ngOnInit** - komponens inicializálás
3. **ngOnChanges** - @Input() változása
7. **ngOnDestroy** - takarítás

```ts
ngOnInit() {
  this.loadData();
}
ngOnDestroy() {
  this.subscription.unsubscribe();
}
```

### Routing

* Navigáció és oldalváltás kezelése.
* Konfiguráció `Routes` tömbben.

```ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent }
];
```

```html
<a routerLink="/about">Rólunk</a>
```

### Interceptors

* HTTP kérések elfogása, módosítása
* Pl. auth token hozzáadása minden kéréshez

```ts
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const authReq = req.clone({
      setHeaders: { Authorization: `Bearer ${token}` }
    });
    return next.handle(authReq);
  }
}
```

### Guards

* Navigáció engedélyezése/elutasítása
* `CanActivate`, `CanDeactivate`, `Resolve`, stb.

```ts
@Injectable()
export class AuthGuard implements CanActivate {
  constructor(private auth: AuthService, private router: Router) {}
  canActivate(): boolean {
    if (!this.auth.isLoggedIn()) {
      this.router.navigate(['/login']);
      return false;
    }
    return true;
  }
}
```

### Angular sablonok (Template)

* Angular sablonok HTML alapúak, de speciális Angular szintaxist használnak.
* Interpolációval (`{{ }}`) adatokat jeleníthetünk meg.
* Használhatunk adat- és eseménykötéseket (`[property]`, `(event)`, `[(ngModel)]`).
* Direktívák és pipe-ok segítségével dinamikus és jól olvasható felületet hozhatunk létre.

Példa:

```html
<div *ngIf="!!user">
    <ul *ngIf="!!user.roles">
        <li *ngFor="let role of user.roles">{{ role.name }}</li>
    </ul>

    <input [(ngModel)]="user.name">
    <p>{{ user.name | uppercase }}</p>
    <button (click)="save()">Mentés</button>
</div>
```

## 4. Angular CLI

* Az Angular CLI (Command Line Interface) egy hivatalos eszköz, amely segíti az Angular projektek gyors létrehozását és karbantartását.
* Automatikusan generál fájlokat, konfigurációkat, modulokat, és segít a projekt struktúrájának betartásában.
* Parancssorból használható, pl. komponensek, modulok, szolgáltatások generálására.

### Alap parancsok

```bash
ng new my-app               # új projekt létrehozása
ng serve                    # fejlesztői szerver indítása
ng build                    # alkalmazás buildelése
ng test                     # unit tesztek futtatása
```

### Generálható Angular elemek

```bash
ng generate component name        # komponens
ng generate module name           # modul
ng generate service name          # szolgáltatás
ng generate directive name        # direktíva
ng generate pipe name             # pipe (cső)
ng generate class name            # osztály
ng generate guard name            # guard
ng generate interface name        # interfész
ng generate enum name             # enum
ng generate config name           # konfiguráció
ng generate environments          # környezeti beállítások
ng generate interceptor name      # HTTP interceptor
```

<!-- ## 9. Összefoglalás

* Angular robosztus, strukturált keretrendszer
* Fő témák: komponensek, modulok, adatkötés, routing, pipes, guards, interceptors, lifecycle hook-ok
* Nehezebb tanulni mint Vue, de nagyobb vállalati projektekben előnyös

---

Ez az anyag új tanulóknak segít átlátni az Angular világát lépésről lépésre. -->
