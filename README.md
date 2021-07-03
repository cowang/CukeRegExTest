# CukeRegExTest

## An error when using a regular expression in the step definition.

### OR NOT, as it turns out

The error below is that I was using parentheses to create a delimited optional string, which works fine, but parentheses also mark off a _capture group_, which Cucumber uses to mark the elements to capture. If there is no capture group, then Cucumber expects to see a doc string below the `Given` expression. Since I have a capture group, namely `(s)?` in my regular expression, Cucumber expects a single string for the argument and no doc string. Apparantly when I code `Given formula`, Cucumber sees no `s` at the end and creates an empty string argument, then it sees the doc string and creates another string argument.

The work around, since I only need to delimit a single character, is to code `[Ff]ormula[s]?` instead of `[Ff]ormula(s)?`. However, some Given-statements might require a more tedious work around, e.g., `Given facilit(y|ies)`, where we wish to follow the Given-statement with a doc string. The only general solution that I see is to define two separate step definitions:
    
    @Given("^facility$")
    public void facility(String facilityLine) {
      facilities(facilityLine);
    }

    @Given("^facilities$")
    public void facilities(String facilityLines) {
      // Write code here that turns the phrase above into concrete actions
      throw new io.cucumber.java.PendingException();
    }


### Here are the original, misguided, error notes

Cucumber expects extra arguments from the Gherkin code for some legal forms of regular expressions in the step definition.

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

I have at least one work-around, but this error may reveal a bigger problem, so I want to report it.

One thing to check for the cause of the error is that the Cucumber expression use of parentheses somehow inteferes with the regular expression parsing.
