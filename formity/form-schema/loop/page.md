---
title: Loop
nextjs:
  metadata:
    title: Loop - Docs
    description: Learn what the loop element is about and how it is used in the schema.
---

Learn what the loop element is about and how it is used in the schema.

---

## Usage{% id="usage" %}

The **loop** element is used to define a loop.

## Structure{% id="stucture" %}

It is defined using the following structure.

```json
{
  "loop": {
    "while": "expression",
    "do": ["element", "element", "..."]
  }
}
```

The value of `loop` corresponds to an [Expry](https://expry.dev) expression and it has to evaluate to a boolean value. While the boolean is `true`, the elements inside `do` will be used.

## Example{% id="example" %}

Here is an example in which the **loop** element is used.

```tsx
import type { Schema } from "formity";

const schema: Schema = [
  {
    variables: {
      languages: [
        {
          value: "js",
          question: "What rating would you give to the JavaScript language?",
        },
        {
          value: "py",
          question: "What rating would you give to the Python language?",
        },
        {
          value: "go",
          question: "What rating would you give to the Go language?",
        },
      ],
    },
  },
  {
    variables: {
      i: 0,
      languagesRatings: [],
    },
  },
  {
    loop: {
      while: { $lt: ["$i", { $size: "$languages" }] },
      do: [
        {
          variables: {
            language: { $arrayElemAt: ["$languages", "$i"] },
          },
        },
        {
          form: {
            defaultValues: {
              rating: ["love-it", ["$language.value"]],
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
                    heading: "$language.question",
                    description: "We would like to know how much you like it",
                    fields: [
                      {
                        select: {
                          name: "rating",
                          label: "Rating",
                          options: [
                            {
                              value: "love-it",
                              label: "Love it",
                            },
                            {
                              value: "like-it-a-lot",
                              label: "Like it a lot",
                            },
                            {
                              value: "it-is-okay",
                              label: "It's okay",
                            },
                          ],
                          direction: "y",
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
            i: { $add: ["$i", 1] },
            languagesRatings: {
              $concatArrays: [
                "$languagesRatings",
                [{ name: "$language.value", rating: "$rating" }],
              ],
            },
          },
        },
      ],
    },
  },
  {
    return: "$languagesRatings",
  },
];

export default schema;
```
