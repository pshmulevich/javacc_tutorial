#What is JavaCC?
JavaCC stands for Java Compiler Compiler. It is a Lexical Analyzer Generator and a Parser Generator. 
As input it takes a set of regular expressions that describe a type of token in the language and ouput a lexical analyzer that reads an input file and separates it into tokens.

##More on how to get started working with JavaCC. 
Most people use the Eclipse workspace because of its versitility as well as Maven in order to build their projects. So here are the steps to set up working with ANTLR using Eclipse and Maven: 
1) Go to this url:  http://eclipse-javacc.sourceforge.net/
2) Install the JavaCC plugin
3) Save as a local zip
4) Open Eclipse workspace 
5) Add new software by navigating to the 'help' tab
6) Selesct the local zip folder
7) Unclick categorized (**Critical or it won't work)
8) Accept defaults and install the plugin.
9) Create a new Maven project
10) Create a new package (make sure its not resources, src/main/java ok)
11) Create new JavaCC template with a name of your choosing.

Your result will be a generated JavaCC template class that will follow this general pattern:
```
options{
/* Code to set various options flags */
}
PARSER_BEGIN(foo)
public class foo {
/* This segment is often empty */
}
PARSER_END(foo)
TOKEN_MGR_DECLS :
{
/* Declarations used by lexical analyzer */
}
/* Token Rules & Actions */
```

###What does each part of this program mean?

this is the generated line with the plugin version
```**
 * JavaCC template file created by SF JavaCC plugin 1.5.28+ wizard for JavaCC 1.5.0+
 */

options
{
  static = true;
}
```


Here it states your parser program begins

```
PARSER_BEGIN(MyNewGrammarParser)

public class MyNewGrammarParser
{
  public static void main(String args []) throws ParseException
  {
    MyNewGrammarParser parser = new MyNewGrammarParser(System.in);
    while (true)
    {
```
This is just stuff you can read from the console with a suggestion prompt

```
     System.out.println("Reading from standard input...");
      System.out.print("Enter an expression like \"1+(2+3)*4;\" :");
      try
      {
        switch (MyNewGrammarParser.one_line())
        {
          case 0 : 
          System.out.println("OK.");
          break;
          case 1 : 
          System.out.println("Goodbye.");
          break;
          default : 
          break;
        }
      }
      catch (Exception e)
      {
        System.out.println("NOK.");
        System.out.println(e.getMessage());
        MyNewGrammarParser.ReInit(System.in);
      }
      catch (Error e)
      {
        System.out.println("Oops.");
        System.out.println(e.getMessage());
        break;
      }
    }
  }
}
```
This line denotes the end of your parser program
```
PARSER_END(MyNewGrammarParser)
```

Here are the tokens that you look for and/or use in your program; this is the skip token
```
SKIP :
{
  " "
| "\r"
| "\t"
| "\n"
}
```

These are the math operation tokens
```
TOKEN : /* OPERATORS */
{
  < PLUS : "+" >
| < MINUS : "-" >
| < MULTIPLY : "*" >
| < DIVIDE : "/" >
}
```

These are the constant identifiers tokens

```
TOKEN :
{
  < CONSTANT : (< DIGIT >)+ >
| < #DIGIT : [ "0"-"9" ] >
}
```

These are the java code lines that allow you to process what you find (See notes below)

```
int one_line() :
{}
{
  sum() ";"
  {
    return 0;
  }
| ";"
  {
    return 1;
  }
}

void sum() :
{}
{
  term()
  (
    (
      < PLUS >
    | < MINUS >
    )
    term()
  )*
}

void term() :
{}
{
  unary()
  (
    (
      < MULTIPLY >
    | < DIVIDE >
    )
    unary()
  )*
}

void unary() :
{}
{
  < MINUS > element()
| element()
}

void element() :
{}
{
  < CONSTANT >
| "(" sum() ")"
}
```


###With JavaCC template ready, we can talk about the parts of the program created and what you fill them with.

Tokens are described by rules. They follow the pattern like:
```
TOKEN:
{
<TOKEN_NAME: RegularExpression>
}
```
You name the token you are looking for and after the colon you provide the regular expression that you are looking for. Here is an example:
```
TOKEN :
{
<ELSE: "else">
}
```
This token is essentially looking for the string "else" in whatever you are examining.
You can also have more than one token in the same block by using the delimeter"|".

Lets look at some with real application:

This skip token is meant to skip single-line comments within code:
```
SKIP:
{
	<"//" (~["\n"])*"\n">
}
```

This skip token is meant to skip multi-line comments within code (Please note this is approx):
```
SKIP:
{
	<"/*"(~["*/"])*"*/">
}
```

If you want to do more than just find patterns, it is also possible for you to inject java into your code within "{ }". For example, if you want to skip and count the number of comments you find "//", you can do the following:
```
SKIP :
{
<"//"(~["\n"])* "\n"> 
{ numcomments ++ }
```
