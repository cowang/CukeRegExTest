# CukeRegExTest

## An error when using a regular expression in the step definition.

Cucumber expects extra arguments from the Gherkin code for some legal forms of regular expressions in the step definition.

When the Cucumber regular expression in the step definition ends with ...(s)? and expects a doc string, such as

    @Given("^[Ff]ormula(s)?$")
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

Is it possible that the Cucumber expression use of parentheses is somehow embedded in the regular expression parsing.
