# Refactoring 

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

to which it will provide:

```typescript
Then('there are no more then 20 products on the page', async function () {
  const products = await driver.findElements(By.css(PRODUCT_NAME));
  expect(products.length).to.be.lessThanOrEqual(20);
});
```


* the details page requires a wait - can add that in using chat gpt
* refactor to include chai messages
* pros and cons of leaving chat gpt behind