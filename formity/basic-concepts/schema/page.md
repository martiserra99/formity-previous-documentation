---
title: Schema
nextjs:
  metadata:
    title: Schema - Docs
    description: Learn what the schema is about and how it can be used to define the forms.
---

Learn what the schema is about and how it can be used to define the forms.

---

## Schema{% id="schema" %}

The schema is a JSON used to define the form. It consists of various elements of different types, and the existing ones are as follows:

- **Form**: It defines a step in the form.

- **Return**: It defines what the form returns.

- **Variables**: It defines variables.

- **Condition**: It defines a condition.

- **Loop**: It defines a loop.

All these elements, used in combination, allow us to create any form in a really simple way. These elements use the [Expry](https://expry.dev) package under the hood, allowing us to take advantage of various operators to perform any logic we want.

Here is an example of a schema:

```tsx
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
