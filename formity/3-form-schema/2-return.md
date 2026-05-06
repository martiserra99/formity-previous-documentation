# Return

Learn what the return element is about and how it is used in the schema.

## Usage

The **return** element is used to define what the form returns.

## Structure

It is defined using the following structure.

```json
{ "return": "expression" }
```

The value of `return` corresponds to an [Expry](https://expry.dev) expression and it can evaluate to anything you want.

## Example

Here is an example in which the **return** element is used.

```tsx
import type { Schema } from "formity";

const schema: Schema = [
  {
    form: {
      defaultValues: {
        name: ["", []],
        surname: ["", []],
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
      $concat: ["$name", " ", "$surname"],
    },
  },
];

export default schema;
```
