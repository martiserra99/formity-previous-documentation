# Condition

Learn how the condition element is used in the schema.

## Usage

The **condition** element is used to define a condition.

To understand how it is used let's look at this example.

```tsx
import type { Schema, Form, Return, Cond, Variables } from "@formity/react";

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
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<{ working: string }>,
  Variables<{ company: string | null }>,
  Cond<{
    then: [Form<{ company: string }>];
    else: [];
  }>,
  Form<{ study: string }>,
  Return<{
    working: string;
    company: string | null;
    study: string;
  }>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        working: ["yes", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="working"
            defaultValues={values}
            resolver={zodResolver(
              z.object({
                working: z.string(),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>Are you working?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="working"
                  label="Working"
                  options={[
                    { value: "yes", label: "Yes" },
                    { value: "no", label: "No" },
                  ]}
                />
              </FormStepInputs>
              <NextButton>Next</NextButton>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    variables: () => ({
      company: null,
    }),
  },
  {
    cond: {
      if: ({ working }) => working === "yes",
      then: [
        {
          form: {
            values: () => ({
              company: ["", []],
            }),
            render: ({ values, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack}>
                <FormStep
                  key="company"
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      company: z.string().min(1, "Required"),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>At what company?</FormStepHeading>
                    <FormStepInputs>
                      <TextInput
                        name="company"
                        label="Company"
                        placeholder="Company name"
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
      ],
      else: [],
    },
  },
  {
    form: {
      values: () => ({
        study: ["business", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="study"
            defaultValues={values}
            resolver={zodResolver(
              z.object({
                study: z.string(),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>What did you study?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="study"
                  label="Study"
                  options={[
                    { value: "business", label: "Business" },
                    { value: "health", label: "Health" },
                    { value: "technology", label: "Technology" },
                    { value: "education", label: "Education" },
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
    return: ({ working, company, study }) => ({
      working,
      company,
      study,
    }),
  },
];
```

We need to use the `Cond` type with the corresponding types.

```tsx
export type Values = [
  // ...
  Cond<{
    then: [Form<{ company: string }>];
    else: [];
  }>,
  // ...
];
```

Then, in the schema we need to create an object with the following structure.

```tsx
export const schema: Schema<Values> = [
  // ...
  {
    cond: {
      if: ({ working }) => working === "yes",
      then: [
        {
          form: {
            values: () => ({
              company: ["", []],
            }),
            render: ({ values, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack}>
                <FormStep
                  key="company"
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      company: z.string().min(1, "Required"),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>At what company?</FormStepHeading>
                    <FormStepInputs>
                      <TextInput
                        name="company"
                        label="Company"
                        placeholder="Company name"
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
      ],
      else: [],
    },
  },
  // ...
];
```

The `if` property is a function that takes the input values and returns a boolean value. If it is true, the elements in `then` are used. Otherwise, the elements in `else` are used.
