# Using ChatGPT to generate integration tests

For an introduction to this series look here

Previously I suggested using feature files to provide ChatGPT with enough context to create test suites for BDD. Lets have a look at the most successful approach I have found to generating effective integration tests.

We need to provide a very detailed initial prompt that sets the context and asks ChatGPT to adopt a specific persona.  Its important to include key information in this initial prompt.  Here is an example of a prompt i have used.

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

A couple of key things to note about this prompt it details the languages, we are using what style of programming we would like to see, it includes any supplementary files that we need ChatGPT to use as well as providing the feature file we would like to use to generate the tests.

The first response it generates includes a supplementary file that provides functions used to access various elements on the page. Personally I don't like this pattern but you can see it [here](InitialResponse.md)

It generally requires a couple of iterations back and forth to write the code in the style you would like, here is the prompt I used to make the changes

> Could you make few changes
>
> Please remove pageObjects.ts and in future don't use that pattern in this conversation
> Use the constants file  
> The URL variable belongs in the constants file  
> Can you move the css selectors out of the file and into constants.js
>

The final result looks like this

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
    await productList[0].click();  // click the first product in the list
  }
});

Then('they are redirected to the details page for that product', async function () {
  const currentUrl = await driver.getCurrentUrl();
  expect(currentUrl).to.contain('/product/'); // assuming the product details page URL contains '/product/'
});

Given('the user is on page {int} of the product list', async function (pageNumber: string) {
  // we need to navigate to the page manually here. The implementation will depend on how your site's pagination works.
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

In general it is a pretty decent starting point for a BDD test suite. It has added in cucumber expressions to generalize the statements and has handled the generic arguments in the last scenario outline well.  You will note that one of the functions has no code in it, if prompted chatGPT will provide this for best results you will need to provide it details of how your site performs pagination.

As an example the prompt I used

> Could you write the test for Given('the user is on the {string} page of the product list' the url for the product list page is "localhost:3000/product-list/1

and it's response

```typescript
Given('the user is on page {int} of the product list', async function (pageNumber: number) {
  await driver.get(`${URL}product-list/${pageNumber}`);
});
```

As you develop it is inevitable that you will need to make changes to the test suite as you make decisions, in part three we will look at refactoring our test suite using ChatGPT to improve test readability and adapt to page updates
