# Hilltop

Hilltop is a text-based, general-purpose programming language. Hilltop provides a programming paradigm that borrows concepts from spreadsheet programming.

A Hilltop application is programmed by providing a text-based description of cells and their attributes. Cell attribute values are expressed as literal values or as one or more instructions. As with spreadsheet formulas, cell attribute instructions are automatically reevaluated when their dependencies receive values. 

A Hilltop application is programmed using a Hilltop-compatible editor.

## **_Core Concepts_**

Consider the following spreadsheet application:

![alt text](/assets/images/CoreConcepts1.png)

This application could be represented in text as follows:

      B2 = ‘Fahrenheit
      B3 = 80
      C2 = ‘Celsius
      C3 = ROUND(B3-32*(5/9),0)

But what about this application:

![alt text](/assets/images/CoreConcepts2.png)

Without the spreadsheet interface and its row and column labels, cell names and other cell attributes such as the cell's value -- which is just one of many cell attributes including screen position, typeface, font size, alignment, and decimal places -- could be described using text as follows:

      Fahrenheit_label
      	Value = ‘Fahrenheit
      	Typeface = Calibri
      	FontSize = 12
      	Alignment = Left
      	X = 10   //don’t worry about the units for now
      	Y = 10
      Fahrenheit_value
      	Value = 80
      	Typeface = Calibri
      	FontSize = 12
      	Alignment = Left
      	DecimalPlaces = 0
      	X = 10
      	Y = 15
      Celsius_label
      	Value = ‘Celsius
      	Typeface = Calibri
      	FontSize = 12
      	Alignment = Left
      	X = 20
      	Y = 10
      Celsius_value
      	Value = ROUND(Fahrenheit_value-32*(5/9),0)
      	Typeface = Calibri
      	FontSize = 12
      	Alignment = Left
      	DecimalPlaces = 0
      	X = 20
      	Y = 15

In a spreadsheet, many cell attributes, such as typeface, font size, alignment, and decimal places, have default values. Thus, cell attributes need only be listed when changing a default value or where no default value exists. The text description above may therefore be simplified as follows:

      Fahrenheit_label
      	Value = ‘Fahrenheit
      	X = 10
      	Y = 10
      Fahrenheit_value
      	Value = 80
      	X = 10
      	Y = 15
      Celsius_label
      	Value = ‘Celsius
      	X = 20
      	Y = 10
      Celsius_value
      	Value = ROUND(Fahrenheit_value-32*(5/9),0)
      	X = 20
      	Y = 15

Spreadsheet cell attributes vary by cell type. For example, decimal places may be specified for numeric-type cells but not for date-type cells, while date formats may be specified for date-type cells but not for text-type cells. Hilltop cell attributes also vary by cell type.

Hilltop uses prefixes such as 'txt' and 'num' to identify cell and attribute types, as well as the types of values that are returned by functions. For example:

      txtFahrenheit_label
      	txtValue = Fahrenheit 	//no need for the ‘ to indicate a text value
      	numX = 10
      	numY = 10
      	…
      txtCelsius_value
      	numValue = numfunRound(numFahrenheit_value-32*(5/9),0)
      	…

In Hilltop, a cell’s type is indicated once by the programmer. Thereafter, the Hilltop program editor will automatically fill in type prefixes, although the programmer may hide them from view, if desired. Attribute and function return types are predefined and therefore require no prefix-based type-ing by the programmer.

In place of rows and columns of cells, Hilltop organizes cells hierarchically under a root cell that represents the application itself (and that has its own attributes). Child cells are distinguished from their attributes by using an additional ‘.’ prefix for cells and a ‘:’ prefix for attributes. Indentation is also used, which the Hilltop program editor will handle automatically. For example:

      appTempConverter
      	.txtFahrenheit_label
      		:txtValue = Fahrenheit
      		:numX = 10
      		:numY = 10
      	.numboxFahrenheit_value   // ‘numbox’ indicates that this cell is for numeric user input
      		:numValue = 80    // 80 is provided as a default value
      		:numX = 10
      		:numY = 15
      		:numWidth = 5
      		…

As in a spreadsheet, Hilltop cells are reactive (i.e., their values are automatically reevaluated) when cells on which they depend receive a value. But unlike a spreadsheet, where only a cell’s value attribute may depend on the value attribute of another cell, in Hilltop the value of any cell attribute may depend on the value of any other cell attribute. For example, a cell’s font size could be made to depend on another cell’s numeric value as follows:

      .numboxCelsius_value
      	:numFontSize = numfunRound(.numboxFahrenheit_value)

Although a cell’s value is just one of its attributes, it is arguably its most important one. Therefore,

      .numboxFahrenheit_value
      	:numValue = 80

can be shortened to 

      .numboxFahrenheit_value = 80

and references to `.numboxFahrenheit_value:numValue` can likewise be shortened to `.numboxFahrenheit_value`.

Here is the final Hilltop code:

      appTempConverter
      	.txtFahrenheit_label = Fahrenheit
      		:numX = 10
      		:numY = 10
      	.numboxFahrenheit_value = 80
      		:numX = 10
      		:numY = 15
      		:numWidth = 5
      	.txtCelsius_label = Celsius
      		:numX = 20
      		:numY = 10
            .numboxCelsius_value = numfunRound(.numboxFahrenheit_value-32*(5/9),0)
      		:numX = 20
      		:numY = 15

Cells may be listed in any order under their common parent. Thus, the following is functionally equivalent to the above example:

      appTempConverter
            .numboxCelsius_value = numfunRound(.numboxFahrenheit_value-32*(5/9),0)
      		:numX = 20
      		:numY = 15
      	.numboxFahrenheit_value = 80
      		:numX = 10
      		:numY = 15
      		:numWidth = 5
            .txtFahrenheit_label = Fahrenheit
      		:numX = 10
      		:numY = 10
            .txtCelsius_label = Celsius
      		:numX = 20
      		:numY = 10

##
**_Guessing Game_**

Attribute values may be expressed using one or more instructions. Where more than one instruction is needed, they may be placed in a `:txtInstructions` attribute, such as in the following Guessing Game example:

![alt text](/assets/images/GuessingGame.png)

The Hilltop editor uses highlighting to distinguish literal values, in place of quotes that most other programming languages use.

`:txtInstructions` is a sub-attribute of any attribute, and is also an attribute of `rulePlayAgain`, which is a rule-type cell.

The instructions in `:txtInstructions` are evaluated whenever any attribute that is **_evaluated_** within `:txtInstructions` receives a value. Thus, in the above example, `.txtAnswer:txtValue:txtInstructions` is evaluated whenever `.numboxGuess` receives a value.

The Hilltop program editor supports If-Then tables as shown above, as well as classic If-Then statements and nested If-Then statements.

Boolean-type attribute values may be assigned ‘Yes/No’ or ‘True/False’.

##
**_Flappy Bird_**

In a spreadsheet, a cell is “instantiated” when its value or other attributes are provided by the programmer or its value is referenced by another cell or macro. In Hilltop, one instance of each named cell is created at runtime unless otherwise specified. Multiple instances of a cell may be created using an `<n>` or `<n..m>` suffix after the cell name. Named cell instances may also be specified using `<name>`. A cell template or “metacell” may be defined using `<>`, from which cells may be created programmatically using the `Create` command. `<*>` is used to refer to all instances of a cell.

In the following Flappy Bird game example, `.vgpColumns<>` and `.imgColumn<>` are metacells, and `<Top>` and `<Bottom>` are named instances of each instance of `.imgColumn<>`. Thus, when an instance of `.vgpColumns` is created, `<Top>` and `<Bottom>` instances of `.imgColumn<>` are also created as child cells of the `.vgpColumns` cell instance. `ThisCell` and `ParentCell` may be used when desired to refer to specific cell instances.

`.vgpColumns` and `.vgpGameOverText` are visual-group-type cells under which multiple child cells may be grouped and that may include default attribute values for group members.

![alt text](/assets/images/FlappyBird1.png)
![alt text](/assets/images/FlappyBird2.png)
![alt text](/assets/images/FlappyBird3.png)

_To be continued..._
