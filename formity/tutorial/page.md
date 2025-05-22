---
title: Tutorial
nextjs:
  metadata:
    title: Tutorial - Docs
    description: Follow this tutorial to grasp the core concepts of Formity and how it has to be used.
---

Follow this tutorial to grasp the core concepts of Formity and how it has to be used.

---

## Initial steps{% id="initial-steps" %}

In this tutorial we will show you how to create a basic form. For that, you will need to clone the following GitHub repository so that you don't need to start from scratch.

```bash
git clone https://github.com/martiserra99/formity-previous-tutorial
```

Make sure you run the following command to install all the dependencies:

```bash
npm install
```

This tutorial explains how to use Formity with TypeScript, but if you want to learn how to use it with JavaScript you can still follow this tutorial since almost everything is the same. The only thing that is different is that in JavaScript you don't define the types.

## Components{% id="components" %}

When creating a form, the first thing that needs to be done is to create the components that we will use. After creating these components, we will be able to reference them in the schema that defines the form.

To understand how to create them, it is helpful to first know how they need to be referenced. Here is an example:

```json
{
  "form": {
    "step": "$step",
    "defaultValues": "$defaultValues",
    "resolver": "$resolver",
    "onNext": "$onNext",
    "children": {
      // ...
    }
  }
}
```

We have an object with a key that is the name of the component, and all the properties of the object are the parameters used to configure the component.

The values of the properties can be components as well:

```json
{
  "form": {
    "step": "$step",
    "defaultValues": "$defaultValues",
    "resolver": "$resolver",
    "onNext": "$onNext",
    "children": {
      "formLayout": {
        "heading": "Tell us about yourself",
        "description": "We would want to know about you",
        "fields": [
          {
            "textField": {
              "name": "name",
              "label": "Name"
            }
          },
          {
            "numberField": {
              "name": "age",
              "label": "Age"
            }
          }
        ],
        "button": {
          "next": { "text": "Next" }
        }
      }
    }
  }
}
```

These components need to be created so that we can reference them in the schema. For that, we will create a `components.tsx` file with the following content:

```tsx
// src/components.tsx

import { Fragment } from "react";
import {
  Components,
  Step,
  DefaultValues,
  Resolver,
  OnNext,
  OnBack,
} from "formity";
import { Value } from "expry";

import Form from "@/components/form";
import FormLayout from "@/components/form-layout";
import Next from "@/components/navigation/next";
import Back from "@/components/navigation/back";
import TextField from "@/components/react-hook-form/text-field";
import NumberField from "@/components/react-hook-form/number-field";
import YesNo from "@/components/react-hook-form/yes-no";

type Parameters = {
  form: {
    step: Step;
    defaultValues: DefaultValues;
    resolver: Resolver;
    onNext: OnNext;
    children: Value;
  };
  formLayout: {
    heading: string;
    description: string;
    fields: Value[];
    button: Value;
    back?: Value;
  };
  next: {
    text: string;
  };
  back: {
    onBack: OnBack;
  };
  textField: {
    name: string;
    label: string;
  };
  numberField: {
    name: string;
    label: string;
  };
  yesNo: {
    name: string;
    label: string;
  };
};

const components: Components<Parameters> = {
  form: ({ step, defaultValues, resolver, onNext, children }, render) => (
    <Form
      key={step}
      defaultValues={defaultValues}
      resolver={resolver}
      onNext={onNext}
    >
      {render(children)}
    </Form>
  ),
  formLayout: ({ heading, description, fields, button, back }, render) => (
    <FormLayout
      heading={heading}
      description={description}
      fields={fields.map((field, index) => (
        <Fragment key={index}>{render(field)}</Fragment>
      ))}
      button={render(button)}
      back={back ? render(back) : undefined}
    />
  ),
  next: ({ text }) => <Next>{text}</Next>,
  back: ({ onBack }) => <Back onBack={onBack} />,
  textField: ({ name, label }) => <TextField name={name} label={label} />,
  numberField: ({ name, label }) => <NumberField name={name} label={label} />,
  yesNo: ({ name, label }) => <YesNo name={name} label={label} />,
};

export default components;
```

In this file we have created a `components` object. This object has a property for each component, and each of these properties is a function that receives some parameters and renders the component with some props.

We also have a second parameter called `render`. This parameter is a function that receives a value of type `Value`, which in this case corresponds to the reference of a component, and is responsible for rendering the component.

### Parameters{% id="parameters" %}

As you may have noticed, the `components` object is of type `Components`, and it receives a type called `Parameters`. This type defines the parameters that must be used for each component.

### Variables{% id="variables" %}

If you look closer at how the components are referenced in the schema, you will see that some values are strings that start with `$`.

```json
{
  "form": {
    "step": "$step",
    "defaultValues": "$defaultValues",
    "resolver": "$resolver",
    "onNext": "$onNext",
    "children": {
      // ...
    }
  }
}
```

These values are variables, and there are some predefined variables that can be used whenever we reference the components in the schema. Here are the main ones:

- `step`: Number that defines the current step.
- `defaultValues`: Default values that we need to pass to the `useForm` hook.
- `resolver`: Validation rules that we need to pass to the `useForm` hook.
- `onNext`: Function that we need to call to go to the next step.
- `onBack`: Function that we need to call to go to the previous step.

### Step{% id="step" %}

Whenever we go to the next step, if the rendered components are the same, the state of the form will remain unchanged. To reset the state of the components, we can pass the step variable as the value to the `key` prop of the form.

```tsx
// ...

const components: Components<Parameters> = {
  form: ({ step, defaultValues, resolver, onNext, children }, render) => (
    <Form
      key={step}
      defaultValues={defaultValues}
      resolver={resolver}
      onNext={onNext}
    >
      {render(children)}
    </Form>
  ),
  // ...
};

// ...
```

## Schema{% id="schema" %}

We can now create the schema that defines the form. The schema is a JSON containing elements of different types. By using these elements in combination, we can create any form we want in a very simple way.

We will create a `schema.ts` file with the following content:

```ts
// src/schema.ts

import { Schema } from "formity";

const schema: Schema = [
  {
    form: {
      defaultValues: {
        name: ["", []],
        age: [18, []],
      },
      resolver: {
        name: [
          [{ "#$ne": ["#$name", ""] }, "Required"],
          [{ "#$lt": [{ "#$strLen": "#$name" }, 20] }, "No more than 20 chars"],
        ],
      },
      render: {
        form: {
          step: "$step",
          defaultValues: "$defaultValues",
          resolver: "$resolver",
          onNext: "$onNext",
          children: {
            formLayout: {
              heading: "Tell us about yourself",
              description: "We would want to know about you",
              fields: [
                {
                  textField: {
                    name: "name",
                    label: "Name",
                  },
                },
                {
                  numberField: {
                    name: "age",
                    label: "Age",
                  },
                },
              ],
              button: {
                next: { text: "Next" },
              },
            },
          },
        },
      },
    },
  },
  {
    cond: {
      if: { $gte: ["$age", 18] },
      then: [
        {
          form: {
            defaultValues: {
              drive: [true, []],
            },
            resolver: {},
            render: {
              form: {
                step: "$step",
                defaultValues: "$defaultValues",
                resolver: "$resolver",
                onNext: "$onNext",
                children: {
                  formLayout: {
                    heading: "Can you drive?",
                    description: "We would want to know if you can drive",
                    fields: [
                      {
                        yesNo: {
                          name: "drive",
                          label: "Drive",
                        },
                      },
                    ],
                    button: {
                      next: { text: "Next" },
                    },
                    back: {
                      back: { onBack: "$onBack" },
                    },
                  },
                },
              },
            },
          },
        },
      ],
      else: [{ variables: { drive: false } }],
    },
  },
  {
    return: {
      name: "$name",
      age: "$age",
      drive: "$drive",
    },
  },
];

export default schema;
```

We are using the **form**, **condition**, **variables** and **return** element to create this form. If you look closely, you will see that some properties start with `$`. These are operators from the [Expry](https://expry.dev) package, and they let us do all kinds of operations to achieve anything we want.

```tsx
// ...

const schema: Schema = [
  // ...
  {
    cond: {
      if: { $gte: ["$age", 18] },
      then: ["..."],
      else: ["..."],
    },
  },
  // ...
];

// ...
```

## Formity{% id="formity" %}

The last thing we need to do is use the `Formity` component. In this component, we have to pass the components, the schema, and a function that is called when the form is submitted. To do this, we will write the following code in `app/page.tsx`:

```tsx
// src/app/page.tsx

"use client";

import { useState } from "react";
import { Formity, Value } from "formity";

import components from "@/components";
import schema from "@/schema";

import Data from "@/data";

export default function Home() {
  const [result, setResult] = useState<Value | null>(null);

  function handleReturn(result: Value) {
    setResult(result);
  }

  if (result) {
    return <Data data={result} onStart={() => setResult(null)} />;
  }

  return (
    <Formity components={components} schema={schema} onReturn={handleReturn} />
  );
}
```
