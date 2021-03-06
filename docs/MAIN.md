
#React shopping cart with _localization_ and _multiple currencies_

Shopping cart package provides several components:
- __Cart__
- __Product__
- __CheckoutButton__

which can be used separately or in union.
By default [__Redux__](https://github.com/reactjs/redux) is the framework to operate data.

So, it's your choice to use Redux or not, but its reducers, actions and actionTypes are already included.

Pay attention! All components are  [__Pure__](https://facebook.github.io/react/docs/react-api.html#react.purecomponent).

**Meta**

-   **author**: Oleg Nosov &lt;olegnosov1@gmail.com>
-   **license**: MIT

##Demo

Production demo: [`https://www.solarleague.org/shop/macbook-case/`](https://www.solarleague.org/shop/macbook-case/)

[`Latest version demo (example1)`](https://olegnn.github.io/)

**Usage**
```shell
npm i --save react-shopping-cart
```

**Examples**


In all cases you must include bootstrap_v4.css in your project
```javascript
import 'bootstrap/dist/css/bootstrap.css';
```
And if you want to see animation, also include animate.css
```javascript
import 'animate.css/animate.min.css';
```

__With Redux.__ After store initialization you must dispatch setCartCurrency action or 'USD' will be used as cart's currency.
```javascript
import React, { Component } from 'react';
import { Provider } from 'react-redux';
import { createStore, combineReducers } from 'redux';
import {
  Cart,
  Product,
  CheckoutButton,
  cartLocalization,
  cartReducer,
  setCartCurrency,
} from 'react-shopping-cart';


import 'bootstrap/dist/css/bootstrap.css';
import 'animate.css/animate.min.css';

const { getDefaultLocalization } = cartLocalization;

// You may take localization object from wherever you want, that's just an example
// For more information, see localization section
const iPadCaseLocalization = {
  colour: 'Colour',
  iPadCase: 'iPad case',
  red: 'Red',
  green: 'Green',
  yellow: 'Yellow',
  GBP: '£',
  EUR: '€',
  USD: '$',
};

const iPadPropertiesWithAdditionalCostLocalization = {
  yellow: 'Yellow (+{cost}{localizedCurrency})',
};

const store = createStore(
  combineReducers(
    {
      cart: cartReducer,
      // Your own reducers, sir
    }
  )
);

store.dispatch(
  setCartCurrency('USD'),
);


class App extends Component {

  state = {
    product: {
      name: 'iPadCase',
      id: 'ipad-case',
      path: '/shop/ipad-case/',
      properties: {
        colour: ['red', 'green', {
          additionalCost: {
            GBP: 1,
            EUR: 2,
            USD: 3.50,
          },
          value: 'yellow',
        }],
      },
      propertiesToShowInCart: ['colour'],
      prices: { GBP: 70, EUR: 80, USD: 90 },
      currency: 'GBP',
      imagePath: '1-483x321.jpeg',
    },
    getProductLocalization:
      getDefaultLocalization(
        'product',
        'en',
        {
          ...iPadCaseLocalization,
          ...iPadPropertiesWithAdditionalCostLocalization
        }
      ),
    getCheckoutButtonLocalization:
      getDefaultLocalization(
        'checkoutButton',
        'en',
        iPadCaseLocalization,
      ),
    getCartLocalization:
      getDefaultLocalization(
        'cart',
        'en',
        iPadCaseLocalization
      )
  };

  render() {

    const {
      product,
      getCheckoutButtonLocalization,
      getProductLocalization,
      getCartLocalization,
    } = this.state;

    const checkoutButtonElement =
      <CheckoutButton
        getLocalization={
          getCheckoutButtonLocalization
        }
        checkoutURL="/to/my/checkout"
      />;
    return (
      <Provider store={store}>
        <div className="container">
          <Product
            {...product}
            checkoutButton={checkoutButtonElement}
            getLocalization={
              getProductLocalization
            }

          />
          <Cart
            checkoutButton={checkoutButtonElement}
            getLocalization={
              getCartLocalization
            }
          />
        </div>
      </Provider>
    );
  }
}

export default App;


// You also may import actions and actionTypes

import { cartActions, cartActionTypes } from 'react-shopping-cart';

// And do some cool things with them
```

__Without redux__
```javascript
import React, { Component } from 'react';
import {
  CartComponent,
  ProductComponent,
  CheckoutButtonComponent,
  cartLocalization,
} from 'react-shopping-cart';


import 'bootstrap/dist/css/bootstrap.css';
import 'animate.css/animate.min.css';

const { getDefaultLocalization } = cartLocalization;

// You may take localization object from wherever you want, that's just an example
// For more information, see localization section
const iPadCaseLocalization = {
  colour: 'Colour',
  iPadCase: 'iPad case',
  red: 'Red',
  green: 'Green',
  yellow: 'Yellow',
  GBP: '£',
  EUR: '€',
  USD: '$',
};

const iPadPropertiesWithAdditionalCostLocalization = {
  yellow: 'Yellow (+{cost}{localizedCurrency})',
};

class App extends Component {

  state = {
    products: {},
    product: {
      name: 'iPadCase',
      id: 'ipad-case',
      path: '/shop/ipad-case/',
      properties: {
        colour: ['red', 'green', {
          additionalCost: {
            GBP: 1,
            EUR: 2,
            USD: 3.50,
          },
          value: 'yellow',
        }],
      },
      propertiesToShowInCart: ['colour'],
      prices: { GBP: 70, EUR: 80, USD: 90 },
      currency: 'GBP',
      imagePath: '1-483x321.jpeg',
    },
    getProductLocalization:
      getDefaultLocalization(
        'product',
        'en',
        {
          ...iPadCaseLocalization,
          ...iPadPropertiesWithAdditionalCostLocalization
        }
      ),
    getCheckoutButtonLocalization:
      getDefaultLocalization(
        'checkoutButton',
        'en',
        iPadCaseLocalization,
      ),
    getCartLocalization:
      getDefaultLocalization(
        'cart',
        'en',
        iPadCaseLocalization
      )
  };

  addProduct = (key, product, currency) =>
    void this.setState(
      (
        { products:
          {
            [key]: cartProduct = { quantity: 0 },
            ...restOfProducts
          }
        }
      ) => ({
        products: {
          ...restOfProducts,
          [key]: {
            ...product,
            quantity:
              product.quantity +
              cartProduct.quantity,
          }
        }
      })
    );

  generateProductKey = (id, properties) =>
    `${id}/${Object.entries(properties).join('_')}`;

  updateProduct = (key, updatedProduct) => void console.log(':)');

  removeProduct = key => void console.log(':C');

  render() {

    const {
      addProduct,
      generateProductKey,
      updateProduct,
      removeProduct,
      state,
    } = this;

    const {
      getProductLocalization,
      getCheckoutButtonLocalization,
      getCartLocalization,
      products,
      product,
    } = state;

    const checkoutButtonElement =
      <CheckoutButtonComponent
        grandTotal={500}
        hidden={false}
        checkoutURL="/to/my/checkout"
        currency="GBP"
        getLocalization={getCheckoutButtonLocalization}
      />;
    return (
      <div className="container">
        <ProductComponent
          {...product}
          checkoutButton={checkoutButtonElement}
          onAddProduct={
            addProduct
            // Help product to get into the cart
          }
          generateProductKey={
            generateProductKey
                    // create product key as you wish
          }
          getLocalization={getProductLocalization}
        />


        <CartComponent
          products={
            products
            // Provide your own product's Object(Look at ProductsMapType)
          }
          onUpdateProduct={
            updatedProduct
            // Update something
          }
          getLocalization={
            getCartLocalization
          }
          currency="GBP"
          onRemoveProduct={
            removeProduct
            // Remove something
          }
          checkoutButton={
            checkoutButtonElement
          }
          isCartEmpty={
            false
          }
          getLocalization={getCartLocalization}
        />
      </div>
    );
  }
}

export default App;
```
