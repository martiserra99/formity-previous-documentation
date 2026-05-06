---
title: Tutorial
nextjs:
  metadata:
    title: Tutorial - Docs
    description: Follow this tutorial to grasp the core concepts of Formity and how it has to be used.
---

Follow this tutorial to grasp the core concepts of Formity and how it has to be used.

---

## Initial steps{% id="initial-steps" %}

In this tutorial, we'll show you how to turn a basic single-step form into a dynamic multi-step form with conditional logic. The starting point is already set up in the GitHub repository below, so go ahead and clone it to follow along.

```bash
git clone https://github.com/martiserra99/formity-tutorial
```

Make sure you run the following command to install all the dependencies.

```bash
npm install
```

This tutorial explains how to use Formity with TypeScript, but if you want to learn how to use it with JavaScript you can still follow this tutorial since almost everything is the same. The only thing that is different is that in JavaScript you don't define the types.

## Single-step form{% id="single-step-form" %}

If you take a look at the `app.tsx` file, you'll find a single-step form already in place. This form is built using [React Hook Form](https://react-hook-form.com/). However, you're not restricted to this library. Formity is designed to work smoothly with any single-step form library you choose.

```tsx
// app.tsx
import { useCallback, useState } from "react";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { Output } from "./components/output";

export default function App() {
  const [output, setOutput] = useState<unknown>(null);

  const onSubmit = useCallback((output: unknown) => {
    setOutput(output);
  }, []);

  if (output) {
    return <Output output={output} onStart={() => setOutput(null)} />;
  }

  return (
    <FormStep
      defaultValues={{
        name: "",
        surname: "",
        age: 20,
      }}
      resolver={zodResolver(
        z.object({
          name: z
            .string()
            .min(1, { message: "Required" })
            .max(20, { message: "Must be at most 20 characters" }),
          surname: z
            .string()
            .min(1, { message: "Required" })
            .max(20, { message: "Must be at most 20 characters" }),
          age: z
            .number()
            .min(18, { message: "Minimum of 18 years old" })
            .max(99, { message: "Maximum of 99 years old" }),
        }),
      )}
      onSubmit={onSubmit}
    >
      <FormStepContent>
        <FormStepHeading>Tell us about yourself</FormStepHeading>
        <FormStepInputs>
          <FormStepRow>
            <TextInput name="name" label="Name" placeholder="Your name" />
            <TextInput
              name="surname"
              label="Surname"
              placeholder="Your surname"
            />
          </FormStepRow>
          <NumberInput name="age" label="Age" placeholder="Your age" />
        </FormStepInputs>
        <NextButton>Submit</NextButton>
      </FormStepContent>
    </FormStep>
  );
}
```

## Formity component{% id="formity-component" %}

To get started with Formity, the first thing we'll do is use the `Formity` component. It is the one that renders the multi-step form, and these are the most important props:

- `schema`: Defines the structure and behavior of the multi-step form.
- `onReturn`: A callback function that is triggered when the form is completed.

We'll replace the code that we have in `app.tsx` with the following code.

```tsx
// app.tsx
import { useCallback, useState } from "react";

import { Formity, type OnReturn, type ReturnOutput } from "@formity/react";

import { Output } from "./components/output";

export default function App() {
  const [output, setOutput] = useState<ReturnOutput<[]> | null>(null);

  const onReturn = useCallback<OnReturn<[]>>((output) => {
    setOutput(output);
  }, []);

  if (output) {
    return <Output output={output} onStart={() => setOutput(null)} />;
  }

  return <Formity<[]> schema={[]} onReturn={onReturn} />;
}
```

## Form schema{% id="form-schema" %}

The next step is to create the schema, which defines the structure and behavior of the multi-step form. To do this, we'll create a `schema.tsx` file with the following code.

```tsx
// schema.tsx
import type { Schema, Form } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  Form<{ softwareDeveloper: string }>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      render: ({ values, onNext }) => (
        <FormStep
          key="yourself"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              name: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              surname: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              age: z
                .number()
                .min(18, { message: "Minimum of 18 years old" })
                .max(99, { message: "Maximum of 99 years old" }),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Tell us about yourself</FormStepHeading>
            <FormStepInputs>
              <FormStepRow>
                <TextInput name="name" label="Name" placeholder="Your name" />
                <TextInput
                  name="surname"
                  label="Surname"
                  placeholder="Your surname"
                />
              </FormStepRow>
              <NumberInput name="age" label="Age" placeholder="Your age" />
            </FormStepInputs>
            <NextButton>Next</NextButton>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
  {
    form: {
      values: () => ({
        softwareDeveloper: ["yes", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <FormStep
          key="softwareDeveloper"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              softwareDeveloper: z.string(),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Are you a software developer?</FormStepHeading>
            <FormStepInputs>
              <Select
                name="softwareDeveloper"
                label="Software developer"
                options={[
                  { value: "yes", label: "Yes" },
                  { value: "no", label: "No" },
                ]}
              />
            </FormStepInputs>
            <FormStepRow>
              <BackButton onBack={onBack}>Back</BackButton>
              <NextButton>Submit</NextButton>
            </FormStepRow>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
];
```

The `schema` constant is an array of type `Schema`. There are different types of elements you can use within the schema, and in this example, we've included two form elements.

Additionally, to ensure complete type safety, the `Schema` accepts a `Values` type that defines the values handled at each step of the multi-step form.

We can now pass the `schema` to the `Formity` component, as shown below.

```tsx
// app.tsx
import { useCallback, useState } from "react";

import { Formity, type OnReturn, type ReturnOutput } from "@formity/react";

import { Output } from "./components/output";

import { schema, type Values } from "./schema";

export default function App() {
  const [output, setOutput] = useState<ReturnOutput<Values> | null>(null);

  const onReturn = useCallback<OnReturn<Values>>((output) => {
    setOutput(output);
  }, []);

  if (output) {
    return <Output output={output} onStart={() => setOutput(null)} />;
  }

  return <Formity<Values> schema={schema} onReturn={onReturn} />;
}
```

If you complete the multi-step form, you'll see that the `onReturn` callback is not called. That's because we need to add a return element to the schema, as shown below.

```tsx
// schema.tsx
import type { Schema, Form, Return } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  Form<{ softwareDeveloper: string }>,
  Return<{
    name: string;
    surname: string;
    age: number;
    softwareDeveloper: boolean;
  }>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      render: ({ values, onNext }) => (
        <FormStep
          key="yourself"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              name: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              surname: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              age: z
                .number()
                .min(18, { message: "Minimum of 18 years old" })
                .max(99, { message: "Maximum of 99 years old" }),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Tell us about yourself</FormStepHeading>
            <FormStepInputs>
              <FormStepRow>
                <TextInput name="name" label="Name" placeholder="Your name" />
                <TextInput
                  name="surname"
                  label="Surname"
                  placeholder="Your surname"
                />
              </FormStepRow>
              <NumberInput name="age" label="Age" placeholder="Your age" />
            </FormStepInputs>
            <NextButton>Next</NextButton>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
  {
    form: {
      values: () => ({
        softwareDeveloper: ["yes", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <FormStep
          key="softwareDeveloper"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              softwareDeveloper: z.string(),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Are you a software developer?</FormStepHeading>
            <FormStepInputs>
              <Select
                name="softwareDeveloper"
                label="Software developer"
                options={[
                  { value: "yes", label: "Yes" },
                  { value: "no", label: "No" },
                ]}
              />
            </FormStepInputs>
            <FormStepRow>
              <BackButton onBack={onBack}>Back</BackButton>
              <NextButton>Submit</NextButton>
            </FormStepRow>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
  {
    return: ({ name, surname, age, softwareDeveloper }) => ({
      name,
      surname,
      age,
      softwareDeveloper: softwareDeveloper === "yes",
    }),
  },
];
```

So far, we've covered the form and return elements. However, Formity supports additional elements that allow you to build any logic you need. One of these is the condition element, which can be used as you can see here.

```tsx
// schema.tsx
import type { Schema, Form, Return, Cond } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  Form<{ softwareDeveloper: string }>,
  Cond<{
    then: [
      Form<{ expertise: string }>,
      Return<{
        name: string;
        surname: string;
        age: number;
        softwareDeveloper: true;
        expertise: string;
      }>,
    ];
    else: [
      Form<{ interested: string }>,
      Return<{
        name: string;
        surname: string;
        age: number;
        softwareDeveloper: false;
        interested: string;
      }>,
    ];
  }>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      render: ({ values, onNext }) => (
        <FormStep
          key="yourself"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              name: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              surname: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              age: z
                .number()
                .min(18, { message: "Minimum of 18 years old" })
                .max(99, { message: "Maximum of 99 years old" }),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Tell us about yourself</FormStepHeading>
            <FormStepInputs>
              <FormStepRow>
                <TextInput name="name" label="Name" placeholder="Your name" />
                <TextInput
                  name="surname"
                  label="Surname"
                  placeholder="Your surname"
                />
              </FormStepRow>
              <NumberInput name="age" label="Age" placeholder="Your age" />
            </FormStepInputs>
            <NextButton>Next</NextButton>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
  {
    form: {
      values: () => ({
        softwareDeveloper: ["yes", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <FormStep
          key="softwareDeveloper"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              softwareDeveloper: z.string(),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Are you a software developer?</FormStepHeading>
            <FormStepInputs>
              <Select
                name="softwareDeveloper"
                label="Software developer"
                options={[
                  { value: "yes", label: "Yes" },
                  { value: "no", label: "No" },
                ]}
              />
            </FormStepInputs>
            <FormStepRow>
              <BackButton onBack={onBack}>Back</BackButton>
              <NextButton>Next</NextButton>
            </FormStepRow>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
  {
    cond: {
      if: ({ softwareDeveloper }) => softwareDeveloper === "yes",
      then: [
        {
          form: {
            values: () => ({
              expertise: ["frontend", []],
            }),
            render: ({ values, onNext, onBack }) => (
              <FormStep
                key="expertise"
                defaultValues={values}
                resolver={zodResolver(
                  z.object({
                    expertise: z.string(),
                  }),
                )}
                onSubmit={onNext}
              >
                <FormStepContent>
                  <FormStepHeading>
                    What is your area of expertise?
                  </FormStepHeading>
                  <FormStepInputs>
                    <Select
                      name="expertise"
                      label="Expertise"
                      options={[
                        { value: "frontend", label: "Frontend development" },
                        { value: "backend", label: "Backend development" },
                        { value: "mobile", label: "Mobile development" },
                      ]}
                    />
                  </FormStepInputs>
                  <FormStepRow>
                    <BackButton onBack={onBack}>Back</BackButton>
                    <NextButton>Submit</NextButton>
                  </FormStepRow>
                </FormStepContent>
              </FormStep>
            ),
          },
        },
        {
          return: ({ name, surname, age, expertise }) => ({
            name,
            surname,
            age,
            softwareDeveloper: true,
            expertise,
          }),
        },
      ],
      else: [
        {
          form: {
            values: () => ({
              interested: ["yes", []],
            }),
            render: ({ values, onNext, onBack }) => (
              <FormStep
                key="interested"
                defaultValues={values}
                resolver={zodResolver(
                  z.object({
                    interested: z.string(),
                  }),
                )}
                onSubmit={onNext}
              >
                <FormStepContent>
                  <FormStepHeading>
                    Are you interested in learning how to code?
                  </FormStepHeading>
                  <FormStepInputs>
                    <Select
                      name="interested"
                      label="Interested"
                      options={[
                        { value: "yes", label: "Yes, I am interested." },
                        { value: "no", label: "No, it is not for me." },
                        { value: "maybe", label: "Maybe, I am not sure." },
                      ]}
                    />
                  </FormStepInputs>
                  <FormStepRow>
                    <BackButton onBack={onBack}>Back</BackButton>
                    <NextButton>Submit</NextButton>
                  </FormStepRow>
                </FormStepContent>
              </FormStep>
            ),
          },
        },
        {
          return: ({ name, surname, age, interested }) => ({
            name,
            surname,
            age,
            softwareDeveloper: false,
            interested,
          }),
        },
      ],
    },
  },
];
```

You've successfully created a multi-step form with conditional logic. Be sure to explore the other schema elements to see everything Formity can do.

## Form submission{% id="form-submission" %}

Until now, we have shown the values we want to submit. Now, we need to create the logic to submit these values and keep track of the submission state. To do this, we'll start by creating a `types.ts` file with the following code.

```ts
// types.ts
export type Status = FormityStatus | EndStatus;

export type FormityStatus = {
  type: "formity";
  submitting: boolean;
};

export type EndStatus = {
  type: "end";
};
```

Then, we'll update the `app.tsx` file to include the submission logic and display a thank you screen once the values have been successfully submitted.

```tsx
// app.tx
import { useCallback, useState } from "react";

import { Formity, type OnReturn } from "@formity/react";

import { End } from "./components/end";

import { schema, type Values } from "./schema";

import type { Status } from "./types";

export default function App() {
  const [status, setStatus] = useState<Status>({
    type: "formity",
    submitting: false,
  });

  const onReturn = useCallback<OnReturn<Values>>(async (output) => {
    setStatus({ type: "formity", submitting: true });

    // Show output in the console
    console.log(output);

    // Simulate a network request
    await new Promise((resolve) => setTimeout(resolve, 2000));

    setStatus({ type: "end" });
  }, []);

  if (status.type === "end") {
    return (
      <End onStart={() => setStatus({ type: "formity", submitting: false })} />
    );
  }

  return <Formity<Values> schema={schema} onReturn={onReturn} />;
}
```

As you may have noticed, the form stays interactive during submission, with no feedback shown. To address this, we'll access the `status` object within the schema to disable the form and provide visual feedback to the user.

```tsx
// schema.tsx
import type { Schema, Form, Return, Cond } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import type { FormityStatus } from "./types";

export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  Form<{ softwareDeveloper: string }>,
  Cond<{
    then: [
      Form<{ expertise: string }>,
      Return<{
        name: string;
        surname: string;
        age: number;
        softwareDeveloper: true;
        expertise: string;
      }>,
    ];
    else: [
      Form<{ interested: string }>,
      Return<{
        name: string;
        surname: string;
        age: number;
        softwareDeveloper: false;
        interested: string;
      }>,
    ];
  }>,
];

export type Inputs = Record<never, never>;

export type Params = {
  status: FormityStatus;
};

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      render: ({ values, onNext }) => (
        <FormStep
          key="yourself"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              name: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              surname: z
                .string()
                .min(1, { message: "Required" })
                .max(20, { message: "Must be at most 20 characters" }),
              age: z
                .number()
                .min(18, { message: "Minimum of 18 years old" })
                .max(99, { message: "Maximum of 99 years old" }),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Tell us about yourself</FormStepHeading>
            <FormStepInputs>
              <FormStepRow>
                <TextInput name="name" label="Name" placeholder="Your name" />
                <TextInput
                  name="surname"
                  label="Surname"
                  placeholder="Your surname"
                />
              </FormStepRow>
              <NumberInput name="age" label="Age" placeholder="Your age" />
            </FormStepInputs>
            <NextButton>Next</NextButton>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
  {
    form: {
      values: () => ({
        softwareDeveloper: ["yes", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <FormStep
          key="softwareDeveloper"
          defaultValues={values}
          resolver={zodResolver(
            z.object({
              softwareDeveloper: z.string(),
            }),
          )}
          onSubmit={onNext}
        >
          <FormStepContent>
            <FormStepHeading>Are you a software developer?</FormStepHeading>
            <FormStepInputs>
              <Select
                name="softwareDeveloper"
                label="Software developer"
                options={[
                  { value: "yes", label: "Yes" },
                  { value: "no", label: "No" },
                ]}
              />
            </FormStepInputs>
            <FormStepRow>
              <BackButton onBack={onBack}>Back</BackButton>
              <NextButton>Next</NextButton>
            </FormStepRow>
          </FormStepContent>
        </FormStep>
      ),
    },
  },
  {
    cond: {
      if: ({ softwareDeveloper }) => softwareDeveloper === "yes",
      then: [
        {
          form: {
            values: () => ({
              expertise: ["frontend", []],
            }),
            render: ({ values, params, onNext, onBack }) => (
              <FormStep
                key="expertise"
                defaultValues={values}
                resolver={zodResolver(
                  z.object({
                    expertise: z.string(),
                  }),
                )}
                disabled={params.status.submitting}
                onSubmit={onNext}
              >
                <FormStepContent>
                  <FormStepHeading>
                    What is your area of expertise?
                  </FormStepHeading>
                  <FormStepInputs>
                    <Select
                      name="expertise"
                      label="Expertise"
                      options={[
                        { value: "frontend", label: "Frontend development" },
                        { value: "backend", label: "Backend development" },
                        { value: "mobile", label: "Mobile development" },
                      ]}
                    />
                  </FormStepInputs>
                  <FormStepRow>
                    <BackButton onBack={onBack}>Back</BackButton>
                    <NextButton submitting={params.status.submitting}>
                      Submit
                    </NextButton>
                  </FormStepRow>
                </FormStepContent>
              </FormStep>
            ),
          },
        },
        {
          return: ({ name, surname, age, expertise }) => ({
            name,
            surname,
            age,
            softwareDeveloper: true,
            expertise,
          }),
        },
      ],
      else: [
        {
          form: {
            values: () => ({
              interested: ["yes", []],
            }),
            render: ({ values, params, onNext, onBack }) => (
              <FormStep
                key="interested"
                defaultValues={values}
                resolver={zodResolver(
                  z.object({
                    interested: z.string(),
                  }),
                )}
                disabled={params.status.submitting}
                onSubmit={onNext}
              >
                <FormStepContent>
                  <FormStepHeading>
                    Are you interested in learning how to code?
                  </FormStepHeading>
                  <FormStepInputs>
                    <Select
                      name="interested"
                      label="Interested"
                      options={[
                        { value: "yes", label: "Yes, I am interested." },
                        { value: "no", label: "No, it is not for me." },
                        { value: "maybe", label: "Maybe, I am not sure." },
                      ]}
                    />
                  </FormStepInputs>
                  <FormStepRow>
                    <BackButton onBack={onBack}>Back</BackButton>
                    <NextButton submitting={params.status.submitting}>
                      Submit
                    </NextButton>
                  </FormStepRow>
                </FormStepContent>
              </FormStep>
            ),
          },
        },
        {
          return: ({ name, surname, age, interested }) => ({
            name,
            surname,
            age,
            softwareDeveloper: false,
            interested,
          }),
        },
      ],
    },
  },
];
```

The `Params` type defines the values that are provided to the schema. In this case, we're providing the `status` object, which is used in the last two forms to disable interaction and indicate that a submission is in progress.

Lastly, we need to update the `app.tsx` file to pass the `status` object to the schema. We'll do this by providing it through the `params` prop of the `Formity` component.

```tsx
// app.tsx
import { useCallback, useState } from "react";

import { Formity, type OnReturn } from "@formity/react";

import { End } from "./components/end";

import { schema, type Values, type Inputs, type Params } from "./schema";

import type { Status } from "./types";

export default function App() {
  const [status, setStatus] = useState<Status>({
    type: "formity",
    submitting: false,
  });

  const onReturn = useCallback<OnReturn<Values>>(async (output) => {
    setStatus({ type: "formity", submitting: true });

    // Show output in the console
    console.log(output);

    // Simulate a network request
    await new Promise((resolve) => setTimeout(resolve, 2000));

    setStatus({ type: "end" });
  }, []);

  if (status.type === "end") {
    return (
      <End onStart={() => setStatus({ type: "formity", submitting: false })} />
    );
  }

  return (
    <Formity<Values, Inputs, Params>
      schema={schema}
      params={{ status }}
      onReturn={onReturn}
    />
  );
}
```

## Context{% id="context" %}

One last tip before wrapping up — using the Context API instead of passing the navigation functions and the status directly to the components can lead to cleaner code.

To do this, we recommend creating a `multi-step` folder with the following files.

`multi-step/multi-step-value.ts`:

```tsx
// multi-step/multi-step-value.ts
import type { OnNext, OnBack } from "@formity/react";
import type { FormityStatus } from "@/types";

export interface MultiStepValue<T extends Record<string, unknown>> {
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  status: FormityStatus;
}
```

`multi-step/multi-step-context.ts`:

```tsx
// multi-step/multi-step-context.ts
import { createContext } from "react";

import type { MultiStepValue } from "./multi-step-value";

type Value = MultiStepValue<Record<string, unknown>> | null;

export const MultiStepContext = createContext<Value>(null);
```

`multi-step/multi-step.tsx`:

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { OnNext, OnBack } from "@formity/react";

import { useMemo } from "react";

import type { FormityStatus } from "@/types";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  status: FormityStatus;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  onNext,
  onBack,
  status,
  children,
}: MultiStepProps<T>) {
  const values = useMemo(
    () => ({ onNext, onBack, status }),
    [onNext, onBack, status],
  ) as MultiStepValue<Record<string, unknown>>;
  return (
    <MultiStepContext.Provider value={values}>
      {children}
    </MultiStepContext.Provider>
  );
}
```

`multi-step/use-multi-step.ts`:

```tsx
// multi-step/use-multi-step.ts
import { useContext } from "react";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

function useMultiStep<T extends Record<string, unknown>>(): MultiStepValue<T> {
  const context = useContext(MultiStepContext);
  if (!context) throw new Error("useMultiStep must be used within a MultiStep");
  return context as MultiStepValue<T>;
}

export { useMultiStep };
```

`multi-step/index.ts`:

```tsx
// multi-step/index.ts
export type { MultiStepValue } from "./multi-step-value";
export { MultiStep } from "./multi-step";
export { useMultiStep } from "./use-multi-step";
```

Then, you need to update the following components.

`components/form-step.tsx`:

```tsx
// components/form-step.tsx
import type { ReactNode } from "react";
import type { DefaultValues, Resolver } from "react-hook-form";

import { FormProvider, useForm } from "react-hook-form";
import { useMultiStep } from "@/multi-step";

interface FormStepProps<T extends Record<string, unknown>> {
  defaultValues: DefaultValues<T>;
  resolver: Resolver<T>;
  children: ReactNode;
}

export function FormStep<T extends Record<string, unknown>>({
  defaultValues,
  resolver,
  children,
}: FormStepProps<T>) {
  const form = useForm({ defaultValues, resolver });
  const { onNext, status } = useMultiStep<T>();
  return (
    <form
      onSubmit={form.handleSubmit(onNext)}
      className="flex h-screen w-full items-center justify-center px-4 py-8 font-sans"
      inert={status.submitting}
    >
      <FormProvider {...form}>{children}</FormProvider>
    </form>
  );
}

interface FormStepContentProps {
  children: ReactNode;
}

export function FormStepContent({ children }: FormStepContentProps) {
  return <div className="w-full max-w-md">{children}</div>;
}

interface FormStepHeadingProps {
  children: ReactNode;
}

export function FormStepHeading({ children }: FormStepHeadingProps) {
  return (
    <h2 className="mb-6 text-center text-4xl font-semibold text-white">
      {children}
    </h2>
  );
}

interface FormStepInputsProps {
  children: ReactNode;
}

export function FormStepInputs({ children }: FormStepInputsProps) {
  return <div className="mb-6 flex flex-col gap-4">{children}</div>;
}

interface FormStepRowProps {
  children: ReactNode;
}

export function FormStepRow({ children }: FormStepRowProps) {
  return <div className="flex gap-4">{children}</div>;
}
```

`components/buttons/next-button.tsx`:

```tsx
// components/buttons/next-button.tsx
import type { ComponentPropsWithoutRef } from "react";

import { Button } from "../button";
import { useMultiStep } from "@/multi-step";

export function NextButton({
  children,
  ...props
}: ComponentPropsWithoutRef<"button">) {
  const { status } = useMultiStep();
  return (
    <Button
      type="submit"
      variant="primary"
      disabled={status.submitting}
      {...props}
    >
      {status.submitting ? "Submitting..." : children}
    </Button>
  );
}
```

`components/buttons/back-button.tsx`:

```tsx
// components/buttons/back-button.tsx
import type { ComponentPropsWithoutRef } from "react";

import { useFormContext } from "react-hook-form";

import { Button } from "../button";
import { useMultiStep } from "@/multi-step";

export function BackButton<T extends Record<string, unknown>>(
  props: ComponentPropsWithoutRef<"button">,
) {
  const { getValues } = useFormContext<T>();
  const { onBack } = useMultiStep<T>();
  return (
    <Button
      type="button"
      variant="secondary"
      onClick={() => onBack(getValues())}
      {...props}
    />
  );
}
```

Lastly, you need to update the schema to use the `MultiStep` component.

```tsx
// schema.tsx
import type { Schema, Form, Return, Cond } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import type { FormityStatus } from "./types";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  Form<{ softwareDeveloper: string }>,
  Cond<{
    then: [
      Form<{ expertise: string }>,
      Return<{
        name: string;
        surname: string;
        age: number;
        softwareDeveloper: true;
        expertise: string;
      }>,
    ];
    else: [
      Form<{ interested: string }>,
      Return<{
        name: string;
        surname: string;
        age: number;
        softwareDeveloper: false;
        interested: string;
      }>,
    ];
  }>,
];

export type Inputs = Record<never, never>;

export type Params = {
  status: FormityStatus;
};

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      render: ({ values, params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack} status={params.status}>
          <FormStep
            key="yourself"
            defaultValues={values}
            resolver={zodResolver(
              z.object({
                name: z
                  .string()
                  .min(1, { message: "Required" })
                  .max(20, { message: "Must be at most 20 characters" }),
                surname: z
                  .string()
                  .min(1, { message: "Required" })
                  .max(20, { message: "Must be at most 20 characters" }),
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>Tell us about yourself</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
                <NumberInput name="age" label="Age" placeholder="Your age" />
              </FormStepInputs>
              <NextButton>Next</NextButton>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    form: {
      values: () => ({
        softwareDeveloper: ["yes", []],
      }),
      render: ({ values, params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack} status={params.status}>
          <FormStep
            key="softwareDeveloper"
            defaultValues={values}
            resolver={zodResolver(
              z.object({
                softwareDeveloper: z.string(),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>Are you a software developer?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="softwareDeveloper"
                  label="Software developer"
                  options={[
                    { value: "yes", label: "Yes" },
                    { value: "no", label: "No" },
                  ]}
                />
              </FormStepInputs>
              <FormStepRow>
                <BackButton>Back</BackButton>
                <NextButton>Next</NextButton>
              </FormStepRow>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    cond: {
      if: ({ softwareDeveloper }) => softwareDeveloper === "yes",
      then: [
        {
          form: {
            values: () => ({
              expertise: ["frontend", []],
            }),
            render: ({ values, params, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack} status={params.status}>
                <FormStep
                  key="expertise"
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      expertise: z.string(),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>
                      What is your area of expertise?
                    </FormStepHeading>
                    <FormStepInputs>
                      <Select
                        name="expertise"
                        label="Expertise"
                        options={[
                          { value: "frontend", label: "Frontend development" },
                          { value: "backend", label: "Backend development" },
                          { value: "mobile", label: "Mobile development" },
                        ]}
                      />
                    </FormStepInputs>
                    <FormStepRow>
                      <BackButton>Back</BackButton>
                      <NextButton>Submit</NextButton>
                    </FormStepRow>
                  </FormStepContent>
                </FormStep>
              </MultiStep>
            ),
          },
        },
        {
          return: ({ name, surname, age, expertise }) => ({
            name,
            surname,
            age,
            softwareDeveloper: true,
            expertise,
          }),
        },
      ],
      else: [
        {
          form: {
            values: () => ({
              interested: ["yes", []],
            }),
            render: ({ values, params, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack} status={params.status}>
                <FormStep
                  key="interested"
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      interested: z.string(),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>
                      Are you interested in learning how to code?
                    </FormStepHeading>
                    <FormStepInputs>
                      <Select
                        name="interested"
                        label="Interested"
                        options={[
                          { value: "yes", label: "Yes, I am interested." },
                          { value: "no", label: "No, it is not for me." },
                          { value: "maybe", label: "Maybe, I am not sure." },
                        ]}
                      />
                    </FormStepInputs>
                    <FormStepRow>
                      <BackButton>Back</BackButton>
                      <NextButton>Submit</NextButton>
                    </FormStepRow>
                  </FormStepContent>
                </FormStep>
              </MultiStep>
            ),
          },
        },
        {
          return: ({ name, surname, age, interested }) => ({
            name,
            surname,
            age,
            softwareDeveloper: false,
            interested,
          }),
        },
      ],
    },
  },
];
```

## Next steps{% id="next-steps" %}

You've successfully completed the tutorial. To dive deeper into Formity's capabilities, you can continue with the following sections.

**Important:** The upcoming sections use a different setup. Do not combine them with the tutorial code. Instead, clone the following Github repository.

```bash
git clone https://github.com/martiserra99/formity-docs
```

This codebase is similar to the one in this tutorial, but without the submission logic.
