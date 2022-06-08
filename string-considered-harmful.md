---
marp: true
theme: uncover
class: invert
paginate: true

---

# String Considered Harmful

</br>

### Boosterconf 2022
### Morten Nygaard Åsnes


</br>

[@mortenaa](twitter.com/mortenaa)
morten.nygaard.aasnes@dfind.no
<!--
* Intro
* Edgar Dijikstra: Go to statement considered harmful - 1968
* Litt om typer, for de enkle objektene der man 
* ofte velger å bruke primitive typer.
-->

---

# Types

* Set of possible values and operations
* Primitive types
* User defined types
* Static vs Dynamic
* Exploratory programming

<!--
* Hva er en type?
* Sett av mulige verdier, og operasjoner
* Mismatch mellom hva som er mulig å hva som er lovlig
* Statiske og dynamiske språk, fordeler for statiske språk
* prøv og feil koding med dynamiske.
* compile time vs runtime
* gammel krangel
* trend mot mer typer og typesjekking
-->

---

# Primitive Obsession

* Why do we use primitive types?
    * Optimization
    * Convenience
    * Verbosity bad?

<!--
* Primitive obsession = overdreven bruk av primitive
  typer for enkle objekt.
  mange eksempel. Navn. Telefonnr. Adresse. Personnr.
* Optimering av minne og cpu. historisk. ikke like nødvendig. kompilator
  optimerer vekk.
* Det er enkelt å raskt
* Vi liker ikke unødvendig verbositet
-->

---

* Why we should define a new type
    * Validation in one place
    * Communicates intent
    * Type safety

<!--
* Samler validering på en plass
* Kommuniserer hva vi vil uttrykke både til lesere av koden og til
  kompilatoren. Bedre lesbarhet.
* Typesikkerhet. 
* Verbosity ok.
* Type inference
-->

---

# The Compiler is your friend

* Compile time errors > runtime errors
* Shorten feedback loop
* Help the Compiler help you!

<!--
* Bruker du statisk språk så utnytt det
* Jo tidligere du får feedback på feil jo bedre
* I moderne ide er feedback umidellbar.
* En feil kompilatoren kan fange er en unit test du ikke trenger skrive
* Gjør refaktorering lettere
-->

---

# The problem with String

* A string can contain anything
* Validation must be enforced outside the type
* Buypasses the type system

<!--
* Kanskje den mest missbrukte primitive typen
* Kan inneholde alt. XML, Json, Base64 coded gif
* Mulighetsrommet for mulige verdier i en string er
  alltid mye større en det du ønsker å tillate som lovlige verdier
* Vi mister typesikkerhet.
* Valideringen må gjøres utenfor typen, og må gjøres
  flere steder
-->

---
# A String example

* National identity number
* 11 digits
* 2 control digits
<!--
* Fødselsnummer, kjent eksempel
* Kan behandles som en id uten struktur
  men inneholder også struktur som man må hente ut ved validering
* Lettvint å bruke string
* Men validering må gjøres flere steder.
* må huske å validere
* mister typesikkerhet
* en streng med 11 tall er en en
-->
---

# Custom types dont have to be verbose

```kotlin

"27113626688".toNationalIdentiyNumber

42.asPersonAge

10.dp
```
<!--
* kotlin eksempel
* extension functions
* andre språk har lignende funksjonalitet
* om ikke er ikke konstruktører og factory metoder så ille
-->
---
# Make illegal states unrepresentable

* Only valid instances can be constructed
* Can be proved at compile time
* Replaces some unit test
<!--
* Et steg videre fra de enkleste typene
* prinsipp å gjøre det umulig å konstruere ugyldige instanser
  i følge domenet eller business rules
* tilbake til type som set, prøver vi å oppnå at settet av mulige
  verdier matcher eksakt settet av gyldige verdier
* Kompilatoren kan dermed forsikre at det ikke er mulig å konstruere
  ugyldig state,

A common example where this can be used, is when you have a class that consist of a few related objects, But the combined state of the whole thing is only valid for some combinations of values. Making it possible to construct instances of the class that are nonsensical.
-->

---

```kotlin
enum JobState { 
    Submitted,
    Running,
    Finished,
    Failed
}

data class Job (
    val id: UUID,
    val state: JobState,
    val progress: Int?,
    val result: Result?,
    val error: Error?
)
```
<!--
Et vanlig eksempel
* Klasse består av objekter som hører sammen
* men ikke alle kombinasjoner gir mening.
* Eksempel, en jobb eller prosess som kan ha ulike tilstander
* Tilhørende informasjon er satt, eller ikke avhengig av tilstand
* Kan ende opp med en tilstand som ikke gir mening
* progress for en jobb som ikker er startet
* feil på en jobb som ikke er i failed state, osv
* Gjør klassen vanskeligere å bruke
* øker sjansen for feil
-->

---

```kotlin
sealed class Job(val id: UUID)

data class Submitted(val id: UUID): Job(id)
data class Started(val id: UUID, val progress: int): Job(id)
data class Finished(val id: UUID, val result: Result): Job(id)
data class Failed(val id: UUID, val error: Error): Job(id)
```
<!--
* løsningen er å bruke typesystemet slik at kun gyldige tilstander
  kan konstrueres.
* sealed classes i kotlin er nyttig til dette
* Også i Java fra Java 17
* I dette eksempelet kan kun en job i failed state ha error
* og kun en statet jobb har progress, osv
* og komilator kan sjekke at dette følges
* og ide kan guide deg til å konstruere objektet
* hvor stopper vi? detaljniva. precondition. dependent types
-->

---

# Conclusion

* Make types
* Be carefull with Strings
* Prefer compile time errors to runtime errors

</br>

[@mortenaa](twitter.com/mortenaa)
morten.nygaard.aasnes@dfind.no
<!--
* lag egne typer, også for enkle verdier
* ikke la string bli en ukritisk go to type for alt mulig
* strukturer koden slik at du får compile time feil
  heller enn runtime feil
* altså, hjelp komilatoren å hjelpe deg!
-->
