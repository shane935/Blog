# Initial Response

Alright, let's start by defining the required page objects. Page objects help us to organize our code better and make it more readable and maintainable. It's a common pattern in Selenium tests.

Create a new file pageObjects.ts and add the following code:

```typescript
import { By, WebDriver } from 'selenium-webdriver';

export class ProductPage {
  constructor(private driver: WebDriver, private url: string) {}

  async navigateTo() {
    await this.driver.get(this.url);
  }

  async getProductList() {
    return await this.driver.findElements(By.className("product-item"));
  }

  async getPaginationArrow(direction: string) {
    return await this.driver.findElement(By.className(`${direction}-arrow`));
  }

  async getCurrentPageNumber() {
    return await this.driver.findElement(By.className("page-number")).getText();
  }

  async clickProduct(index: number) {
    const productList = await this.getProductList();
    if (productList.length > index) {
      await productList[index].click();
    }
  }
}
```

Next, let's define the step definitions based on the gherkin scenarios in a new file productListSteps.ts.

```typescript
import { Given, Then, When } from 'cucumber';
import { expect } from 'chai';
import { ProductPage } from './pageObjects';
import { driver, URL } from './setup';

const productPage = new ProductPage(driver, URL);

Given('the user navigates to the product page', async function () {
  await productPage.navigateTo();
});

Then('the user is presented with a paginated list of all available products', async function () {
  const productList = await productPage.getProductList();
  expect(productList).to.not.be.empty;
});

Then('each of the products has a name, a volume and a cost', async function () {
  const productList = await productPage.getProductList();
  for (const product of productList) {
    const name = await product.findElement(By.className("product-name")).getText();
    const volume = await product.findElement(By.className("product-volume")).getText();
    const cost = await product.findElement(By.className("product-cost")).getText();
    
    expect(name).to.not.be.empty;
    expect(volume).to.not.be.empty;
    expect(cost).to.not.be.empty;
  }
});

When('the user clicks on a product in the list', async function () {
  await productPage.clickProduct(0); // click the first product in the list
});

Then('they are redirected to the details page for that product', async function () {
  const currentUrl = await driver.getCurrentUrl();
  expect(currentUrl).to.contain('/product/'); // assuming the product details page URL contains '/product/'
});

Given('the user is on the {string} page of the product list', async function (pageNumber: string) {
  // we need to navigate to the page manually here. The implementation will depend on how your site's pagination works.
});

Then('the {string} pagination arrow is visible', async function (arrowDirection: string) {
  const arrow = await productPage.getPaginationArrow(arrowDirection);
  expect(await arrow.isDisplayed()).to.be.true;
});

When('the user clicks the {string} pagination arrow', async function (arrowDirection: string) {
  const arrow = await productPage.getPaginationArrow(arrowDirection);
  await arrow.click();
});

Then('the user is moved to page {string} of the product list', async function (expectedPage: string) {
  const currentPage = await productPage.getCurrentPageNumber();
  expect(currentPage).to.equal(expectedPage);
});
```

Please note that you'll need to adjust the selectors (i.e., By.className) according to your actual HTML structure and CSS classes.

Also, remember that these are just the base definitions. You might need to add some error handling and additional checks to make the tests more robust.