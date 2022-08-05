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

##  Les observables ##

- Un Observable est un objet qui émet des valeurs au cours du temps.

- Il est typé : il émettra toujours des valeurs du même type.

- Un Observable peut émettre une erreur : à ce moment-là, l'Observable est détruit et n'émettra plus de valeurs.

- L'Observable peut également être complété : il est aussi détruit et n'émettra plus rien.

On observe un Observable avec sa méthode  subscribe()   pour "souscrire" à l'Observable.

> **Il ne faut pas oublier de  unsubscribe()  quand on n'a plus besoin de l'Observable, car sinon, on court le risque de créer des fuites de mémoire ! Mais ne vous inquiétez pas, ce sera le sujet d'un prochain chapitre.**

par convention le nom d'un observable se termine par `$` :

```
  const interval$ = interval(1000);
```

> **un observale n'emet que lorsque l'on y a souscrit, et si on y a souscrit plusieurs fois, on crée une nouvelle instance de cet observable pour chaque souscription**

>**Très souvent, l'objectif sera d'afficher les émissions d'un Observable dans le DOM, et pour cela, Angular nous fournit `le pipe async`  . Contrairement à tous les autres pipes que vous avez rencontrés jusqu'ici, `le pipe  async`  ne formate pas des données : il souscrit à un Observable et insère les émissions dans le DOM**

dans onInit, on déclare un nouvel Observable :

```
  interval$!: Observable<number>;
```

dans la méthode ngOnInit on initialise l'Observable :

```
  this.interval$ = interval(1000);
```

dans la vue, on utilise `le pipe async` pour insérer les émissions dans le DOM :

```
  <h1>{{ interval$ | async }}</h1>
```

## Les opérateurs ##

on utilise les opérateurs pour manipuler les observables. Il existe deux types principaux d'opérateurs :

- les opérateurs bas niveau – ces opérateurs touchent aux émissions directement, généralement pour transformer ou filtrer ces émissions.

- les opérateurs haut niveau – ces opérateurs touchent à l'Observable lui-même.
  
### Les opérateurs bas niveau ###

>**Pour appliquer des opérateurs à un Observable, on les passe, dans l'ordre, à une méthode qui s'appelle  `pipe()`.On crée un nouvel Observable en branchant un "tuyau" à un autre Observable et en y ajoutant les opérateurs que l'on veut y ajouter. Le concept de "tuyau", pipe, en anglais, est très utile : chaque émission de l'Observable traverse le tuyau et donc les opérateurs dans l'ordre.**

1. l'opérateur map()
on va pour l'exemple, transformer les emissions de l'observable qui étaient de type number en string. il faut donc changer le type de l'observable en string

```
  this.interval$ = interval(1000).pipe(
    map(value => value % 2 === 0 ? `le nombre ${value} est pair` : `le nombre ${value} est impair`)
  );
```

>**!!!! Attention pour pouvoir insérer la valeur avec ${value}, il faut utiliser les backtick `**

2. l'opérateur filter()
on va pour l'exemple, filtrer les emissions de l'observable qui étaient de type number et ne garder que les divisible par 3

```
  this.interval$ = interval(1000).pipe(
    filter(value => value % 3 === 0)
  );
```

3. l'opérateur tap()

>**En programmation réactive, un effet secondaire est une fonction qui fait quelque chose avec les émissions d'un Observable sans les modifier.**

on crée la méthode logger() qui va afficher les émissions de l'Observable dans la console.

```
  logger(text: string): void {
    console.log(`Log: ${text}`);
}
```

il reste a ajouter le tap() à l'observable :

```
this.interval$ = interval(1000).pipe(
    filter(value => value % 3 === 0),
    map(value => value % 2 === 0 ?
        `Je suis ${value} et je suis pair` :
        `Je suis ${value} et je suis impair`
    ),
    tap(text => this.logger(text))
);
```

>**il existe de nombreux autres opérateurs de bas niveau**

## Les opérateurs de haut niveau ##

Observable de haut niveau :  c'est un observable qui souscrit à d'autres Observables.  
Un Observable haut niveau consiste donc en un Observable extérieur qui souscrit à des Observables intérieurs selon ses émissions. Quand l'Observable extérieur émet "flux 1", il souscrit à l'Observable intérieur qui y correspond.
Ce sont les opérateurs haut niveau qui permettent ce type de souscription.

###  mergeMap ###

Cet opérateur représente donc la mise en parallèle : pour chaque émission de l'Observable extérieur,  mergeMap  souscrit à l'Observable intérieur sans se soucier si l'Observable intérieur précédemment souscrit a complété ou non.

```
lightObservable$.pipe(
    mergeMap(color => getTrainObservable$(color))
).subscribe();
```

>**On utilisera  mergeMap  quand l'ordre des souscriptions n'a pas besoin d'être conservé.**  

### concatMap ###

Avec concatMap si on a une émission d'un observable intérieur avant que l'observable précédent ai été complété, on stock l'ordre des émissions et on  attend que l'Observable intérieur précédemment souscrit ait été complété , et on appel le suivant dans la liste .  

```
lightObservable$.pipe(
    concatMap(color => getTrainObservable$(color))
).subscribe();
```

>**concatMap va donc assurer la mise en série, ce qui est important quand l'ordre des opérations doit être conservé.**

###  exhaustMap ###

Avec exhaustMap, on ignore les autres émissions d' Observable intérieur, tant que le dernier souscrit n'a pas été complété, puis on se remet à écouter.

```
lightObservable$.pipe(
    exhaustMap(color => getTrainObservable$(color))
).subscribe();
```

>**exhaustMap  sera utile quand un événement doit être entièrement traité avant de permettre à d'autres événements d'être émis.**

### switchMap ###

Avec switchMap on va annuler le dernier Observable intérieur souscrit, si celui-ci n'est pas complété, et qu'un nouvel Observable intérieur a émit.

```
lightObservable$.pipe(
    switchMap(color => getTrainObservable$(color))
).subscribe();
```

>**Quand l'Observable extérieur émet, s'il y a déjà un Observable intérieur en cours,  switchMap  va unsubscribe ("se désabonner") de lui pour subscribe au nouveau. switchMap  servira donc quand la dernière émission de l'Observable extérieur est celle qui nous intéresse.**

En résumé:

- mergeMap  assure la mise en parallèle : l'Observable extérieur peut souscrire aux Observables intérieurs suivants sans attendre que les précédents soient complétés.

- concatMap  assure la mise en série : il attend que les Observables intérieurs complètent avant de souscrire aux suivants– même si l'Observable extérieur émet plusieurs fois. Les Observables intérieurs seront traités en séquence à la suite.

- exhaustMap  assure le traitement complet d'une souscription avant d'observer une nouvelle émission de l'Observable extérieur. Si d’autres demandes sont faites entre temps, elles ne seront pas prises en compte.

- switchMap  traite la dernière demande de souscription de l’Observable extérieur et annule toute souscription précédente non-complétée.  

## Observables et fuite de mémoire ##

1. si on utilise la méthode `subscribe` sur un Observable.

- Le cas le plus simple  est si on connait le nombre d'émission de l'observable que l'on veut.  
Dans ce cas on utilise la méthode `take()` à laquelle on passe le nombre d'émission que l'on veut.

```
lightObservable$.pipe(
    take(3)
).subscribe();
```

- Dans le cas où l'on veut recevoir toutes les émissions d'un Observable pendant la durée de vie du component. Du coup, il faut trouver un moyen de compléter l'Observable au moment de la destruction du component. Pour cela, vous allez utiliser le pattern "Destroy Subject".  

On utilise le `**lifecycle hook**` `ngOnDestroy` pour compléter l'Observable.
Pour l'utiliser, il faut rajouter  OnDestroy  (importé depuis  @angular/core) aux  implements  du component :

```
export class FaceSnapListComponent implements OnInit, OnDestroy {
    //...
```

on rajoute dans le composant la methode `OnDestroy():void{}`.

On crée un type spécial d'observable: **un subject**

Un Subject est un Observable que l'on pouve faire émettre à la demande. Von crée donc créer un Subject appelé  destroy$  qui émettra une seule fois, au moment de la destruction du component.

on le déclare dans notre classe :

```
export class FaceSnapListComponent implements OnInit, OnDestroy {

  faceSnaps!: FaceSnap[];
  private destroy$!: Subject<boolean>;

  constructor(private faceSnapsService: FaceSnapsService) { }
  //...
```

Dans le `onInit()` il faut initialiser `destroy$` :

```
ngOnInit(): void {
    this.destroy$ = new Subject<boolean>();
    //...
```

La dernière étape de la création de  destroy$  est de le faire émettre dans  `ngOnDestroy()`  . Pour faire émettre un Subject, on appelle sa méthode  `next()` :

```
ngOnDestroy(): void {
    this.destroy$.next(true);
}
```

Maintenant il faut dire à notre observable qui fuit de se _compléter_  avec la méthode `takeUntil()` :

```
interval(1000).pipe(
    tap(console.log),
    takeUntil(this.destroy$)
).subscribe();
```

2. Le `pipe async`

Comme vu précédement, on peut souscire  à un Observable et afficher ses émissions dans le DOM avec le pipe async.

>**Dans ce cas,TOUT Observable souscrit avec le `pipe async` est automatiquement unsubscribe lors de la destruction du component qui le consomme.**
