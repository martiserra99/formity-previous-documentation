---
title: Params
nextjs:
  metadata:
    title: Params - Docs
    description: Learn how to pass values that can be used when rendering each form.
---

Learn how to pass values that can be used when rendering each form.

---

## Params{% id="Params" %}

The `params` prop of the `Formity` component allows us to pass values meant to be used when rendering each form. Unlike the `inputs` prop, these values are exclusively for rendering purposes, and any changes to them will dynamically update the form.

To pass these values, we first need to define the schema the following way.

```tsx
import type { Schema, Form, Return } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { NextButton } from "./components/buttons/next-button";

import { MultiStep } from "./multi-step";

export type Values = [Form<{ language: string }>, Return<{ language: string }>];

export type Inputs = {
  languages: { label: string; value: string }[];
  defaultLanguage: string;
};

export type Params = {
  heading: string;
};

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: ({ defaultLanguage }) => ({
        language: [defaultLanguage, []],
      }),
      render: ({ inputs, values, params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="language"
            defaultValues={values}
            resolver={zodResolver(
              z.object({
                language: z.string(),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>{params.heading}</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="language"
                  label="Language"
                  options={inputs.languages}
                />
              </FormStepInputs>
              <NextButton>Submit</NextButton>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    return: ({ language }) => ({
      language,
    }),
  },
];
```

We pass the `Params` type to the `Schema` to specify the types of the values.

We can now provide the values using the `params` prop of the `Formity` component.

```tsx
import { useCallback, useState } from "react";

import { Formity, type OnReturn, type ReturnOutput } from "@formity/react";

import { Output } from "./components/output";

import { schema, type Values, type Inputs, type Params } from "./schema";

export default function App() {
  const [output, setOutput] = useState<ReturnOutput<Values> | null>(null);

  const onReturn = useCallback<OnReturn<Values>>((output) => {
    setOutput(output);
  }, []);

  if (output) {
    return <Output output={output} onStart={() => setOutput(null)} />;
  }

  return (
    <Formity<Values, Inputs, Params>
      schema={schema}
      inputs={{
        languages: [
          { label: "English", value: "en" },
          { label: "Spanish", value: "es" },
          { label: "Catalan", value: "ca" },
        ],
        defaultLanguage: "en",
      }}
      params={{
        heading: "What language do you speak the most?",
      }}
      onReturn={onReturn}
    />
  );
}
```
