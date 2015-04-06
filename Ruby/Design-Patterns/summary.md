# Design Patterns in Ruby by Obie Fernandez

This book draws on the 'Gang of Four' (Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides) first published Design Patterns: Elements of Reusable Object-Oriented Software in 1994.  In this book, Fernandez provides for uses in Ruby of 14 of the original 23 patterns.

## Patterns for Patterns (last one added by Fernandez)

1. "Separate out the things that change from those that stay the same"
Write less tightly coupled code and contain the parts that are going to change in methods or classes so that making a change does not require making it making places.

2. "Program to an interface, not and implementation"
Program to the most general type - for example, instead of car, program to vehicle.

3. "Prefer composition over inheritance"
Inheritance can magnify the coupling of code.  Composition allows for "assembl[ing] functionality from the bottom up" by providing references to other objects.  The difference is between "saying that an object is a kind of soemthing and isntead say that it has something".

4. "Delegate, delegate, delegate"
"Pass the buck" technique where the only thing a method does in one class is call a method on another class.
--
5. "You ain't gonna need it"
"The YAGNI principle says sinply that you should not implement features, or design in flexibility that you don't need right now".  If code is written well, it will allow enought flexibility to be able to be changed later.

# Brief Descriptions of Each Pattern
- 
