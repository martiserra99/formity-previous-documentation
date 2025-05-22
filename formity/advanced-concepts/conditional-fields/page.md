---
title: Conditional fields
nextjs:
  metadata:
    title: Conditional fields - Docs
    description: Learn how to add fields that appear when a condition is met in these forms.
---

Learn how to add fields that appear when a condition is met in these forms.

---

## Initial steps{% id="initial-steps" %}

This package allows you to create fields that appear when a condition is met in a really simple way. To learn how to do it, clone the following GitHub repository so that you don't need to start from scratch.

```bash
git clone https://github.com/martiserra99/formity-previous-conditional-fields
```

Make sure you run the following command to install all the dependencies:

```bash
npm install
```

## Create conditional field{% id="create-conditional-field" %}

We will create the `components/conditional-field.tsx` file to create conditional fields. At this file we will create the following component:

```tsx
// src/components/conditional-field.tsx

import { Value, expry } from "expry";
import { useFormContext } from "react-hook-form";

interface ConditionalFieldProps {
  condition: Value;
  values: string[];
  children: React.ReactNode;
}

export default function ConditionalField({
  condition,
  values,
  children,
}: ConditionalFieldProps) {
  const { watch } = useFormContext();
  const variables = watch(values).reduce(
    (acc, value, index) => ({ ...acc, [values[index]]: value }),
    {}
  );
  if (expry(condition, variables)) {
    return children;
  }
  return null;
}
```

This component receives a condition that is an [Expry](https://expry.dev) expression, the list of values of the form that we want to work with, and the field that we want to render conditionally.

After creating the component, we need to update the `components.tsx` file to add this new component:

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

// ...
import ConditionalField from "./components/conditional-field";

type Parameters = {
  // ...
  conditionalField: {
    condition: Value;
    values: string[];
    children: Value;
  };
};

const components: Components<Parameters> = {
  // ...
  conditionalField: ({ condition, values, children }, render) => (
    <ConditionalField condition={condition} values={values}>
      {render(children)}
    </ConditionalField>
  ),
};

export default components;
```

## Create schema{% id="create-schema" %}

Now that we have the component created, we can use it in the schema. We can update the `schema.ts` file and use the component to show a field conditionally:

```tsx
// src/schema.ts

import { Schema } from "formity";

const schema: Schema = [
  {
    form: {
      defaultValues: {
        working: [true, []],
        company: ["", []],
      },
      resolver: {
        company: [
          [
            {
              "#$cond": {
                if: { "#$eq": ["#$working", true] },
                then: { "#$ne": ["#$company", ""] },
                else: true,
              },
            },
            "Required",
          ],
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
              heading: "Are you working?",
              description: "We would want to know if you are working",
              fields: [
                {
                  yesNo: {
                    name: "working",
                    label: "Working",
                  },
                },
                {
                  conditionalField: {
                    condition: { "#$eq": ["#$working", true] },
                    values: ["working"],
                    children: {
                      textField: {
                        name: "company",
                        label: "Company name",
                      },
                    },
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
      company: {
        $cond: {
          if: { $eq: ["$working", true] },
          then: "$company",
          else: null,
        },
      },
    },
  },
  {
    return: {
      working: "$working",
      company: "$company",
    },
  },
];

export default schema;
```

Apart from using the component to render the field conditionally, we also do some other things. We have the condition in the validation rules and we also create a variable with a value that depends on the condition.
