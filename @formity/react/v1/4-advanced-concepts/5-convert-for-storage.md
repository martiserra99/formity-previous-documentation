---
title: Convert for storage
nextjs:
  metadata:
    title: Convert for storage - Docs
    description: Learn how to convert your forms to JSON for efficient storage.
---

Learn how to convert your forms to JSON for efficient storage.

---

## Overview{% id="overview" %}

We may face the challenge of having to store forms in databases with a structured format. JSON would be a reliable choice, as it is widely supported and easy to work with.

Since Formity forms are defined with JavaScript, they need to have a corresponding JSON representation that can be converted back into Formity code.

This is where [Expry](https://www.expry.dev/) steps in, a package used to map JSON to JavaScript. It allows custom mappings, provides built-in operators, and offers first-class support for Formity.

## Expry{% id="expry" %}

Expry allows you to define logic using a simple JSON syntax and convert it into JavaScript code. You can either define your own mappings from JSON to JavaScript or take advantage of the built-in operators that we provide.

To install the core package you have to run the following command.

```bash
npm install @expry/system
```

Then, you can start using it as you can see here.

```ts
import { createExpry, Operations } from "@expry/system";

type Operations = {
  map: {
    params: { input: unknown; as: unknown; in: unknown };
    return: unknown[];
  };
  add: {
    params: unknown[];
    return: unknown;
  };
};

const operations: Operations<Operations> = {
  map(args, vars, expry) {
    const array = expry(args.input, vars) as unknown[];
    const as = expry(args.as, vars) as string;
    return array.map((value) => {
      return expry(args.in, { ...vars, [`$${as}`]: value });
    });
  },
  add: (args, vars, expry) => {
    const array = args.map((num) => expry(num, vars)) as number[];
    return array.reduce((acc, val) => acc + val, 0);
  },
};

const expry = createExpry<[Operations]>(operations);

const expression: unknown = {
  $map: {
    input: [1, 2, 3],
    as: "num",
    in: { $add: ["$$num", 1] },
  },
};

const result = expry(expression);

console.log(result); // [2, 3, 4]
```

If you take a closer look, you will see that the way it works is by defining operators that map JSON to JavaScript code. In this case we have created our own operators, but we can also install additional packages to have access to some built-in operators.

You can install the following package to have access to some basic operators.

```bash
npm install @expry/basic
```

The operators from this package can be provided as you can see here.

```ts
import { createExpry } from "@expry/system";

import { basicOperations, BasicOperations } from "@expry/basic";

const expry = createExpry<[BasicOperations]>(basicOperations);
```

Moreover, you can install the following package to have access to Formity operators.

```bash
npm install @expry/formity
```

The operators from this package can be provided as you can see here.

```ts
import { createExpry } from "@expry/system";
import { basicOperations, BasicOperations } from "@expry/basic";
import { formityOperations, FormityOperations } from "@expry/formity";

type Operations = [BasicOperations, FormityOperations];

export const expry = createExpry<Operations>(
  basicOperations,
  formityOperations,
);
```

If you want to take a look at a codebase with a form defined with Expry, we strongly suggest you to navigate to the following folder in the `formity-prev-docs-code` repository. Clone it first if you haven't already.

```bash
git clone https://github.com/martiserra99/formity-prev-docs-code
cd formity-prev-docs-code/@formity/react/v1/expry-formity
```
