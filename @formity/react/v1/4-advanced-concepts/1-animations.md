---
title: Animations
nextjs:
  metadata:
    title: Animations - Docs
    description: Learn how to add animations to a multi-step form using Motion.
---

Learn how to add animations to a multi-step form using Motion.

---

## Initial steps{% id="initial-steps" %}

We'll show you how to add animations to a multi-step form using [Motion](https://www.framer.com/motion/). A pre-built form is available in the `formity-prev-docs-code` repository. Clone it if you haven't already, then navigate to the corresponding folder.

```bash
git clone https://github.com/martiserra99/formity-prev-docs-code
cd formity-prev-docs-code/@formity/react/v1/animations
```

Make sure you run the following command to install all the dependencies.

```bash
npm install
```

Additionally, you also need to install [Motion](https://www.framer.com/motion/) by doing the following.

```bash
npm install motion
```

## Key prop{% id="key-prop" %}

If you look at `schema.tsx`, you'll see that each `FormStep` receives a `key` prop. To animate form transitions, we need to use the `key` inside the `MultiStep` component.

We will update the `MultiStep` component with the following code.

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { OnNext, OnBack } from "@formity/react";

import { useMemo } from "react";

import type { FormityStatus } from "@/types";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  id: string;
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  status: FormityStatus;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  id,
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
    <div key={id} className="h-full">
      <MultiStepContext.Provider value={values}>
        {children}
      </MultiStepContext.Provider>
    </div>
  );
}
```

We will update the `schema.tsx` file with the following code.

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
        <MultiStep
          id="yourself"
          onNext={onNext}
          onBack={onBack}
          status={params.status}
        >
          <FormStep
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
        <MultiStep
          id="softwareDeveloper"
          onNext={onNext}
          onBack={onBack}
          status={params.status}
        >
          <FormStep
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
              <MultiStep
                id="expertise"
                onNext={onNext}
                onBack={onBack}
                status={params.status}
              >
                <FormStep
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
              <MultiStep
                id="interested"
                onNext={onNext}
                onBack={onBack}
                status={params.status}
              >
                <FormStep
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

## Animation{% id="animation" %}

After that, we need to use `motion` and `AnimatePresence` to ensure the form transition plays correctly when the `key` prop changes.

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { OnNext, OnBack } from "@formity/react";

import { useMemo } from "react";
import { AnimatePresence, motion } from "motion/react";

import type { FormityStatus } from "@/types";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  id: string;
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  status: FormityStatus;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  id,
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
    <AnimatePresence mode="popLayout" initial={false}>
      <motion.div
        key={id}
        initial={{ opacity: 0 }}
        animate={{
          opacity: 1,
          transition: { delay: 0.25, duration: 0.25 },
        }}
        exit={{
          opacity: 0,
          transition: { delay: 0, duration: 0.25 },
        }}
        className="h-full"
      >
        <MultiStepContext.Provider value={values}>
          {children}
        </MultiStepContext.Provider>
      </motion.div>
    </AnimatePresence>
  );
}
```

## Block interaction{% id="block-interaction" %}

While we are transitioning to the next form, we may want to block the interaction with the current form. To do it, we first need to update `types.ts` to include the `moving` property as shown below.

```ts
// types.ts
export type Status = FormityStatus | EndStatus;

export type FormityStatus = {
  type: "formity";
  moving: boolean;
  submitting: boolean;
};

export type EndStatus = {
  type: "end";
};
```

Then, in the `MultiStep` component we can update this property when navigating to the next or previous step, and use the `inert` prop to disable interaction with the current form while the transition is in progress.

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { OnNext, OnBack } from "@formity/react";

import { useMemo, useCallback } from "react";
import { AnimatePresence, motion } from "motion/react";

import type { FormityStatus } from "@/types";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  id: string;
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  status: FormityStatus;
  onChangeStatus: (status: FormityStatus) => void;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  id,
  onNext,
  onBack,
  status,
  onChangeStatus,
  children,
}: MultiStepProps<T>) {
  const handleNext = useCallback<OnNext<T>>(
    (values) => {
      onChangeStatus({ type: "formity", moving: true, submitting: false });
      onNext(values);
    },
    [onNext, onChangeStatus],
  );

  const handleBack = useCallback<OnBack<T>>(
    (values) => {
      onChangeStatus({ type: "formity", moving: true, submitting: false });
      onBack(values);
    },
    [onBack, onChangeStatus],
  );

  const values = useMemo(
    () => ({ onNext: handleNext, onBack: handleBack, status }),
    [handleNext, handleBack, status],
  ) as MultiStepValue<Record<string, unknown>>;

  return (
    <AnimatePresence
      mode="popLayout"
      initial={false}
      onExitComplete={() => {
        onChangeStatus({ type: "formity", moving: false, submitting: false });
      }}
    >
      <motion.div
        key={id}
        inert={status.moving}
        initial={{ opacity: 0 }}
        animate={{
          opacity: 1,
          transition: { delay: 0.25, duration: 0.25 },
        }}
        exit={{
          opacity: 0,
          transition: { delay: 0, duration: 0.25 },
        }}
        className="h-full"
      >
        <MultiStepContext.Provider value={values}>
          {children}
        </MultiStepContext.Provider>
      </motion.div>
    </AnimatePresence>
  );
}
```

We also need to update the schema so that we provide the `onChangeStatus` callback to the `MultiStep` component.

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
  onChangeStatus: (status: FormityStatus) => void;
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
        <MultiStep
          id="yourself"
          onNext={onNext}
          onBack={onBack}
          status={params.status}
          onChangeStatus={params.onChangeStatus}
        >
          <FormStep
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
        <MultiStep
          id="softwareDeveloper"
          onNext={onNext}
          onBack={onBack}
          status={params.status}
          onChangeStatus={params.onChangeStatus}
        >
          <FormStep
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
              <MultiStep
                id="expertise"
                onNext={onNext}
                onBack={onBack}
                status={params.status}
                onChangeStatus={params.onChangeStatus}
              >
                <FormStep
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
              <MultiStep
                id="interested"
                onNext={onNext}
                onBack={onBack}
                status={params.status}
                onChangeStatus={params.onChangeStatus}
              >
                <FormStep
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

After that, we need to update `app.tsx` with the code shown below.

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
    moving: false,
    submitting: false,
  });

  const onReturn = useCallback<OnReturn<Values>>(async (output) => {
    setStatus({ type: "formity", moving: false, submitting: true });

    // Show output in the console
    console.log(output);

    // Simulate a network request
    await new Promise((resolve) => setTimeout(resolve, 2000));

    setStatus({ type: "end" });
  }, []);

  if (status.type === "end") {
    return (
      <End
        onStart={() =>
          setStatus({ type: "formity", moving: false, submitting: false })
        }
      />
    );
  }

  return (
    <Formity<Values, Inputs, Params>
      schema={schema}
      params={{ status, onChangeStatus: (status) => setStatus(status) }}
      onReturn={onReturn}
    />
  );
}
```

With a slow animation, you'll notice the current form stays interactive during navigation. This happens because the `key` changes with the status, causing `inert` to only affect the next form. To fix it, we need to update the status before navigating.

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { OnNext, OnBack } from "@formity/react";

import { useMemo, useCallback } from "react";
import { AnimatePresence, motion } from "motion/react";

import type { FormityStatus } from "@/types";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  id: string;
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  status: FormityStatus;
  onChangeStatus: (status: FormityStatus) => void;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  id,
  onNext,
  onBack,
  status,
  onChangeStatus,
  children,
}: MultiStepProps<T>) {
  const handleNext = useCallback<OnNext<T>>(
    (values) => {
      onChangeStatus({ type: "formity", moving: true, submitting: false });
      setTimeout(() => onNext(values), 0);
    },
    [onNext, onChangeStatus],
  );

  const handleBack = useCallback<OnBack<T>>(
    (values) => {
      onChangeStatus({ type: "formity", moving: true, submitting: false });
      setTimeout(() => onBack(values), 0);
    },
    [onBack, onChangeStatus],
  );

  const values = useMemo(
    () => ({ onNext: handleNext, onBack: handleBack, status }),
    [handleNext, handleBack, status],
  ) as MultiStepValue<Record<string, unknown>>;

  return (
    <AnimatePresence
      mode="popLayout"
      initial={false}
      onExitComplete={() => {
        onChangeStatus({ type: "formity", moving: false, submitting: false });
      }}
    >
      <motion.div
        key={id}
        inert={status.moving}
        initial={{ opacity: 0 }}
        animate={{
          opacity: 1,
          transition: { delay: 0.25, duration: 0.25 },
        }}
        exit={{
          opacity: 0,
          transition: { delay: 0, duration: 0.25 },
        }}
        className="h-full"
      >
        <MultiStepContext.Provider value={values}>
          {children}
        </MultiStepContext.Provider>
      </motion.div>
    </AnimatePresence>
  );
}
```

## Animation direction{% id="animation-direction" %}

To play different animations when navigating forward or backward, we need to track the animation direction. For this reason, we need to update `types.ts` as shown below.

```ts
// types.ts
export type Status = FormityStatus | EndStatus;

export type FormityStatus = {
  type: "formity";
  moving: "next" | "back" | false;
  submitting: boolean;
};

export type EndStatus = {
  type: "end";
};
```

Then, we need to update the `MultiStep` component as shown below.

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { MotionProps } from "motion/react";
import type { OnNext, OnBack } from "@formity/react";

import { useMemo, useCallback } from "react";
import { AnimatePresence, motion } from "motion/react";

import type { FormityStatus } from "@/types";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  id: string;
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  status: FormityStatus;
  onChangeStatus: (status: FormityStatus) => void;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  id,
  onNext,
  onBack,
  status,
  onChangeStatus,
  children,
}: MultiStepProps<T>) {
  const handleNext = useCallback<OnNext<T>>(
    (values) => {
      onChangeStatus({ type: "formity", moving: "next", submitting: false });
      setTimeout(() => onNext(values), 0);
    },
    [onNext, onChangeStatus],
  );

  const handleBack = useCallback<OnBack<T>>(
    (values) => {
      onChangeStatus({ type: "formity", moving: "back", submitting: false });
      setTimeout(() => onBack(values), 0);
    },
    [onBack, onChangeStatus],
  );

  const values = useMemo(
    () => ({ onNext: handleNext, onBack: handleBack, status }),
    [handleNext, handleBack, status],
  ) as MultiStepValue<Record<string, unknown>>;

  return (
    <AnimatePresence
      mode="popLayout"
      initial={false}
      onExitComplete={() => {
        onChangeStatus({ type: "formity", moving: false, submitting: false });
      }}
    >
      <motion.div
        key={id}
        inert={Boolean(status.moving)}
        animate={{
          x: 0,
          opacity: 1,
          transition: { delay: 0.25, duration: 0.25 },
        }}
        {...motionProps(status.moving)}
        className="h-full"
      >
        <MultiStepContext.Provider value={values}>
          {children}
        </MultiStepContext.Provider>
      </motion.div>
    </AnimatePresence>
  );
}

function motionProps(animate: "next" | "back" | false): MotionProps {
  switch (animate) {
    case "next":
      return {
        initial: { x: 50, opacity: 0 },
        exit: {
          x: -50,
          opacity: 0,
          transition: { delay: 0, duration: 0.25 },
        },
      };
    case "back":
      return {
        initial: { x: -50, opacity: 0 },
        exit: {
          x: 50,
          opacity: 0,
          transition: { delay: 0, duration: 0.25 },
        },
      };
    default:
      return {};
  }
}
```

## Progress bar{% id="progress-bar" %}

We could also add a progress bar that is animated every time we go to a different step. To do it, we will create a `FormStepContainer` component as shown below.

```tsx
// components/form-step-container.tsx
import type { ReactNode } from "react";
import { motion } from "motion/react";

interface FormStepContainerProps {
  children: ReactNode;
  progress: {
    numberOfSteps: number;
    currentStep: number;
  };
}

export function FormStepContainer({
  children,
  progress,
}: FormStepContainerProps) {
  return (
    <div className="relative h-full">
      <div className="absolute inset-x-0 top-0 z-10 h-1 bg-blue-500/50">
        <motion.div
          initial={false}
          animate={{
            width: `${(progress.currentStep / progress.numberOfSteps) * 100}%`,
          }}
          className="h-full bg-blue-500"
        />
      </div>
      <>{children}</>
    </div>
  );
}
```

Now, we will be able to use this component in the schema.

```tsx
// schema.tsx
import type { Schema, Form, Return, Cond } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import { FormStepContainer } from "./components/form-step-container";

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
  onChangeStatus: (status: FormityStatus) => void;
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
        <FormStepContainer progress={{ numberOfSteps: 3, currentStep: 1 }}>
          <MultiStep
            id="yourself"
            onNext={onNext}
            onBack={onBack}
            status={params.status}
            onChangeStatus={params.onChangeStatus}
          >
            <FormStep
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
                    <TextInput
                      name="name"
                      label="Name"
                      placeholder="Your name"
                    />
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
        </FormStepContainer>
      ),
    },
  },
  {
    form: {
      values: () => ({
        softwareDeveloper: ["yes", []],
      }),
      render: ({ values, params, onNext, onBack }) => (
        <FormStepContainer progress={{ numberOfSteps: 3, currentStep: 2 }}>
          <MultiStep
            id="softwareDeveloper"
            onNext={onNext}
            onBack={onBack}
            status={params.status}
            onChangeStatus={params.onChangeStatus}
          >
            <FormStep
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
        </FormStepContainer>
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
              <FormStepContainer
                progress={{ numberOfSteps: 3, currentStep: 3 }}
              >
                <MultiStep
                  id="expertise"
                  onNext={onNext}
                  onBack={onBack}
                  status={params.status}
                  onChangeStatus={params.onChangeStatus}
                >
                  <FormStep
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
                            {
                              value: "frontend",
                              label: "Frontend development",
                            },
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
              </FormStepContainer>
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
              <FormStepContainer
                progress={{ numberOfSteps: 3, currentStep: 3 }}
              >
                <MultiStep
                  id="interested"
                  onNext={onNext}
                  onBack={onBack}
                  status={params.status}
                  onChangeStatus={params.onChangeStatus}
                >
                  <FormStep
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
              </FormStepContainer>
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
