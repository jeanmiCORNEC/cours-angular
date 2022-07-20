# Angular #

## Installer le CLI angular ##

    npm install -g @angular/cli

## Créer un nouveau projet angular ##

    ng new <nom-du-projet> --style=scss

## Lancer le serveur ##

    ng serve

### Connecter le serveur à votre navigateur ###

    http://localhost:4200

## Structurer votre projet angular ##

dans le dossier du projet src

le fichier index.html est le point d'entrée du projet c'est dans ce fichier que l'on fera les imports comme les polices de caractères.

le fichier styles.scss est le fichier où se trouvent les styles globaux de l'appli.

il contient dans le `<body>`la balise `<app-root>` qui   correspond à la racine de votre application, ou plus précisément à l'AppComponent, le component racine de votre application. Angular sait qu'il doit remplacer cette balise par le fameux AppComponent

dans le sous-dossier app on trouve les 3 fichiers qui composent un composant angular :  
app.component.ts  
app.component.html  
app.component.scss  

dans le fichier app.component.ts on trouve

```
@Component({
  selector: 'app-root', => Le sélecteur d'un component correspond à la balise HTML personnalisée qu'on utilisera pour l'insérer dans l'application.
  templateUrl: './app.component.html', => le fichier html qui contient le code html du composant
  styleUrls: ['./app.component.scss'] => le fichier scss qui contient le code css du composant
})
```  

## Créer un composant ##

    ng generate component <nom-du-composant>

Cela créera un fichier  
`app.<nom-du-composant>.ts`,  
`app.<nom-du-composant>.html` et  
`app.<nom-du-composant>.scss`  
dans le dossier src/app  
`app.module.ts`  été mis à jour pour inclure le composant créé  
on import le composant, et dans le @NgModule on l'ajoute aux déclarations  
  
> **Un décorateur en TypeScript permet, entre autres, d'apporter des modifications à une classe. Ici, le décorateur  @Component  vient ajouter tous les comportements nécessaires à l'utilisation de ce component dans l'application. Il est importé depuis le package  @angular/core. Tout se passe sous le capot, on n'a pas à s'en occuper !**

## Afficher des données dynamiques ##

notre class doit implémenter l'interface `OnInit`  

La méthode  ngOnInit()  est appelée automatiquement par Angular au moment de la création de chaque instance du component. Elle permet notamment d'initialiser des propriétés.  

###  La string interpolation ###

`<h1>{{title}}</h1>` il suffit de mettre le contenu de la variable entre doubles accolades

###  L'attribut binding ###

`<img [src]="imageUrl" [alt]="title">`
Les crochets permettent à Angular d'associer les valeurs des propriétés TypeScript aux attributs de l'élément HTML.

### Réagit aux événements ###

dans le fichier.ts on déclare les méthodes qui seront appelées lors des événements

```
onClickSnap() {
    if (this.buttonText === 'Oh Snap!') {
      this.buttonText = 'Oops UnSnap !!';
      this.faceSnapsService.snapFaceSnapById(this.faceSnap.id, 'snap');
    } else {
      this.buttonText = 'Oh Snap!';
      this.faceSnapsService.snapFaceSnapById(this.faceSnap.id, 'unsnap');
    }
  }
  ```

dans le fichier.html on a le button qui lors de l'événement (click) appelle la méthode onClickSnap()

> **chaque instance d'un component est totalement indépendante.  
> Pour lier une méthode à un événement d'un élément du template, mettez l'événement entre parenthèses  ()  et passez la méthode en argument  
> (click)="onClickButton()"**

##  Ajouter des propriétés personnalisées ##

### Models ###

on crée un dossier models  
et dedans un fichier `<nom-du-component>.models.ts`  
dans ce fichier on crée un model on exporte la class et on peut aussi lui passer un constructeur

```
export class <nom-du-component> {
  id: number;
  name: string;

  constructor(id: number, name: string) {
    this.id = id;
    this.name = name;
  }
}
```

On pourra instancier cette classe avec un `new <nom-du-component>()`  

> **Pour qu'une propriété puisse être injectée depuis l'extérieur d'un component, il faut lui ajouter le décorateur  @Input()**

Dans le component.ts, on doit utiliser le décorateur @input() que l'on importe depuis l'angular/core pour indiquer que la propriété est injectée depuis l'extérieur du component.  
`@input() nom-du-compo: type du compo`

Dans le ts du parent, il faut déclarer la propriété et l'initialiser.
dans le html du parent il faut utiliser l'attribut binding  
`<app-face-snap [facesnap] = "mySnap"><app-face-snap>`

## Conditionner l'affichage d'un composant ##

###  ng if ###

```
<p *ngIf="faceSnap.location === 'Paris'">
```

permet de conditionner l'affichage d'un composant si une condition est vraie ou fausse

### ng for ###

```
<app-face-snap *ngFor="let faceSnap of faceSnaps" [faceSnap]="faceSnap"></app-face-snap>
```

permet de boucler sur une liste d'éléments et d'y associer la propriété faceSnap du component  

##  Injecter du style dynamique ##

###  ngStyle ###

```
<span [ngStyle]="{ color: 'rgb(0, ' + faceSnap.snaps + ', 0)' }">🤌 {{ faceSnap.snaps }}</span>
```

la directive ngStyle prendra comme argument un objet où:  

- la clé est le nom de la propriété css
- les valeurs sont les valeurs de cette propriété

###  ngClass ###

```
<div [ngClass]="{ 'class-name': condition }"></div>
```

la directive ngClass prend comme argument un objet où:

- la clé est le nom de la classe css
- les valeurs sont les conditions qui doivent être remplies pour appliquer la classe

##  Les Pipes ##

Un pipe permet de formater l'affichage d'une donnée sans modifier la donnée sous-jacente.

exemple d'un pipe pour modifier la casse :

```
<h2>{{ faceSnap.title | uppercase }}</h2>
```

> **les pipes ne changent rien à la valeur des variables sous-jacentes. Les pipes existent uniquement pour modifier le formatage affiché d'une donnée : on ne peut pas les utiliser ailleurs que dans le template, et il est fortement déconseillé de les utiliser ailleurs que dans une string interpolation.**

> **Il existe toutes sortes de pipe pour formater les dates, les valeurs monnaitaires, les nombres. On peut aussi crée ses propres pipe**

### Changer la locale par défaut ###

Dans app.module.ts  

```
import { registerLocaleData } from '@angular/common';
import * as fr from '@angular/common/locales/fr';
import { LOCALE_ID } from '@angular/core';
providers: [
    { provide: LOCALE_ID, useValue: 'fr-FR' }
  ],
export class AppModule {
  constructor() {
    registerLocaleData(fr.default);
  }
}
```

##  Les services ##

dans le dossier `app` on crée un dossier `services`, et dedans un fichier `<nom-du-service>.ts`

> **Un service est une classe, et la façon la plus simple de déclarer une classe comme étant un service est d'utiliser le décorateur  @Injectable()  qui s'importe depuis  @angular/core**

```
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class FaceSnapsService {
  
}
```

> **L'objet de configuration qui spécifie  providedIn: 'root'  dit à Angular d'enregistrer ce service à la racine de l'application. Ce sera très souvent le cas pour vos services, car ça permet de s'assurer de n'avoir qu'une seule instance du service, partagée par tous les partis intéressés.**

Pour pouvoir utiliser un service dans un component, il faut utiliser le système d'injection de dépendances (dependency injection ou DI) que vous fournit Angular. C'est très simple : vous passez un argument du type du service au constructor du component, et Angular vous mettra à disposition la bonne instance du service.  

```
import { FaceSnapsService } from '../services/face-snaps.service';
//...
constructor(private faceSnapsService: FaceSnapsService) { }
```

> **On préfère généralement  private  pour les injections de service, car ça empêche le template du component d'y accéder directement. Donner au template accès aux injections serait un anti-pattern Angular – autrement dit, une approche plus que déconseillée, souvent pour des raisons qui ne sont pas flagrantes au premier abord.**

###  Les types literaux ###

```
snapFaceSnapById(faceSnapId: number, snapType: 'snap' | 'unsnap'): void {
```

On ne peut passer que `'snap'` ou `'unsnap'` en paramètre de la méthode.

> **Créer des types "limités" comme ça, sous forme de literal types, ce n'est pas limité aux chaînes de caractères !**

##  Le routing #

dans le dossier app, on crée un fichier `app-routing.module.ts`  
dans ce fichier, on importe ngModule à partir de `@angular/core`  
on déclare une classe avec le décorateur `@NgModule()`  
au dessus de cette déclaration, on initialise une constante routes importé depuis `@angular/router` qui contient un tableau de routes  
Ce tableau va lier les rourtes aux components correspondants.

```
const routes: Routes = [
  { path: 'facesnaps', component: FaceSnapListComponent }
  { path: '', component: LandingPageComponent }
];
```

Pour enregistrer ces routes dans votre application, il faut les passer au routeur en passant un objet de configuration au décorateur  `@NgModule()`  de `AppRoutingModule` :

```
@NgModule({
  imports: [
    RouterModule.forRoot(routes)
  ],
  exports: [
    RouterModule
  ]
})
```

pour enregistrer le router module, on le rajoute dans le tableau des imports de l `app.module.ts`

Pour expliquer à angular où il faut afficher le component spécifié par le router. On utilise la balise `router-outlet` qui permet de déclarer un outlet dans le template du component. par exemple pour avoir le header puis en dessous le routeur, on utilise :

```
<app-header></app-header>
<router-outlet></router-outlet>
```

### Créer des liens avec routerLink ###

angular utilise la directive routerLink pour créer des liens. On peut l'utiliser dans la balise `<a>` et on lui passe la route vers la quelle on veut être dirigé, on est pas obligé d'utiliser les crochets autours de la directive car on lui passe une chaine de caractères :

```
<a routerLink="facesnaps">Continuer vers FaceSnaps</a>
```

On peut rajouter une extension de directive `routerLinkActive` pour ajouter du style à un lien quand il est actif.

```
<a routerLink="facesnaps" routerLinkActive="active">Continuer vers FaceSnaps</a>
```

Ceci peut créer un 'petit bug' : si on clique sur un lien, le lien est actif, mais quand on clique sur un autre lien, le lien précédent est encore actif.  
En fait, une route est considérée comme étant active lorsqu'elle-même ou l'un de ses enfants est la route sélectionnée. On voit bien que  `/facesnaps`  est un enfant de  `/`, donc finalement Angular se comporte normalement. Cependant, il existe une configuration qui permet de considérer uniquement la route exacte et non ses enfants. Il suffit d'ajouter :  
`[routerLinkActiveOptions]="{ exact: true }`  à la directive `routerLinkActive`

```
<a routerLink="" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: true }">Home</a>
```

### Changer de route programmatiquement ###

dans le HTML,  on crée un bouton qui va permettre de changer de route.  
sur ce bouton, on utilise la methode `onContinue()` qui va permettre de changer de route au click.

```
<button (click)="onContinue()">Continuer vers snapface</button>
```

dans le ts on importe le router et on crée une méthode `onContinue()` qui va permettre de changer de route.

```
 constructor(private router: Router) {
  }

  onContinue(): void {
    this.router.navigateByUrl('facesnaps');
  }
```

### Activer les routes avec ActivatedRoute ###

on aura souvent besoin d'avoir accès à la route active, ce qui permet de créer des routes avec un paramètre.

on rajoute dans le router la route vers le nouveau component :

```
{ path: 'facesnaps/:id', component: SingleFaceSnapComponent }
```
Les `:id`  spécifient que ce qui se trouvera après  facesnaps/  correspondra à un paramètre qui s'appellera `id`.

pour pouvoir récupérer la route active, il faut injecter l'objet `ActivatedRoute` dans le constructeur du component.

```
 constructor(
    private faceSnapsService: FaceSnapsService, private route: ActivatedRoute
  ) { }
```
dans le ngOnInit, on récupére le paramètre `id` :

```
  const faceSnapId = this.route.snapshot.params['id'];
```
Les paramètres d'une route sont toujours une string, pour les transformer en number il faut faire du type cast, pour récupérer un number il faut rajouter `+` au paramètre.

```
const faceSnapId = +this.route.snapshot.params['id'];
```

On peut donc utiliser la méthode getFaceSnapById de notre service pour récupérer le snapFace correspondant à l'id.

```
this.faceSnap = this.faceSnapsService.getFaceSnapById(faceSnapId);
```
dans le html des faceSnaps, on peut supprimer tout sauf le titre et la description et rajouter un bouton voir les détails, qui redirigera vers le component SingleFaceSnapComponent du faceSnap correspondant à l'id.

```
<button (click)="onViewFaceSnap()">Voir les détails</button>
```
DAns le fichier ts on crée la méthode `onViewFaceSnap()` qui va permettre de rediriger vers le component SingleFaceSnapComponent.

```
  onViewFaceSnap(): void {
    this.router.navigateByUrl('facesnaps/'${this.faceSnap.id});
  }
```
dans la route, on passe la variable en utilisant `${}`.