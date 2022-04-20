Quand on commence un nouveau langage, une nouvelle techno, nous cherchons toujours à nous rattacher à ce que nous connaissons. Quand je me suis mis à REACT/Angular avec du Typescript, et venant du monde .NET C#, j’ai commencé à faire les différences et les points communs entre ces langages.  

## Qu’est-ce que TypeScript ? 

TypeScript est un langage open source développé et maintenu par Microsoft ([github source](https://github.com/microsoft/TypeScript)), sous licence Apache 2. Il s’agit d’un « sur-ensemble » (*super set*) typé de Javascript qui se « compile » en JavaScript brut. Je mets des guillemets pour compiler, car ce n’est pas une compilation comme en C#, mais une transpilation (*[transpiler](https://fr.wiktionary.org/wiki/transpiler)*) du code TypeScript en code Javascript.  
![](https://media.giphy.com/media/3KC2jD2QcBOSc/giphy.gif)

La commande pour « compiler » un fichier TypeScript en javascript est :   
`tsc nomDuFichier.ts`.   
La commande va générer un fichier du même nom, mais en js (javascript).  
Mais alors pourquoi faire du TypeScript si c’est pour avoir du JavaScript en sortie ?  
JavaScript est un langage dynamiquement typé. Cette caractéristique, qui facilite la déclaration des variables, peut, dans certains cas, entraîner des résultats inattendus. Le système de type statique en TypeScript permet de décrire la forme d’un objet, permet à TypeScript de valider le bon fonctionnement du code. Les règles sont plus strictes avec du typage fort, du coup les erreurs peuvent se voir pendant l’écriture du code, pas à la fin en test/prod.  

## Comparaisons

Je vais mettre des bouts de code pour comparer les 2 langages, dans le sens C# vers Typescript. Pour tester les bouts de code vous pouvez utiliser avec Visual Studio Code pour C# [dotnet script](https://github.com/filipw/dotnet-script) (voir [mon post sur le sujet](https://www.ctrl-alt-suppr.dev/2022/02/28/tips-tricks-du-script-en-c/)) et pour TypeScript : [deno.land](https://deno.land/), ou directement en ligne avec [Playground](https://www.typescriptlang.org/play).  

#### Les variables

```csharp
// C#
// Déclarer une variable
var unNombre = 12345;
var unBlabla = "Faire du blabla";
// équivaut à :
int unNombre = 12345;
string unBlabla = "Faire du blabla";

// Utilisation des constantes
const int unAutreNombre = 123456;
unAutreNombre = 123456; // ---> Exception
```

```typescript
// TYPESCRIPT
// Déclarer une variable
let unNombre = 12345;
let unBlabla = "Faire du blabla";
// équivaut à :
let unNombre : number = 12345;
let unBlabla : String = "Faire du blabla";

// Utilisation des constantes
const unAutreNombre = 123456;
unAutreNombre = 123456; // ---> Exception
```

En C#, nous pouvons utiliser le mot clé `var` ([doc Microsoft](https://docs.microsoft.com/fr-fr/dotnet/csharp/language-reference/keywords/var)) pour déclarer une variable. C’est au moment de la compilation, que le compilateur va choisir le bon type pour la variable, mais nous pouvons aussi le dire de façon explicite, en indiquant directement le type. Personnellement je préfère, il n’y a pas ambiguïté. Pour les [types primitifs sur TS](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#the-primitives-string-number-and-boolean).  
En TS, c’est le mot clé `let` pour déclarer une variable, et n’a pas besoin d’être initialisée. Il y a aussi `var`, il est de portée sur la « function » et `let` est de portée sur le bloc.   
Pour les 2 langages, `const` indique que c’est une constante.   

#### Les conditions

##### if, else

```csharp
// C#
int x = 10;
int y = 20;

if (x > y) 
{
    Console.WriteLine("x est plus grand que y.");
} 
else
{
    Console.WriteLine("x est inférieur ou égal à y.");
}
```

```typescript
// TYPESCRIPT
let x: number = 10;
let y: number = 20;

if (x > y) 
{
    console.log('x est plus grand que y.');
} 
else
{
    console.log('x est inférieur ou égal à y.');
}
```

##### switch

```csharp
// C#
short day = 4;

switch (day) {
    case 1:
        console.log("C'est lundi");
        break;
    case 2:
        console.log("C'est mardi");
        break;
    case 3:
        console.log("C'est mercredi");
        break;
    case 4:
        console.log("C'est jeudi");
        break;
    case 5:
        console.log("C'est vendredi");
        break;
    case 6:
        console.log("C'est samedi");
        break;
    case 7:
        console.log("C'est dimanche");
    default:
        console.log("Euhh... y a une coui(biiiip) dans le pâté");
        break;
}
```

```typescript
// TYPESCRIPT
let day : number = 4;

switch (day) {
    case 1:
        console.log("C'est lundi");
        break;
    case 2:
        console.log("C'est mardi");
        break;
    case 3:
        console.log("C'est mercredi");
        break;
    case 4:
        console.log("C'est jeudi");
        break;
    case 5:
        console.log("C'est vendredi");
        break;
    case 6:
        console.log("C'est samedi");
        break;
    case 7:
        console.log("C'est dimanche");
    default:
        console.log("Euhh... y a une coui(biiiip) dans le pâté");
        break;
}
```

#### Les boucles

##### for

```csharp
// C#
for (var i = 0; i < 3; i++) 
{
  Console.WriteLine("Numéro de i = " + i);
}
```

```typescript
// TYPESCRIPT
for (let i = 0; i < 3; i++) 
{
  console.log ("Numéro de i = " + i);
}
```

##### foreach

```csharp
// C#
int[] collection = {10,20,30,40};

foreach(var item in collection) {
  Console.WriteLine(item);
}
```

```typescript
// TYPESCRIPT
let collection = [10, 20, 30, 40];

for (var item of collection) {
  console.log(item);
}
```

##### while

```csharp
// C#
int i = 1;

while (i < 4) {
    Console.WriteLine( "J'en suis à :" + i );
    i++;
}
```

```typescript
// TYPESCRIPT
let i: number = 1;

while (i < 4) {
    console.log( "J'en suis à :" + i )
    i++;
}
```

#### Les méthodes/functions

```csharp
// C#
// Fonction sans paramètre ni retour.
public void Display()
{
   Console.WritleLine("Hello de C# !");
}
```

```typescript
// TYPESCRIPT
// Fonction sans paramètre ni retour.
function display() 
{
    console.log("Hello de TypeScript!");
}

// pareil que
function display() : void 
{
    console.log("Hello de TypeScript!");
}
```

Le type retour d’une méthode en TS se met après les paramètres.  
`function NomDeLaFonction (nomParameter: type,...) : typeRetour { ... }`  
Si aucun retour, pas besoin d’ajouter le type de retour, mais nous pouvons le mettre ! Le retour `void` existe. Moi j’aime bien le mettre, c’est EXPLICITE !  

```csharp
// C#
public string Greet(string greeting, string name) 
{
    return greeting + " " + name + "!";
}

// Avec paramètre optionnel.
public string Greet(string greeting, string name = null)
{
    return greeting + " " + name + "!";
}

// Avec un paramètre par défaut
public string Greet(string name, string greeting = "Hello")
{
    return greeting + " " + name + "!";
}
```

```typescript
// TYPESCRIPT
function Greet(greeting: string, name: string ) : string 
{
    return greeting + ' ' + name + '!';
}

// Avec paramètre optionnel.
function Greet(greeting: string, name?: string ) : string 
{
    return greeting + ' ' + name + '!';
}

// Avec un paramètre par défaut
function Greet(name: string, greeting: string = "Hello") : string 
{
    return greeting + ' ' + name + '!';
}
```

En TS il y a une différence entre un paramètre optionnel et un paramètre par défaut. Pour un paramètre optionnel `name?: string`, si le paramètre n’est pas indiqué lors de l’appel à la function, le type sera `undefined`.  

#### Class

La doc sur les `class` en Typescript ([doc sur Typescript.org](https://www.typescriptlang.org/docs/handbook/2/classes.html)). Je vais essayer de mettre le plus de chose dans le bout de code.  

```csharp
// C#
public class Person
{
    protected readonly string name;
    // Pour montrer les niveaux de visibilité
    public int age;
    string numSecu; // --> défaut en private

    public Person(string nom, int age, string secu)
    {
        name = nom;
        age = age;
        numSecu = secu;
    }
}

public class Habitant : Person
{
    public string Adresse { get; set; }
    
    // Juste pour montrer en "Full"
    private string _autreAdresse;

    public string AutreAdresse
    {
        get { return _autreAdresse; }
        set { _autreAdresse = value; }
    }

    public Habitant(string nom, int age, string secu, string adress)
        : base(nom, age, secu) // Appel au constructeur de Person
    {
        Adresse = adress;
    }

    public void WhoAreYou()
    {
        Console.WriteLine($"Je suis " + this.name + " et j'habite au " + this.Adresse);
    }
}
```

```typescript
// TYPESCRIPT
class Person 
{
    // Fields
    protected readonly name: string;
    // Pour montrer les niveaux de visibilité
    age: number; // --> défaut en public
    private numSecu: string;
  
    constructor(nom: string, age: number, secu: string) 
    {
      this.name = nom;
      this.age = age;
      this.numSecu = secu;
    }
}

class Habitant extends Person 
{

    // Field
    _adresse: string;

    // Getter et Setter
    get adresse() {
        return this._adresse;
    }
    set adresse(value) {
        this._adresse = value;
    }

    // Constructeur
    constructor(nom: string, age: number, secu: string, adress: string) 
    {
        // super : pour faire appel au constructeur de Person.
        super(nom, age, secu);
        this._adresse = adress;
    }

    public whoAreYou () : void 
    {
        console.log("Je suis " + this.name + " et j'habite au " + this.adresse)
    }
}

// Utilisation de la class
let hab : Habitant = new Habitant("Jean", 25, "15151548", "la bas");
hab.whoAreYou();
```

En C# ou TS, il y a un quelques différences, mais ça reste très proche.  

#### Interface

Bon là c’est la grosse différence que j’ai trouvé entre C# et TS.  

##### Interface en tant que Type

```typescript
// TYPESCRIPT
interface Person {
    name: string;
    age: number;
}

let quiEstce: Person = { name:"Jean", age:25 };
```

C’est une entité, un simple objet POCO en C#. Juste des propriétés sans méthode pour ajouter du « comportement ».  

##### Interface « classique » par rapport au C#

```csharp
// C#
interface Person 
{
    name: string;
    age: number;
    
    // Déclare une fonction
    getAnneeNaissance(anneeEnCours: Date) : number;
}
```

On peut déclarer une interface, avec des propriétés et des méthodes qui seront implémentés dans une class.  
##### Interface comme type de fonction

L’interface TypeScript est également utilisée pour définir un type de fonction. Cela garantit la signature de la fonction.  
```typescript
// TYPESCRIPT
// Interface comme type de fonction
interface Person 
{
    name: string;
    age: number;
}

createPerson(name: string, age: number) : void 
{
    // code de création
    Person     
}

let unePersonne: Person = createPerson;
```

En TS : Les interfaces fournissent une structure aux objets. Elles peuvent hériter d’autres interfaces et des class.  
En C# : Les interfaces sont des contrats pour les classes à implémenter. Elles peuvent seulement hériter d’autres interfaces.  

#### Et le LINQ dans tout ça ?

Une des grandes forces de C# c’est [LINQ](https://docs.microsoft.com/fr-fr/dotnet/csharp/programming-guide/concepts/linq/), alors est-ce qu’il y a la même chose ?   
Eh bien oui !  
![](https://media.giphy.com/media/3KC2jD2QcBOSc/giphy.gif)

###### Where
```csharp
// C#
Person jean = new Person() { Name="Jean", Age=42, Sexe="homme"};
Person gisel = new Person() { Name="Giselle",Age=52,Sexe="femme"};
Person marcel = new Person() { Name="Marcel", Age=25,Sexe="homme"};
Person marion = new Person() { Name="Marion", Age=28, Sexe="femme"};
Person[] allPeople = new Person[] { jean,gisel, marcel, marion };

var selectedPerson = allPeople.Where(x => x.Sexe == "homme");

foreach (var person in selectedPerson)
{
	Console.WriteLine(person.Name);
}

public class Person
{
	public string Name { get; set; }
	public int Age { get; set; }
	public string Sexe { get; set; }
}
```
```typescript
// TYPESCRIPT
interface Person{
	name: string;
	age: number;
	sexe: string;
}

let allPeople : Person[] = [{name:"Jean", age: 42, sexe:"homme"},
							{name:"Marcel", age: 25, sexe:"homme"},
							{name:"Giselle", age:52, sexe:"femme"},
							{name:"Marion", age:28, sexe:"femme"}]

// utiliser "filter"
let selectedPerson = allPeople.filter(x => x.sexe === 'homme');

for(let person of selectedPerson)
{
	console.log(person.name);
}
```
###### Any
```csharp
// C#
bool found = allPeople.Any(x => x.age == 25);
```
```typescript
// TYPESCRIPT
boolean found = (allPeople.findIndex(x => x.age === 25) >= 0);
```
###### FirstOrDefault
```csharp
// C#
var person = allPeople.FirstOrDefault(x => x.Name == "Jean");
```
```typescript
// TYPESCRIPT
let person = allPeople.find(x => x.name === "Jean");
```

## Conclure

Je n’ai pas tout mis car il y a des particularités dans chaque langage, et c’est vraiment dans une utilisation quotidienne et sur du long terme qu’on maitrise un langage, mais nous pouvons voir que les 2 langages se rapproche beaucoup.  
