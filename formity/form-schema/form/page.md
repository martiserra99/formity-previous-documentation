---
title: Form
nextjs:
  metadata:
    title: Form - Docs
    description: Learn what the form element is about and how it is used in the schema.
---

Learn what the form element is about and how it is used in the schema.

---

## Usage{% id="form" %}

The **form** element is used to define a step in the form.

## Structure{% id="form" %}

It is defined using the following structure.

```json
{
  "form": {
    "defaultValues": "expression",
    "resolver": "expression",
    "render": "expression"
  }
}
```

### defaultValues{% id="default-values" %}

The `defaultValues` property defines the default values of the form.

Its value corresponds to an [Expry](https://expry.dev) expression and it has to evaluate to an object with key-value pairs. Each value has to be an array of two values, the first corresponds to the default value and the second one corresponds to an array of strings.

```json
{
  "defaultValues": {
    "name": ["", []],
    "age": [10, []]
  },
  "resolver": "...",
  "render": "..."
}
```

Whenever the user goes to the next or previous step and then goes back to the same one, if the array of strings is the same, it will have as default value the value that was introduced before. It is especially helpful when dealing with loops.

### resolver{% id="resolver" %}

The `resolver` property defines the validation rules of the form.

Its value corresponds to an [Expry](https://expry.dev) expression and it has to evaluate to an object with key-value pairs. Each value has to be an array of two-value arrays. The first value is an [Expry](https://expry.dev) expression that has to evaluate to a boolean value and the second one is an error message.

```json
{
  "defaultValues": "...",
  "resolver": {
    "name": [
      [{ "$ne": ["$name", ""] }, "Required"],
      [{ "$lt": [{ "$strLen": "$name" }, 20] }, "No more than 20 chars"]
    ]
  },
  "render": "..."
}
```

The expressions that have to evaluate to boolean values have access to the values of the form as variables. We can use them to perform any validation logic we want.

### render{% id="render" %}

The `render` property defines what the form needs to render.

Its value corresponds to an [Expry](https://expry.dev) expression and it has to evaluate to an object that corresponds to the reference of a component.

```json
{
  "defaultValues": "...",
  "resolver": "...",
  "render": {
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
}
```

## Example{% id="example" %}

Here is an example in which the **form** element is used.

```tsx
import type { Schema } from "formity";

const schema: Schema = [
  {
    form: {
      defaultValues: {
        name: ["", []],
        surname: ["", []],
        age: ["", []],
      },
      resolver: {
        name: [
          [{ "#$ne": ["#$name", ""] }, "Required"],
          [{ "#$lt": [{ "#$strLen": "#$name" }, 20] }, "Max 20 chars"],
        ],
        surname: [
          [{ "#$ne": ["#$surname", ""] }, "Required"],
          [{ "#$lt": [{ "#$strLen": "#$surname" }, 20] }, "Max 20 chars"],
        ],
        age: [
          [{ "#$ne": ["#$age", ""] }, "Required"],
          [{ "#$gte": ["#$age", 10] }, "You must be at least 10 years old"],
          [{ "#$lte": ["#$age", 100] }, "You must be at most 100 years old"],
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
                  row: {
                    items: [
                      {
                        textField: {
                          name: "name",
                          label: "Name",
                        },
                      },
                      {
                        textField: {
                          name: "surname",
                          label: "Surname",
                        },
                      },
                    ],
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
    return: {
      name: "$name",
      surname: "$surname",
      age: "$age",
    },
  },
];

export default schema;
```
