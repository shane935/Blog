# Experiments in AI development process

The goal of this series is to experiment with integrating AI into different areas of the development process in ways that are productive for companies and developers. This is an experimental area so I'll be clearly marking which areas I confidently recommend, which are speculation and what are the results of testing my speculation.

```<ConfidentRecommending>```

My idealized development process has behavior driven development (BDD) at it heart. Features should have a large number of integration tests and a smaller number of unit tests that are written before development begins. The  integration tests are defined using [gherkin feature files](https://cucumber.io/docs/gherkin/reference/) and executed using the [cucumber test runner](https://cucumber.io/docs/installation/).

The primary advantages of structuring your projects this way is it creates a shared language across developers, testers, product and project managers on a project.  

Creating:

* More visibility on the progress of features during development
  * Features are considered complete when all the cucumber tests are passing
* Tests and test reports are readable to non-technical people
  * Creating improved ability to diagnose problems and find someone to solve them
* There is a clear link all the way through from the ticket to the development of the feature

For a brief summary of how the cucumber test runner works, it takes gherkin statements (Given, When, Then, etc) and matches them against corresponding code blocks.  The code blocks are then executed by the cucumber test runner in the same order as the statements from the feature file.  A single block of code can be used by multiple gherkin statements across multiple feature files as long as the gherkin statement is exactly the same.

```gherkin
Scenario: User can view the product page
    Given the user navigates to the product page
    Then the user is presented with a paginated list of all available products
    And each of the products has a name
```

```typescript
Given("the user navigates to the product page", async function () {
  await driver.get("localhost:3000");
});

Then(
  "the user is presented with a paginated list of all available products",
  async function () {
    const productList = await driver.findElement(By.css(".product-list"));
    expect(productList).to.exist;
  }
);

Then("each of the products has a name", async function () {
  const productNames = await driver.findElements(By.css(PRODUCT_NAME_SELECTOR));
  const names = await Promise.all(
    productNames.map(async (name) => name.getText())
  );
  const allNamesNotEmpty =
    names.every((nameText) => nameText !== "") && names.length > 0;
  expect(allNamesNotEmpty, "Not all products have a name").to.be.true;
});

});

```

```</ConfidentRecommending>```

```<Speculation>```

Feature files provide an excellent starting point to begin integrating AI into your development process, they represent a self contained feature with enough context and detail to create a meaningful set of integration tests without the need to provide an overwhelming amount of information to create a successful result.

```</Speculation>```

In part two of this series, I  will delve deeper into generating meaningful test suites using ChatGPT. We will look at how this can reduce the load of manual test writing and explore new possibilities for addressing edge cases.
