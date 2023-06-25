# Experiments in AI development process

The goal of this series is to experiment with integrating AI into different areas of the development process in ways that are productive for companies and developers. This is an experimental areas so I'll be clearly marking which areas are coming with my own personal recommendation, which are speculation and what are the results of testing my speculation.

```<ConfidentRecommending>```

My idealized development process has behavior driven development (BDD) at it heart. Features should have a large number of integration tests and a smaller number of unit tests that are written before development begins. The  integration tests that are defined using [gherkin feature files](https://cucumber.io/docs/gherkin/reference/).  
The [cucumber test runner](https://cucumber.io/docs/installation/) should be used to execute your test files.

The primary advantages of structuring your projects this way is it creates a shared language across developers, testers, product and project managers on a project.  

* Allowing more visibility on the progress of features during development
  * Feature are complete when all the cucumber tests are passing
* a broader group that can understand what is happening if tests start failing in production
* Improved ability to diagnose problems and find someone to solve them

For a brief summary of how the cucumber test runner works, it takes gherkin statements (Given, When, Then, etc) and matches them against corresponding code blocks.  The code blocks are then executed by the cucumber test runner in the same order as the statements from the feature file.  A single block of code can be used by multiple gherkin statements across multiple feature files as long as the gherkin statement is exactly the same

```gherkin
Scenario: User can view the product page
    Given the user navigates to the product page
    Then the user is presented with a paginated list of all available products
    And each of the products has a name
```

```typescript
Given("the user navigates to the product page", async function () {
  await driver.get(localhost:3000);
});

Then(
  "the user is presented with a paginated list of all available products",
  async function () {
    const productList = await driver.findElement(By.css(".product-list"));
    expect(productList).to.exist;
  }
);

Then("each of the products has a name", async function () {
  const productNames = await driver.findElements(By.css(".product-name"));
  productNames.forEach(async (name) => {
    const nameText = await name.getText();
    expect(nameText).to.not.be.empty;
  });
});

```

```</ConfidentRecommending>```

```<Speculation>```

Feature files provide an excellent starting point to begin integrating AI into your development process, they represent a self contained feature with enough context and detail to create a meaningful set of integration tests without the need to provide an overwhelming amount of information to to create a successful result.

In part two ill be looking at how ways we can use ChatGPT to generate integration tests based on feature files

```</Speculation>```