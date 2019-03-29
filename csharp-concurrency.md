# Parallelisme i C#
I C# bruger eksisterer der flere forskellige parallelismemønstre. I dette dokument vil vi introducere _task-based asynchronous pattern_ som blev introduceret i .NET 4.0. Dette mønster gør brug af `Task` klassen som repræsentere en lille opgave som skal eksekveres på en anden tråd end main tråden. Der er to varianter af denne klasse, en med returværdi og en uden.
```
Task noReturn = new Task(someAction);

Task<int> intReturn = new Task<int>(someAction);
```
