---
title: Conditional fields
nextjs:
  metadata:
    title: Conditional fields - Docs
    description: Learn how to add fields that appear when a condition is met.
---

Learn how to add fields that appear when a condition is met.

---

## Conditional fields{% id="conditional-fields" %}

We can create a field that appear when a condition is met. To do it, we can create a file named `conditional-field.tsx` in the `components` folder with the following code.

```tsx
// components/conditional-field.tsx
import type { ReactNode } from "react";

import { useFormContext } from "react-hook-form";

interface ConditionalFieldProps<T extends Record<string, unknown>> {
  condition: (values: T) => boolean;
  values: string[];
  children: ReactNode;
}

export function ConditionalField<T extends Record<string, unknown>>({
  condition,
  values,
  children,
}: ConditionalFieldProps<T>) {
  const { watch } = useFormContext();
  const variables = watch(values).reduce(
    (acc, value, index) => ({ ...acc, [values[index]]: value }),
    {},
  );
  if (condition(variables)) {
    return children;
  }
  return null;
}
```

Then, we can update the schema with the code shown below.

```tsx
// schema.tsx
import type { Schema, Form, Return, Variables } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NextButton } from "./components/buttons/next-button";
import { ConditionalField } from "./components/conditional-field";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<{ working: string; company: string }>,
  Variables<{ company: string | null }>,
  Return<{ working: string; company: string | null }>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        working: ["no", []],
        company: ["", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="yourself"
            defaultValues={values}
            resolver={zodResolver(
              z
                .object({
                  working: z.string(),
                  company: z.string(),
                })
                .superRefine((data, ctx) => {
                  if (data.working === "yes") {
                    if (data.company === "") {
                      ctx.addIssue({
                        code: z.ZodIssueCode.custom,
                        message: "Required",
                        path: ["company"],
                      });
                    }
                  }
                }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>Tell us about yourself</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="working"
                  label="Are you working?"
                  options={[
                    { value: "yes", label: "Yes" },
                    { value: "no", label: "No" },
                  ]}
                />
                <ConditionalField<{ working: string }>
                  condition={(values) => values.working === "yes"}
                  values={["working"]}
                >
                  <TextInput
                    name="company"
                    label="At what company?"
                    placeholder="Company name"
                  />
                </ConditionalField>
              </FormStepInputs>
              <NextButton>Submit</NextButton>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    variables: ({ working, company }) => ({
      company: working === "yes" ? company : null,
    }),
  },
  {
    return: ({ working, company }) => ({
      working,
      company: company,
    }),
  },
];
```

We need to apply validation rules to the conditional field only when its condition is met. Additionally, we should set a default value for the field when it’s hidden, using a variable.
