# Using ChatGPT to generate integration tests

The way I have had the most success generating tests with ChatGPT is to provide an initial prompt that sets the context and asks ChatGPT to adopt a specific persona.  Its important to include key information in this initial prompt.  Here is the initial prompt I used.

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
>     Given the user is on the first page of the product list
>     Then the right pagination arrow is visible
>
>   Scenario: User sees the left pagination arrow if there is a previous page
>     Given the user is on the second page of the product list
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

A couple of key things to note about this prompt it details the languages we are using what style of programming we would like to see, it includes any supplementary files that we need ChatGPT to use as well as providing the feature file we would like to use to generate the tests.

The first response it generates includes a supplementary file that provides functions used to access various elements on the page. Personally I don't like this pattern but you can see it [here](InitialResponse.md)

In general it requires a couple of iterations to it to write the code in the style you would like



in addition I asked it to make a few amendments to the code, specifically:

> A few changes
>
> Please use the constants file
> The URL variable belongs in the constants file
> Can you move the dogs selectors out of the file and into constants.js

The final result looks like this

constants.ts

```typescript
export const URL = "http://localhost:3000/";
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

Given('the user is on the {string} page of the product list', async function (pageNumber: string) {
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

In general it is a pretty decent starting point for a BDD test suite from this point we have two options as to how to proceed, we can continue to use ChatGPT to expand our test suite if we need to add more statements into it, doing this requires keeping it up to date if we make changes to the files.  

for example if we wish to ensure that the product page only has twenty max entries on it we may ask ChatGPT:

> Can you update the tests to reflect this change to the feature file
>
>```gherkin
> Scenario: User can view the product page
>  Then the user is presented with a paginated list of all available products
>  And there are no more then 20 products on the page
>  And each of the products has a name, a volume and a cost
>
>```
>

to which it will provide:

```typescript
Then('there are no more then 20 products on the page', async function () {
  const products = await driver.findElements(By.css(PRODUCT_NAME));
  expect(products.length).to.be.lessThanOrEqual(20);
});
```

add in some detail about expanding the test suite after pros and cons of leaving chat gpt behind