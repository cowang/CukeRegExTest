# CukeRegExTest

## An error when using a regular expression in the step definition.

### **BUT THE ERROR IS MINE**, as it turns out

I have the explanation below the Original Error Notes

### Here are the Original (misguided) Error Notes

Cucumber finds extra arguments in the Gherkin code for some legal forms of regular expressions in the step definition.

When the Cucumber regular expression in the step definition ends with ...(s)? and expects a doc string, such as

    @Given("^[Ff]ormula(s?)$")
    public void formula(String formulaLines) {
        // Write code here that turns the phrase above into concrete actions
        throw new io.cucumber.java.PendingException();
    }

for Gherkin input, such as

    Given formula
      """
      p_range === q_range
      """
      
then Cucumber produces an error message like:

    io.cucumber.core.exception.CucumberException: Step [^[Ff]ormula(s?)$] is defined with 1 parameters at 'StepDefinitions_eTLeanTaP.formula(java.lang.String)'.
    However, the gherkin step has 2 arguments:
    * 
    * DocString:
         """null
         p_range === q_range
         """

### And here is why this is my error and not Cucumber's

My error is that I was using parentheses to create a delimited optional string, which is perfectly legal for regular expressions and works fine, 
except that parentheses also mark off a _capture group_, 
which Cucumber uses as the `String` arguments to capture for passing to the step-definition parameters. If and only if there is no capture group, 
then Cucumber expects to see a doc string below the `Given` expression in the test. 
Since I have a capture group, namely `(s)?` in my regular expression, Cucumber expects a single string for the argument and no doc string. 
Apparently when I code `Given formula`, Cucumber sees no `s` at the end and creates an empty string argument, then it sees the doc string 
and creates another string argument.

One work around, since I only need to delimit a single character, is to code `[Ff]ormula[s]?` instead of `[Ff]ormula(s)?`. However, 
some Given-statements might require a more tedious work around, e.g., `@Given facilit(y|ies)`, where we wish to follow the Given-statement with a doc string. 
The only general solution that I see is to define two separate step definitions:
    
    @Given("^facility$")
    public void facility(String facilityLine) {
      facilities(facilityLine);
    }

    @Given("^facilities$")
    public void facilities(String facilityLines) {
      // Write code here that turns the phrase above into concrete actions
      throw new io.cucumber.java.PendingException();
    }

(Oh, and here is an even simpler work-around for the optional trailing `s`. Because regular expressions work on a character-by-character basis, 
I don't need to delimit the trailing `s` at all; in `Given [Ff]ormulas?` the question mark only makes the character immediately preceding it optional, 
without the need for a capture group. You might think that someone who's been coding for over 50 years should have seen that right away; my only defense
is that lately I have been using regular expression notation mainly with ANTLR, where the `?` applies to the preceding _token_, not the preceding _character_.)


