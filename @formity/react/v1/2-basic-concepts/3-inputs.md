# Inputs

Learn how to pass values to be used inside the schema.

## Inputs

As we move forward, an object with values is made available to each element. It contains the values from the form and variables elements encountered so far.

To provide additional values, we can use the `inputs` prop of the `Formity` component. To do this, the schema should be defined as follows.

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

export const schema: Schema<Values, Inputs> = [
  {
    form: {
      values: ({ defaultLanguage }) => ({
        language: [defaultLanguage, []],
      }),
      render: ({ inputs, values, onNext, onBack }) => (
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
              <FormStepHeading>
                What language do you speak the most?
              </FormStepHeading>
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

We pass the `Inputs` type to `Schema` to specify the types of the values.

We can now provide the values using the `inputs` prop of the `Formity` component.

```tsx
import { useCallback, useState } from "react";

import { Formity, type OnReturn, type ReturnOutput } from "@formity/react";

import { Output } from "./components/output";

import { schema, type Values, type Inputs } from "./schema";

export default function App() {
  const [output, setOutput] = useState<ReturnOutput<Values> | null>(null);

  const onReturn = useCallback<OnReturn<Values>>((output) => {
    setOutput(output);
  }, []);

  if (output) {
    return <Output output={output} onStart={() => setOutput(null)} />;
  }

  return (
    <Formity<Values, Inputs>
      schema={schema}
      inputs={{
        languages: [
          { label: "English", value: "en" },
          { label: "Spanish", value: "es" },
          { label: "Catalan", value: "ca" },
        ],
        defaultLanguage: "en",
      }}
      onReturn={onReturn}
    />
  );
}
```
