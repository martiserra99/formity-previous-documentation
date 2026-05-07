# Form state

Learn what the form state is about and how it can be used.

## Form state

In the form element's render function, the `getState` and `setState` functions are made available, allowing you to retrieve and modify the multi-step form's state.

It's recommended to access these functions via the Context API. To do that, we can provide them to the `MultiStep` component as shown below.

```tsx
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

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      render: ({ values, onNext, onBack, getState, setState }) => (
        <MultiStep
          onNext={onNext}
          onBack={onBack}
          getState={getState}
          setState={setState}
        >
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
      render: ({ values, onNext, onBack, getState, setState }) => (
        <MultiStep
          onNext={onNext}
          onBack={onBack}
          getState={getState}
          setState={setState}
        >
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
            render: ({ values, onNext, onBack, getState, setState }) => (
              <MultiStep
                onNext={onNext}
                onBack={onBack}
                getState={getState}
                setState={setState}
              >
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
            render: ({ values, onNext, onBack, getState, setState }) => (
              <MultiStep
                onNext={onNext}
                onBack={onBack}
                getState={getState}
                setState={setState}
              >
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

Then, we can update the files that are inside the `multi-step` folder as shown below.

`multi-step/multi-step-value.ts`:

```ts
// multi-step/multi-step-value.ts
import type { OnNext, OnBack, GetState, SetState } from "@formity/react";

export interface MultiStepValue<T extends Record<string, unknown>> {
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  getState: GetState<T>;
  setState: SetState;
}
```

`multi-step/multi-step.tsx`:

```tsx
// multi-step/multi-step.tsx
import type { ReactNode } from "react";
import type { OnNext, OnBack, GetState, SetState } from "@formity/react";

import { useMemo } from "react";

import type { MultiStepValue } from "./multi-step-value";
import { MultiStepContext } from "./multi-step-context";

interface MultiStepProps<T extends Record<string, unknown>> {
  onNext: OnNext<T>;
  onBack: OnBack<T>;
  getState: GetState<T>;
  setState: SetState;
  children: ReactNode;
}

export function MultiStep<T extends Record<string, unknown>>({
  onNext,
  onBack,
  getState,
  setState,
  children,
}: MultiStepProps<T>) {
  const values = useMemo(
    () => ({ onNext, onBack, getState, setState }),
    [onNext, onBack, getState, setState],
  ) as MultiStepValue<Record<string, unknown>>;
  return (
    <MultiStepContext.Provider value={values}>
      {children}
    </MultiStepContext.Provider>
  );
}
```

These functions are particularly useful in two main scenarios:

- **Saving state**: You can store the form state in local storage or another medium to let users continue later from the same point.

- **Jumping to steps**: Navigating forward or backward updates the state automatically, but jumping to a specific step requires a manual update.

Besides these functions, the `Formity` component also accepts an `initialState` prop, which can be used to define the starting state of the form.

```tsx
import { useCallback, useState } from "react";

import {
  Formity,
  type OnReturn,
  type ReturnOutput,
  type State,
} from "@formity/react";

import { Output } from "./components/output";

import { schema, type Values } from "./schema";

const initialState: State = {
  points: [
    {
      path: [{ type: "list", slot: 0 }],
      values: {},
    },
    {
      path: [{ type: "list", slot: 1 }],
      values: { name: "Marti", surname: "Serra", age: 25 },
    },
  ],
  inputs: {
    type: "list",
    list: {
      0: {
        name: { data: { here: true, data: "Marti" }, keys: {} },
        surname: { data: { here: true, data: "Serra" }, keys: {} },
        age: { data: { here: true, data: 25 }, keys: {} },
      },
    },
  },
};

export default function App() {
  const [output, setOutput] = useState<ReturnOutput<Values> | null>(null);

  const onReturn = useCallback<OnReturn<Values>>((output) => {
    setOutput(output);
  }, []);

  if (output) {
    return <Output output={output} onStart={() => setOutput(null)} />;
  }

  return (
    <Formity<Values>
      schema={schema}
      onReturn={onReturn}
      initialState={initialState}
    />
  );
}
```

When using this prop, it's common to pass in a previously saved state rather than defining it manually. This allows you to resume from the last completed step.

## Structure

**The state structure is managed internally by Formity** and is designed to work automatically without requiring your involvement. In most cases, you won’t need to understand or modify it.

You can safely ignore this section unless you need advanced behavior like jumping between steps. In that case, I recommend you to see the _Jump to steps_ section instead.

The state object is of type `State`, and its structure is as follows.

```tsx
type State = {
  points: Point[];
  inputs: Inputs;
};
```

The `points` property is an array of `Point` objects. Each defines the position of a form or yield element encountered and the input values that exist at this point. The last point represents the current form's position.

A `Point` includes:

- `path`: The position of a form or yield element encountered.
- `values`: The input values that exist at this point.

```tsx
type Point = {
  path: Position[];
  values: Record<string, unknown>;
};

type Position = ListPosition | CondPosition | LoopPosition | SwitchPosition;

type ListPosition = {
  type: "list";
  slot: number;
};

type CondPosition = {
  type: "cond";
  path: "then" | "else";
  slot: number;
};

type LoopPosition = {
  type: "loop";
  slot: number;
};

type SwitchPosition = {
  type: "switch";
  branch: number; // -1 if default branch
  slot: number;
};
```

The `inputs` property stores all values entered in the multi-step form to ensure that when you return to the same step, your data is preserved. It's of type `Inputs`, and the way it is structured is as follows.

```tsx
type Inputs = ListInputs;

type ItemInputs = FlowInputs | FormInputs;

type FlowInputs = ListInputs | CondInputs | LoopInputs | SwitchInputs;

type ListInputs = {
  type: "list";
  list: { [position: number]: ItemInputs };
};

type CondInputs = {
  type: "cond";
  then: { [position: number]: ItemInputs };
  else: { [position: number]: ItemInputs };
};

type LoopInputs = {
  type: "loop";
  list: { [position: number]: ItemInputs };
};

type SwitchInputs = {
  type: "switch";
  branches: { [position: number]: { [position: number]: ItemInputs } };
  default: { [position: number]: ItemInputs };
};

type FormInputs = { [key: string]: NameInputs };

type NameInputs = {
  data: { here: true; data: unknown } | { here: false };
  keys: { [key: PropertyKey]: NameInputs };
};
```

When a form value is stored, the `inputs` property captures the positions related to the corresponding form, along with its name and value.

If the value is defined as a non-empty array in the form element's `values` function, each item is recursively mapped, and the form value is stored at the deepest level.
