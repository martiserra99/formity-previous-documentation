---
title: Jump to steps
nextjs:
  metadata:
    title: Jump to steps - Docs
    description: Learn how to jump to specific steps by updating the state.
---

Learn how to jump to specific steps by updating the state.

---

## Multi-step form{% id="multi-step-form" %}

We can jump to specific steps by updating the state of the multi-step form. To add this functionality, we will create the multi-step form in a different way, and we'll start by updating the `schema.tsx` file with the following code.

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

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({}),
      render: ({ onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="nameSurname"
            defaultValues={{ name: "", surname: "" }}
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
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="age"
            defaultValues={{ age: 0 }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="gender"
            defaultValues={{ gender: "" }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="country"
            defaultValues={{ country: "" }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
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
];
```

As you may have noticed, the form values are not provided using the `values` function. That's because we'll handle the form values in a different way.

## Form fields{% id="form-fields" %}

We'll create a `fields` object with all the values of all the forms of the multi-step form. This object will be passed using the `params` prop of the `Formity` component.

To do it, we first need to update the `schema.tsx` file as shown below.

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

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export type Inputs = Record<never, never>;

export type Params = {
  fields: Fields;
};

export type Fields = {
  name: string;
  surname: string;
  age: number;
  gender: string;
  country: string;
};

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="nameSurname"
            defaultValues={{
              name: params.fields.name,
              surname: params.fields.surname,
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
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="age"
            defaultValues={{ age: params.fields.age }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="gender"
            defaultValues={{ gender: params.fields.gender }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="country"
            defaultValues={{ country: params.fields.country }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
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
];
```

Then, we can update the `app.tsx` file as you can see here.

```tsx
// app.tsx
import { useState } from "react";

import { Formity } from "@formity/react";

import {
  schema,
  type Values,
  type Inputs,
  type Params,
  type Fields,
} from "./schema";

const initialFields: Fields = {
  name: "",
  surname: "",
  age: 0,
  gender: "",
  country: "",
};

export default function App() {
  const [fields] = useState(initialFields);
  return (
    <Formity<Values, Inputs, Params> schema={schema} params={{ fields }} />
  );
}
```

## Field changes{% id="field-changes" %}

We need to update the `fields` object every time form values are changed. To do it, we need to include an `onChange` prop to the `FormStep` component as shown below.

```tsx
// components/form-step.tsx
import type { ReactNode } from "react";
import type { DefaultValues, Resolver } from "react-hook-form";
import type { Fields } from "@/schema";

import { useEffect } from "react";
import { FormProvider, useForm } from "react-hook-form";
import { useMultiStep } from "@/multi-step";

interface FormStepProps<T extends Record<string, unknown>> {
  defaultValues: DefaultValues<T>;
  resolver: Resolver<T>;
  children: ReactNode;
  onChange: (fields: Partial<Fields>) => void;
}

export function FormStep<T extends Record<string, unknown>>({
  defaultValues,
  resolver,
  children,
  onChange,
}: FormStepProps<T>) {
  const form = useForm({ defaultValues, resolver });
  const { onNext } = useMultiStep();

  useEffect(() => {
    const { unsubscribe } = form.watch((values) => {
      const fields = values as Partial<Fields>;
      onChange(fields);
    });
    return () => unsubscribe();
  }, [form, onChange]);

  return (
    <form
      onSubmit={form.handleSubmit(onNext)}
      className="flex h-screen w-full items-center justify-center px-4 py-8 font-sans"
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

The function will be passed using the `params` prop of the `Formity` component. For this reason, we need to update the `schema.tsx` file as shown below.

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

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export type Inputs = Record<never, never>;

export type Params = {
  fields: Fields;
  onChange: (fields: Partial<Fields>) => void;
};

export type Fields = {
  name: string;
  surname: string;
  age: number;
  gender: string;
  country: string;
};

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="nameSurname"
            defaultValues={{
              name: params.fields.name,
              surname: params.fields.surname,
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
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="age"
            defaultValues={{ age: params.fields.age }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="gender"
            defaultValues={{ gender: params.fields.gender }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="country"
            defaultValues={{ country: params.fields.country }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
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
];
```

Then, we can update the `app.tsx` file as you can see here.

```tsx
// app.tsx
import { useState, useCallback } from "react";

import { Formity } from "@formity/react";

import {
  schema,
  type Values,
  type Inputs,
  type Params,
  type Fields,
} from "./schema";

const initialFields: Fields = {
  name: "",
  surname: "",
  age: 0,
  gender: "",
  country: "",
};

export default function App() {
  const [fields, setFields] = useState(initialFields);

  const onChange = useCallback(
    (values: Partial<Fields>) => {
      setFields({ ...fields, ...values });
    },
    [fields],
  );

  return (
    <Formity<Values, Inputs, Params>
      schema={schema}
      params={{ fields, onChange }}
    />
  );
}
```

## Submit form{% id="submit-form" %}

We need to pass an `onSubmit` function using the `params` prop of `Formity`. It will be called on the last step, and to do it we need to update `schema.tsx` as shown below.

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

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export type Inputs = Record<never, never>;

export type Params = {
  fields: Fields;
  onChange: (fields: Partial<Fields>) => void;
  onSubmit: () => void;
};

export type Fields = {
  name: string;
  surname: string;
  age: number;
  gender: string;
  country: string;
};

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="nameSurname"
            defaultValues={{
              name: params.fields.name,
              surname: params.fields.surname,
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
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="age"
            defaultValues={{ age: params.fields.age }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="gender"
            defaultValues={{ gender: params.fields.gender }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ params, onBack }) => (
        <MultiStep onNext={params.onSubmit} onBack={onBack}>
          <FormStep
            key="country"
            defaultValues={{ country: params.fields.country }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
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
];
```

Then, we can update the `app.tsx` file as you can see here.

```tsx
// app.tsx
import { useState, useCallback } from "react";

import { Formity } from "@formity/react";

import {
  schema,
  type Values,
  type Inputs,
  type Params,
  type Fields,
} from "./schema";

import { Output } from "./components/output";

const initialFields: Fields = {
  name: "",
  surname: "",
  age: 0,
  gender: "",
  country: "",
};

export default function App() {
  const [fields, setFields] = useState(initialFields);
  const [submit, setSubmit] = useState(false);

  const onChange = useCallback(
    (values: Partial<Fields>) => {
      setFields({ ...fields, ...values });
    },
    [fields],
  );

  const onSubmit = useCallback(() => {
    setSubmit(true);
  }, []);

  if (submit) {
    return (
      <Output
        output={fields}
        onStart={() => {
          setSubmit(false);
          setFields(initialFields);
        }}
      />
    );
  }

  return (
    <Formity<Values, Inputs, Params>
      schema={schema}
      params={{ fields, onChange, onSubmit }}
    />
  );
}
```

## Steps component{% id="steps-component" %}

We'll create a `Steps` component used to navigate to specific steps, and we will start by creating a `components/steps.tsx` file with the following code.

```tsx
// components/steps.tsx
import { tv } from "tailwind-variants";

interface StepsProps {
  steps: {
    label: string;
  }[];
  selected: number;
}

export function Steps({ steps, selected }: StepsProps) {
  return (
    <div className="fixed right-4 top-5 z-10 flex gap-3">
      {steps.map((step, index) => (
        <Step key={index} label={step.label} selected={index === selected} />
      ))}
    </div>
  );
}

const step = tv({
  base: "flex h-10 w-10 items-center justify-center rounded-full border border-neutral-800 text-sm text-white ring-offset-2 ring-offset-neutral-950 hover:bg-neutral-800 focus:outline-none focus:ring-2 focus:ring-white/10",
  variants: {
    selected: {
      false: "bg-neutral-950",
      true: "bg-neutral-800 ring-2 ring-neutral-800",
    },
  },
});

interface StepProps {
  label: string;
  selected: boolean;
}

function Step({ label, selected }: StepProps) {
  return (
    <button type="button" className={step({ selected })}>
      {label}
    </button>
  );
}
```

Then, we'll update `schema.tsx` to include the `Steps` component.

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

import { Steps } from "./components/steps";
import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export type Inputs = Record<never, never>;

export type Params = {
  fields: Fields;
  onChange: (fields: Partial<Fields>) => void;
  onSubmit: () => void;
};

export type Fields = {
  name: string;
  surname: string;
  age: number;
  gender: string;
  country: string;
};

const steps: { label: string }[] = [
  { label: "1" },
  { label: "2" },
  { label: "3" },
  { label: "4" },
];

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="nameSurname"
            defaultValues={{
              name: params.fields.name,
              surname: params.fields.surname,
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
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={0} />
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="age"
            defaultValues={{ age: params.fields.age }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={1} />
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="gender"
            defaultValues={{ gender: params.fields.gender }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={2} />
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ params, onBack }) => (
        <MultiStep onNext={params.onSubmit} onBack={onBack}>
          <FormStep
            key="country"
            defaultValues={{ country: params.fields.country }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={3} />
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
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
];
```

## Completed steps{% id="completed-steps" %}

The `Steps` component should indicate what are the steps that have been completed. To do it, we'll use validation rules to check what are the completed steps.

We'll update `Steps` to include a `check` property in the object of the `steps` array.

```tsx
// components/steps.tsx
import type { ZodType } from "zod";
import { tv } from "tailwind-variants";

import type { Fields } from "@/schema";

interface StepsProps {
  steps: {
    label: string;
    check: ZodType;
  }[];
  selected: number;
  fields: Fields;
}

export function Steps({ steps, selected, fields }: StepsProps) {
  return (
    <div className="fixed right-4 top-5 z-10 flex gap-3">
      {steps.map((step, index) => (
        <Step
          key={index}
          label={step.label}
          check={step.check}
          selected={index === selected}
          fields={fields}
        />
      ))}
    </div>
  );
}

const step = tv({
  base: "flex h-10 w-10 items-center justify-center rounded-full border border-neutral-800 bg-neutral-950 text-sm text-white ring-offset-2 ring-offset-neutral-950 hover:bg-neutral-800 focus:outline-none focus:ring-2 focus:ring-white/10",
  variants: {
    selected: {
      true: "bg-neutral-800 ring-2 ring-neutral-800",
    },
    check: {
      true: "bg-neutral-800",
    },
  },
});

interface StepProps {
  label: string;
  check: ZodType;
  selected: boolean;
  fields: Fields;
}

function Step({ label, check, selected, fields }: StepProps) {
  return (
    <button
      type="button"
      className={step({ selected, check: check.safeParse(fields).success })}
    >
      {label}
    </button>
  );
}
```

Then, we'll update `schema.tsx` to include the validation rules in the `steps` array.

```tsx
// schema.tsx
import type { Schema, Form } from "@formity/react";
import type { ZodType } from "zod";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Steps } from "./components/steps";
import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export type Inputs = Record<never, never>;

export type Params = {
  fields: Fields;
  onChange: (fields: Partial<Fields>) => void;
  onSubmit: () => void;
};

export type Fields = {
  name: string;
  surname: string;
  age: number;
  gender: string;
  country: string;
};

const steps: { label: string; check: ZodType }[] = [
  {
    label: "1",
    check: z.object({
      name: z
        .string()
        .min(1, { message: "Required" })
        .max(20, { message: "Must be at most 20 characters" }),
      surname: z
        .string()
        .min(1, { message: "Required" })
        .max(20, { message: "Must be at most 20 characters" }),
    }),
  },
  {
    label: "2",
    check: z.object({
      age: z
        .number()
        .min(18, { message: "Minimum of 18 years old" })
        .max(99, { message: "Maximum of 99 years old" }),
    }),
  },
  {
    label: "3",
    check: z.object({
      gender: z.string().nonempty({ message: "Required" }),
    }),
  },
  {
    label: "4",
    check: z.object({
      country: z.string().nonempty({ message: "Required" }),
    }),
  },
];

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="nameSurname"
            defaultValues={{
              name: params.fields.name,
              surname: params.fields.surname,
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
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={0} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="age"
            defaultValues={{ age: params.fields.age }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={1} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="gender"
            defaultValues={{ gender: params.fields.gender }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={2} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ params, onBack }) => (
        <MultiStep onNext={params.onSubmit} onBack={onBack}>
          <FormStep
            key="country"
            defaultValues={{ country: params.fields.country }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={3} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
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
];
```

## Jump to steps{% id="jump-to-steps" %}

To jump to steps, we need to use the `setState` function. We'll access this function using the Context API, so we'll need to update the files in the `multi-step` folder.

`multi-step/multi-step-value.ts`:

```tsx
// multi-step/multi-step-value.ts
import type { OnNext, OnBack, SetState } from "@formity/react";

export interface MultiStepValue<T extends Record<string, unknown>> {
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  setState: SetState;
}
```

`multi-step/multi-step.tsx`:

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { OnNext, OnBack, SetState } from "@formity/react";

import { useMemo } from "react";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  setState: SetState;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  onNext,
  onBack,
  setState,
  children,
}: MultiStepProps<T>) {
  const values = useMemo(
    () => ({ onNext, onBack, setState }),
    [onNext, onBack, setState],
  ) as MultiStepValue<Record<string, unknown>>;
  return (
    <MultiStepContext.Provider value={values}>
      {children}
    </MultiStepContext.Provider>
  );
}
```

Then, we'll update `Steps` to include a `state` property in the object of the `steps` array. When we click a step, the `setState` will be called with the corresponding state.

```tsx
// components/steps.tsx
import type { ZodType } from "zod";
import type { State } from "@formity/react";
import { tv } from "tailwind-variants";

import type { Fields } from "@/schema";
import { useMultiStep } from "@/multi-step";

interface StepsProps {
  steps: {
    label: string;
    check: ZodType;
    state: State;
  }[];
  selected: number;
  fields: Fields;
}

export function Steps({ steps, selected, fields }: StepsProps) {
  return (
    <div className="fixed right-4 top-5 z-10 flex gap-3">
      {steps.map((step, index) => (
        <Step
          key={index}
          label={step.label}
          check={step.check}
          state={step.state}
          selected={index === selected}
          fields={fields}
        />
      ))}
    </div>
  );
}

const step = tv({
  base: "flex h-10 w-10 items-center justify-center rounded-full border border-neutral-800 bg-neutral-950 text-sm text-white ring-offset-2 ring-offset-neutral-950 hover:bg-neutral-800 focus:outline-none focus:ring-2 focus:ring-white/10",
  variants: {
    selected: {
      true: "bg-neutral-800 ring-2 ring-neutral-800",
    },
    check: {
      true: "bg-neutral-800",
    },
  },
});

interface StepProps {
  label: string;
  check: ZodType;
  state: State;
  selected: boolean;
  fields: Fields;
}

function Step({ label, check, state, selected, fields }: StepProps) {
  const { setState } = useMultiStep();
  return (
    <button
      type="button"
      onClick={() => setState(state)}
      className={step({ selected, check: check.safeParse(fields).success })}
    >
      {label}
    </button>
  );
}
```

After that, we'll update `schema.tsx` to include the states in the `steps` array and to pass the `setState` function to the `MultiStep` component.

```tsx
// schema.tsx
import type { Schema, Form, State } from "@formity/react";
import type { ZodType } from "zod";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Steps } from "./components/steps";
import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export type Inputs = Record<never, never>;

export type Params = {
  fields: Fields;
  onChange: (fields: Partial<Fields>) => void;
  onSubmit: () => void;
};

export type Fields = {
  name: string;
  surname: string;
  age: number;
  gender: string;
  country: string;
};

const steps: { label: string; check: ZodType; state: State }[] = [
  {
    label: "1",
    check: z.object({
      name: z
        .string()
        .min(1, { message: "Required" })
        .max(20, { message: "Must be at most 20 characters" }),
      surname: z
        .string()
        .min(1, { message: "Required" })
        .max(20, { message: "Must be at most 20 characters" }),
    }),
    state: {
      points: [{ path: [{ type: "list", slot: 0 }], values: {} }],
      inputs: { type: "list", list: [] },
    },
  },
  {
    label: "2",
    check: z.object({
      age: z
        .number()
        .min(18, { message: "Minimum of 18 years old" })
        .max(99, { message: "Maximum of 99 years old" }),
    }),
    state: {
      points: [
        { path: [{ type: "list", slot: 0 }], values: {} },
        { path: [{ type: "list", slot: 1 }], values: {} },
      ],
      inputs: { type: "list", list: [] },
    },
  },
  {
    label: "3",
    check: z.object({
      gender: z.string().nonempty({ message: "Required" }),
    }),
    state: {
      points: [
        { path: [{ type: "list", slot: 0 }], values: {} },
        { path: [{ type: "list", slot: 1 }], values: {} },
        { path: [{ type: "list", slot: 2 }], values: {} },
      ],
      inputs: { type: "list", list: [] },
    },
  },
  {
    label: "4",
    check: z.object({
      country: z.string().nonempty({ message: "Required" }),
    }),
    state: {
      points: [
        { path: [{ type: "list", slot: 0 }], values: {} },
        { path: [{ type: "list", slot: 1 }], values: {} },
        { path: [{ type: "list", slot: 2 }], values: {} },
        { path: [{ type: "list", slot: 3 }], values: {} },
      ],
      inputs: { type: "list", list: [] },
    },
  },
];

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack, setState }) => (
        <MultiStep onNext={onNext} onBack={onBack} setState={setState}>
          <FormStep
            key="nameSurname"
            defaultValues={{
              name: params.fields.name,
              surname: params.fields.surname,
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
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={0} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ params, onNext, onBack, setState }) => (
        <MultiStep onNext={onNext} onBack={onBack} setState={setState}>
          <FormStep
            key="age"
            defaultValues={{ age: params.fields.age }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={1} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack, setState }) => (
        <MultiStep onNext={onNext} onBack={onBack} setState={setState}>
          <FormStep
            key="gender"
            defaultValues={{ gender: params.fields.gender }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={2} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ params, onBack, setState }) => (
        <MultiStep onNext={params.onSubmit} onBack={onBack} setState={setState}>
          <FormStep
            key="country"
            defaultValues={{ country: params.fields.country }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={3} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
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
];
```

## Block submit{% id="disable-button" %}

Lastly, we need to disable the button on the last step when there are uncompleted steps. To do that, we'll update the `schema.tsx` file as shown below.

```tsx
// schema.tsx
import type { Schema, Form, State } from "@formity/react";
import type { ZodType } from "zod";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Steps } from "./components/steps";
import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
  Form<Record<never, never>>,
];

export type Inputs = Record<never, never>;

export type Params = {
  fields: Fields;
  onChange: (fields: Partial<Fields>) => void;
  onSubmit: () => void;
};

export type Fields = {
  name: string;
  surname: string;
  age: number;
  gender: string;
  country: string;
};

const steps: { label: string; check: ZodType; state: State }[] = [
  {
    label: "1",
    check: z.object({
      name: z
        .string()
        .min(1, { message: "Required" })
        .max(20, { message: "Must be at most 20 characters" }),
      surname: z
        .string()
        .min(1, { message: "Required" })
        .max(20, { message: "Must be at most 20 characters" }),
    }),
    state: {
      points: [{ path: [{ type: "list", slot: 0 }], values: {} }],
      inputs: { type: "list", list: [] },
    },
  },
  {
    label: "2",
    check: z.object({
      age: z
        .number()
        .min(18, { message: "Minimum of 18 years old" })
        .max(99, { message: "Maximum of 99 years old" }),
    }),
    state: {
      points: [
        { path: [{ type: "list", slot: 0 }], values: {} },
        { path: [{ type: "list", slot: 1 }], values: {} },
      ],
      inputs: { type: "list", list: [] },
    },
  },
  {
    label: "3",
    check: z.object({
      gender: z.string().nonempty({ message: "Required" }),
    }),
    state: {
      points: [
        { path: [{ type: "list", slot: 0 }], values: {} },
        { path: [{ type: "list", slot: 1 }], values: {} },
        { path: [{ type: "list", slot: 2 }], values: {} },
      ],
      inputs: { type: "list", list: [] },
    },
  },
  {
    label: "4",
    check: z.object({
      country: z.string().nonempty({ message: "Required" }),
    }),
    state: {
      points: [
        { path: [{ type: "list", slot: 0 }], values: {} },
        { path: [{ type: "list", slot: 1 }], values: {} },
        { path: [{ type: "list", slot: 2 }], values: {} },
        { path: [{ type: "list", slot: 3 }], values: {} },
      ],
      inputs: { type: "list", list: [] },
    },
  },
];

export const schema: Schema<Values, Inputs, Params> = [
  {
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack, setState }) => (
        <MultiStep onNext={onNext} onBack={onBack} setState={setState}>
          <FormStep
            key="nameSurname"
            defaultValues={{
              name: params.fields.name,
              surname: params.fields.surname,
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
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={0} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your name?</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
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
      values: () => ({}),
      render: ({ params, onNext, onBack, setState }) => (
        <MultiStep onNext={onNext} onBack={onBack} setState={setState}>
          <FormStep
            key="age"
            defaultValues={{ age: params.fields.age }}
            resolver={zodResolver(
              z.object({
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={1} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your age?</FormStepHeading>
              <FormStepInputs>
                <NumberInput name="age" label="Age" placeholder="Your age" />
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
    form: {
      values: () => ({}),
      render: ({ params, onNext, onBack, setState }) => (
        <MultiStep onNext={onNext} onBack={onBack} setState={setState}>
          <FormStep
            key="gender"
            defaultValues={{ gender: params.fields.gender }}
            resolver={zodResolver(
              z.object({
                gender: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={2} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your gender?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="gender"
                  label="Gender"
                  options={[
                    { value: "", label: "Select your gender" },
                    { value: "man", label: "Man" },
                    { value: "woman", label: "Woman" },
                    { value: "other", label: "Other" },
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
    form: {
      values: () => ({}),
      render: ({ params, onBack, setState }) => (
        <MultiStep onNext={params.onSubmit} onBack={onBack} setState={setState}>
          <FormStep
            key="country"
            defaultValues={{ country: params.fields.country }}
            resolver={zodResolver(
              z.object({
                country: z.string().nonempty({ message: "Required" }),
              }),
            )}
            onChange={params.onChange}
          >
            <Steps steps={steps} selected={3} fields={params.fields} />
            <FormStepContent>
              <FormStepHeading>What is your country?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="country"
                  label="Country"
                  options={[
                    { value: "", label: "Select your country" },
                    { value: "spain", label: "Spain" },
                    { value: "france", label: "France" },
                    { value: "germany", label: "Germany" },
                    { value: "italy", label: "Italy" },
                  ]}
                />
              </FormStepInputs>
              <FormStepRow>
                <BackButton>Back</BackButton>
                <NextButton
                  disabled={steps.some((step) => {
                    return !step.check.safeParse(params.fields).success;
                  })}
                >
                  Submit
                </NextButton>
              </FormStepRow>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
];
```
