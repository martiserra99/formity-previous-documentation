---
title: Condition
nextjs:
  metadata:
    title: Condition - Docs
    description: Learn what the condition element is about and how it is used in the schema.
---

Learn what the condition element is about and how it is used in the schema.

---

## Usage{% id="usage" %}

The **condition** element is used to define a condition.

## Structure{% id="structure" %}

It is defined using the following structure.

```json
{
  "cond": {
    "if": "expression",
    "then": ["element", "element", "..."],
    "else": ["element", "element", "..."]
  }
}
```

The value of `if` corresponds to an [Expry](https://expry.dev) expression and it has to evaluate to a boolean value. If it is `true`, the elements inside `then` will be used; otherwise, the elements inside `else` will be used.

## Example{% id="example" %}

Here is an example in which the **condition** element is used.

```tsx
import type { Schema } from "formity";

const schema: Schema = [
  {
    form: {
      defaultValues: {
        softwareDeveloper: [true, []],
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
              heading: "Are you a software developer?",
              description:
                "We would like to know if you are a software developer",
              fields: [
                {
                  yesNo: {
                    name: "softwareDeveloper",
                    label: "Software Developer",
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
  {
    cond: {
      if: { $eq: ["$softwareDeveloper", true] },
      then: [
        {
          form: {
            defaultValues: {
              languages: [[], []],
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
                    heading: "What are your favourite programming languages?",
                    description:
                      "We would like to know which of the following programming languages you like the most",
                    fields: [
                      {
                        multiSelect: {
                          name: "languages",
                          label: "Languages",
                          options: [
                            { value: "javascript", label: "JavaScript" },
                            { value: "python", label: "Python" },
                            { value: "go", label: "Go" },
                          ],
                          direction: "y",
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
        {
          return: {
            softwareDeveloper: "$softwareDeveloper",
            languages: "$languages",
          },
        },
      ],
      else: [
        {
          form: {
            defaultValues: {
              interested: ["maybe", []],
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
                    heading: "Would you be interested in learning how to code?",
                    description: "Having coding skills can be very beneficial",
                    fields: [
                      {
                        listbox: {
                          name: "interested",
                          label: "Interested",
                          options: [
                            {
                              value: "maybe",
                              label: "Maybe in another time.",
                            },
                            {
                              value: "yes",
                              label: "Yes, that sounds good.",
                            },
                            { value: "no", label: "No, it is not for me." },
                          ],
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
        {
          return: {
            softwareDeveloper: "$softwareDeveloper",
            interested: "$interested",
          },
        },
      ],
    },
  },
];

export default schema;
```
