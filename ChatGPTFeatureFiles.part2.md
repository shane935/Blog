# Generating integration tests with ChatGPT

This is part two of a series for the first part look [here](BDD.part1.md)

Previously, I suggested using feature files to provide ChatGPT with enough context to create test suites for BDD. Let's have a look at the most successful approach I have found for generating effective integration tests.

To begin, we need to provide ChatGPT with a detailed prompt that sets the context and asks it to adopt a specific persona. It's important to include key information: what language we are using, what style we would like it to use, what supplementary files we have, and most importantly, the feature file we are using to generate the integration tests.

Here is an example prompt:

> For the duration of this chat please act like we are pair coding and you have been asked to write a series of integration tests based on gherkin feature files.  The tests are written in a functional style using typescript, cucumber, selenium and chai.
>
> We have a constants file where we store all the constants called constants.js please put all constants in that, so far it contains the following code
>
> ```typescript
> export const URL = "http://localhost:3000/";
> ```
>
> We also have a test setup file setup.js it contains the following code
>
> ```typescript
> import { Builder } from "selenium-webdriver";
>
> export const driver = new Builder().forBrowser("safari").build();
> ```
>
> the first feature file we are working on is
>
> ```gherkin
> @product @product-list
> Feature: Product List
>   As a small business owner placing an order
>   I want to view and navigate the product list
>   So that I can see all available products and their details
> 
>   Background:
>     Given the user navigates to the product page
> 
>   Scenario: User can view the product page
>     Then the user is presented with a paginated list of all available products
>     And each of the products has a name, a volume and a cost
> 
>   Scenario: User can view product details
>     When the user clicks on a product in the list
>     Then they are redirected to the details page for that product
> 
>   Scenario: User sees the right pagination arrow if there is a next page
>     Given the user is on page 1 of the product list
>     Then the right pagination arrow is visible
>
>   Scenario: User sees the left pagination arrow if there is a previous page
>     Given the user is on page 2 of the product list
>     Then the left pagination arrow is visible
>
>   Scenario Outline: User can navigate the paginated list
>     Given the user is on page <current_page> of the product list
>     When the user clicks the <arrow> pagination arrow
>     Then the user is moved to page <expected_page> of the product list
>
>     Examples:
>       | arrow | current_page | expected_page |
>       | left  | 2            | 1             |
>       | right | 1            | 2             |
>```
>

The first response it generates includes a supplementary file that provides functions used to access various elements on the page. Personally, I don't like this pattern, but you can see it [here](InitialResponse.md)

It always requires a couple of iterations back and forth to write the code with the style and structure you would like.

Here is the follow-up prompt I used:

> Could you please a make few changes
>
> Remove pageObjects.ts and in future don't use that pattern in this conversation
> Use the constants file  
> The URL variable belongs in the constants file  
> Can you move the css selectors out of the file and into constants.js
>

The final result looks like this:

constants.ts

```typescript
export const URL = "http://localhost:3000";
export const SELECTORS = {
  PRODUCT_ITEM: ".product-item",
  PRODUCT_NAME: ".product-name",
  PRODUCT_VOLUME: ".product-volume",
  PRODUCT_COST: ".product-cost",
  PAGE_NUMBER: ".page-number",
  LEFT_ARROW: ".left-arrow",
  RIGHT_ARROW: ".right-arrow"
};

```

product-list.test.ts

```typescript
import { Given, Then, When } from 'cucumber';
import { expect } from 'chai';
import { By } from 'selenium-webdriver';
import { driver } from './setup';
import { URL, SELECTORS } from './constants';

Given('the user navigates to the product page', async function () {
  await driver.get(URL);
});

Then('the user is presented with a paginated list of all available products', async function () {
  const productList = await driver.findElements(By.css(SELECTORS.PRODUCT_ITEM));
  expect(productList).to.not.be.empty;
});

Then('each of the products has a name, a volume and a cost', async function () {
  const productList = await driver.findElements(By.css(SELECTORS.PRODUCT_ITEM));
  for (const product of productList) {
    const name = await product.findElement(By.css(SELECTORS.PRODUCT_NAME)).getText();
    const volume = await product.findElement(By.css(SELECTORS.PRODUCT_VOLUME)).getText();
    const cost = await product.findElement(By.css(SELECTORS.PRODUCT_COST)).getText();

    expect(name).to.not.be.empty;
    expect(volume).to.not.be.empty;
    expect(cost).to.not.be.empty;
  }
});

When('the user clicks on a product in the list', async function () {
  const productList = await driver.findElements(By.css(SELECTORS.PRODUCT_ITEM));
  if (productList.length > 0) {
    await productList[0].click(); 
  }
});

Then('they are redirected to the details page for that product', async function () {
  const currentUrl = await driver.getCurrentUrl();
  expect(currentUrl).to.contain('/product/'); 
});

Given('the user is on page {int} of the product list', async function (pageNumber: string) {
});

Then('the {string} pagination arrow is visible', async function (arrowDirection: string) {
  const arrow = await driver.findElement(By.css(SELECTORS[`${arrowDirection.toUpperCase()}_ARROW`]));
  expect(await arrow.isDisplayed()).to.be.true;
});

When('the user clicks the {string} pagination arrow', async function (arrowDirection: string) {
  const arrow = await driver.findElement(By.css(SELECTORS[`${arrowDirection.toUpperCase()}_ARROW`]));
  await arrow.click();
});

Then('the user is moved to page {string} of the product list', async function (expectedPage: string) {
  const currentPage = await driver.findElement(By.css(SELECTORS.PAGE_NUMBER)).getText();
  expect(currentPage).to.equal(expectedPage);
});

```

This is a strong starting point for a BDD test suite. It has added in cucumber expressions {string} and {int} to generalize the statements and has handled the generic arguments in the last scenario outline quite well. You will note that one of the functions has no code in it. If prompted, ChatGPT will provide this, for best results, you will need to provide it with details of how your site performs pagination.

As an example, the prompt I used was:

> Could you write the test for Given('the user is on page {int} of the product list' the url for the product list page is "localhost:3000/product-list/1

And its response was:

```typescript
Given('the user is on page {int} of the product list', async function (pageNumber: number) {
  await driver.get(`${URL}product-list/${pageNumber}`);
});
```

As you develop, it is inevitable that you will need to make changes to the test suite as you make decisions. In part three, we will look at refactoring our test suite using ChatGPT to improve test readability and adapt to page updates.
