#talk #booster 
Frist 1. November  
  
String considered harmful?  
Fnr  
Typesystem  
Statisk vs dynamisk typing  
Type vs unit testing  
Type som dokumentasjon  
Formel definisjon av type  
Set teori  
Tillater typen din et større rom(domene?) en intensjonen din er?  
String som intermediate type  
Avanserte typesystem  
Haskell  
Product typer og sum typer  
Hva kan vi gjøre når typesystemet er begrenset. Invariant, unit test, privat konstruktør. Men hva med reflection og deserialisering  
  
Tradisjonelle imperative språk som java og c# får funksjonelle konsepter. Også innen type systemet. Men noen funksjonelle kan mer presist definere typer.  
  
Hva hjelper typesystemet når vi ukritisk bruker primitive typer som ikke avgrenser verdiene til gyldige?  
Make illegal states imposible  
  
testability (random data, dummy data)  
  
a type is a set of possible values. bool is very specific, and can only hold "valid" values. Int can hold integers, but maybe integers that does not make sense in a certain context. But String can actually hold anything! There is almost no restrictions (todo: check actual restrictions)  
  
må unit teste mulige typer alle steder om vi ikke lager en custom klasse/type  
  
Ssn why not int? Leading zero?  
  
Validation annotation  
  
show example of separate strings as part of illegal state. combine to custom type that does not allow illegal states. this is the goal.  
  
Regex som språk. Presist definert gyldig innhold. Men uleselig.  
  
Extension on number to make less verbose composition. Like 10.dp in jetpack. Could make .year etc  
  
But parameter names explains what a string does? (name, address) but not enforced or checked. More like documentation, and can actually mislead if they are not updated when refactoring.  
  
Types helps with refactoring. Refactoring fun(String, String) vs fun(Name, Address)


Types are sets of possible values. But string could fit any other type. Json, xml, anything. 

there are good ways to validate input, we use for web requests (anotations, etc.), but we
want a good consistent type model 

Sealed classes in Java? 
Ssn string - validate input - but still room for error if just a string. Encapsulate in class's with validation. Should not be afraid to encapsulate even simple types