<div align="center">
  <img src="./.github/logo.png" width="200px">
  <h1>Vue Paycard</h1>
</div>
<p align="center">
  Credit card component made with Vue.js (works with Vue 2 & 3)
</p>

[![Version](https://img.shields.io/npm/v/@pablinho/vue-paycard.svg)](https://www.npmjs.com/package/@pablinho/vue-paycard)
[![GithubActions](https://github.com/guastallaigor/@pablinho/vue-paycard/workflows/@pablinho/vue-paycard/badge.svg)](https://github.com/guastallaigor/@pablinho/vue-paycard/actions)
[![Coverage Status](https://coveralls.io/repos/github/guastallaigor/@pablinho/vue-paycard/badge.svg?branch=master)](https://coveralls.io/github/guastallaigor/@pablinho/vue-paycard?branch=master)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com/)
[![Downloads](https://img.shields.io/npm/dt/@pablinho/vue-paycard.svg)](https://www.npmjs.com/package/@pablinho/vue-paycard)
[![Netlify Status](https://api.netlify.com/api/v1/badges/a87b7690-b534-4c67-b4a6-60e586bd79b5/deploy-status)](https://app.netlify.com/sites/@pablinho/vue-paycard/deploys)

> This component is based on <a href="https://github.com/muhammederdem/vue-interactive-paycard" target="_blank">Vue Interactive Paycard</a>. All the credits for the component (idea, design, images, core code) goes to it </br></br>
> This project only exports the Card component, **you** will need to create your own form

## Reason

Fork of the original `vue-paycard` to just use Visa and Mastercard, modified so it can be used in Vue3 and added isCardFlipped as a prop so it can be controlled from outside the component.

## How to install

### npm

```bash
$ npm install @pablinho/vue-paycard --save
```

### yarn

```bash
$ yarn add @pablinho/vue-paycard
```

## Quick start

### Vue.js

You can import in your `main.js` file

```js
import Vue from "vue";
import VuePaycard from "@pablinho/vue-paycard";

Vue.use(VuePaycard);
```

For Vue 3

```js
import { createApp, h } from "vue";
import VuePaycard from "@pablinho/vue-paycard";

const app = createApp({
  render: () => h(App),
});

app.use(VuePaycard);

app.mount("#app");
```

Or locally in any component

```js
import { VuePaycard } from "@pablinho/vue-paycard";
// In v0.5+ you don't need the brackets above

export default {
  components: {
    VuePaycard,
  },
};
```

### Nuxt.js

You can create a Nuxt.js plugin `@pablinho/vue-paycard.js`

```js
import Vue from "vue";
import VuePaycard from "@pablinho/vue-paycard";

Vue.use(VuePaycard);
```

and then import it in your `nuxt.config.js` file

```js
plugins: [{ src: "~/plugins/@pablinho/vue-paycard.js", mode: "client" }];
```

## <a name="usage">Basic usage</a>

```html
<template>
  <@pablinho/vue-paycard :value-fields="valueFields" />
</template>

<script>
  export default {
    data: () => ({
      valueFields: {
        cardName: "",
        cardNumber: "",
        cardMonth: "",
        cardYear: "",
        cardCvv: "",
      },
    }),
  };
</script>
```

## Props

| Property name          | Type             | Default                                                                                                                             | Description                                                                                                                                                                                                                                                                                                                                                                          |
| ---------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| value-fields\*         | Object           | null                                                                                                                                | A **required** object that let you set the credit card holders name, number, month, year and cvv. Note that is **required** that all the attributes name must be exactly as the example above (see [Basic usage](#usage)). The `cardNumber` attribute value **must** be stored as it's shown: **#### #### #### ####**. The `cardYear` attribute value **must** be stored as **YYYY** |
| input-fields           | Object           | { cardNumber: 'v-card-number', cardName: 'v-card-name', cardMonth: 'v-card-month', cardYear: 'v-card-year', cardCvv: 'v-card-cvv' } | An object that contains all your input fields id from your form. Each input field must have a valid and unique id to bind focus/blur listeners that this component provides. Note that is **required** that all the attributes name must be exactly as the ones in "Default". You need to also add a **data-card-field** attribute for each of those inputs                          |
| labels                 | Object           | { cardName: 'Full Name', cardHolder: 'Card Holder', cardMonth: 'MM', cardYear: 'YY', cardExpires: 'Expires', cardCvv: 'CVV' }       | Set custom labels for the card if needed. English by default. Note that is **required** that all the attributes name must be exactly as the ones in "Default"                                                                                                                                                                                                                        |
| is-card-number-masked  | Boolean          | true                                                                                                                                | Hides the numbers provided and changes to "\*". Only shows the last four digits                                                                                                                                                                                                                                                                                                      |
| has-random-backgrounds | Boolean          | true                                                                                                                                | Set a random background image to the card. You can check all the images in `src/assets/images`                                                                                                                                                                                                                                                                                       |
| background-image       | [String, Number] | ''                                                                                                                                  | Set a background image link to the card (overrides `has-random-backgrounds` prop), or you can pass a single valid number that matches the images name we have in `src/assets/images`                                                                                                                                                                                                 |
| set-type               | String           | ''                                                                                                                                  | Set a card type from the supported card types ['visaelectron', 'visa', 'elo', 'amex', 'mastercard', 'discover', 'unionpay', 'troy', 'dinersclub', 'jcb', 'laser', 'dankort', 'uatp', 'mir', 'hipercard', 'aura', 'maestro']. Note that this will override the type generated from card number                                                                                        |
| isCardFlipped               | Boolean           | false                                                                                                                                  | Controls if the card is flipped     

## Events

| Event name | Type   | Default | Description                               |
| ---------- | ------ | ------- | ----------------------------------------- |
| get-type   | String | null    | Emits the type generated from card number |

## Supported card types

- MasterCard
- Visa

## Development

[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/guastallaigor/@pablinho/vue-paycard/issues)

> **Note**: Contributions are very welcomed, however is very important to open a new issue using the issue template **before** you start working on anything, so we can discuss it before hand

Fork the project and enter this commands in your terminal

```sh
$ git clone https://github.com/YOUR_GITHUB_USERNAME/@pablinho/vue-paycard.git
$ cd @pablinho/vue-paycard
$ yarn
```

### Storybook

For visual testing, this project contains storybook which you can run by running the command

```sh
$ yarn storybook
```

### Jest

Before making the PR, if you changed something that needs to be tested, please make the tests inside the `tests/unit` folder

To run the tests, you can use the command

```sh
$ yarn test:watch
```

### CSS

All the CSS is at `src/assets/css/style.css`

If you make any changes in that file, you will need to run `yarn build` to build it, because the component uses the minified version at `src/assets/css/style.min.css`

### Commitlint

This project follows the [commitlint](https://github.com/conventional-changelog/commitlint) guidelines, with minor changes

You can commit using `yarn commit` to help you with that

There's a `pre-push` hook that runs all the unit tests before you can push it

If an error occurs, you can use the `yarn commit:retry` command that runs the previous `yarn commit` that you already filled

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/C1C63QCB8)

## License

[MIT](https://github.com/guastallaigor/@pablinho/vue-paycard/blob/master/LICENSE) © 2020
