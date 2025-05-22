---
title: Variables
nextjs:
  metadata:
    title: Variables - Docs
    description: Learn how we can pass additional variables to the schema.
---

Learn how we can pass additional variables to the schema.

---

## Variables{% id="variables" %}

We can pass additional variables to the schema using the `Formity` component. We just need to pass the variables to the `variables` prop:

```tsx
// ...

export default function Home() {
  // ...

  return (
    <Formity
      components={components}
      variables={{ name: "John" }}
      schema={schema}
      onReturn={handleReturn}
    />
  );
}
```

Then, in the schema we will be able to use the variables, as we can see here:

```ts
import { Schema } from "formity";

const schema: Schema = [
  {
    form: {
      defaultValues: {
        name: ["$name", []],
      },
      resolver: {
        name: [[{ "#$ne": ["#$name", ""] }, "Required"]],
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
    },
  },
];

export default schema;
```
