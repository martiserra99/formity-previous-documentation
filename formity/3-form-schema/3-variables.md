# Variables

Learn what the variables element is about and how it is used in the schema.

## Usage

The **variables** element is used to define variables.

## Structure

It is defined using the following structure.

```json
{ "variables": "expression" }
```

The value of `variables` corresponds to an [Expry](https://expry.dev) expression and it has to evaluate to an object with key-value pairs. Each key corresponds to a variable.

```json
{
  "variables": {
    "fullName": "Marti Serra",
    "isAdult": true
  }
}
```

## Example

Here is an example in which the **variables** element is used.

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
    variables: {
      fullName: { $concat: ["$name", " ", "$surname"] },
    },
  },
  {
    return: "$fullName",
  },
];

export default schema;
```
