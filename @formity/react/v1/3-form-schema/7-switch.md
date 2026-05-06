---
title: Switch
nextjs:
  metadata:
    title: Switch - Docs
    description: Learn how the switch element is used in the schema.
---

Learn how the switch element is used in the schema.

---

## Usage{% id="usage" %}

The **switch** element is used to define multiple conditions.

To understand how it is used let's look at this example.

```tsx
import type { Schema, Form, Return, Switch } from "@formity/react";

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
  Form<{ interested: string }>,
  Switch<{
    branches: [
      [Form<{ whyYes: string }>, Return<{ interested: "yes"; whyYes: string }>],
      [Form<{ whyNot: string }>, Return<{ interested: "no"; whyNot: string }>],
    ];
    default: [
      Form<{ whyNotSure: string }>,
      Return<{ interested: "notSure"; whyNotSure: string }>,
    ];
  }>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        interested: ["yes", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
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
              <NextButton>Next</NextButton>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    switch: {
      branches: [
        {
          case: ({ interested }) => interested === "yes",
          then: [
            {
              form: {
                values: () => ({
                  whyYes: ["", []],
                }),
                render: ({ values, onNext, onBack }) => (
                  <MultiStep onNext={onNext} onBack={onBack}>
                    <FormStep
                      key="whyYes"
                      defaultValues={values}
                      resolver={zodResolver(
                        z.object({
                          whyYes: z.string().min(1, "Required"),
                        }),
                      )}
                    >
                      <FormStepContent>
                        <FormStepHeading>
                          Why are you interested?
                        </FormStepHeading>
                        <FormStepInputs>
                          <TextInput
                            name="whyYes"
                            label="Why?"
                            placeholder="Write your answer here..."
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
              return: ({ whyYes }) => ({
                interested: "yes",
                whyYes,
              }),
            },
          ],
        },
        {
          case: ({ interested }) => interested === "no",
          then: [
            {
              form: {
                values: () => ({
                  whyNot: ["", []],
                }),
                render: ({ values, onNext, onBack }) => (
                  <MultiStep onNext={onNext} onBack={onBack}>
                    <FormStep
                      key="whyNot"
                      defaultValues={values}
                      resolver={zodResolver(
                        z.object({
                          whyNot: z.string().min(1, "Required"),
                        }),
                      )}
                    >
                      <FormStepContent>
                        <FormStepHeading>
                          Why are you not interested?
                        </FormStepHeading>
                        <FormStepInputs>
                          <TextInput
                            name="whyNot"
                            label="Why?"
                            placeholder="Write your answer here..."
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
              return: ({ whyNot }) => ({
                interested: "no",
                whyNot,
              }),
            },
          ],
        },
      ],
      default: [
        {
          form: {
            values: () => ({
              whyNotSure: ["", []],
            }),
            render: ({ values, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack}>
                <FormStep
                  key="whyNotSure"
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      whyNotSure: z.string().min(1, "Required"),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>Why are you not sure?</FormStepHeading>
                    <FormStepInputs>
                      <TextInput
                        name="whyNotSure"
                        label="Why?"
                        placeholder="Write your answer here..."
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
          return: ({ whyNotSure }) => ({
            interested: "notSure",
            whyNotSure,
          }),
        },
      ],
    },
  },
];
```

We need to use the `Switch` type with the corresponding types.

```tsx
export type Values = [
  // ...
  Switch<{
    branches: [
      [Form<{ whyYes: string }>, Return<{ interested: "yes"; whyYes: string }>],
      [Form<{ whyNot: string }>, Return<{ interested: "no"; whyNot: string }>],
    ];
    default: [
      Form<{ whyNotSure: string }>,
      Return<{ interested: "notSure"; whyNotSure: string }>,
    ];
  }>,
];
```

Then, in the schema we need to create an object with the following structure.

```tsx
export const schema: Schema<Values> = [
  // ...
  {
    switch: {
      branches: [
        {
          case: ({ interested }) => interested === "yes",
          then: [
            {
              form: {
                values: () => ({
                  whyYes: ["", []],
                }),
                render: ({ values, onNext, onBack }) => (
                  <MultiStep onNext={onNext} onBack={onBack}>
                    <FormStep
                      key="whyYes"
                      defaultValues={values}
                      resolver={zodResolver(
                        z.object({
                          whyYes: z.string().min(1, "Required"),
                        }),
                      )}
                    >
                      <FormStepContent>
                        <FormStepHeading>
                          Why are you interested?
                        </FormStepHeading>
                        <FormStepInputs>
                          <TextInput
                            name="whyYes"
                            label="Why?"
                            placeholder="Write your answer here..."
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
              return: ({ whyYes }) => ({
                interested: "yes",
                whyYes,
              }),
            },
          ],
        },
        {
          case: ({ interested }) => interested === "no",
          then: [
            {
              form: {
                values: () => ({
                  whyNot: ["", []],
                }),
                render: ({ values, onNext, onBack }) => (
                  <MultiStep onNext={onNext} onBack={onBack}>
                    <FormStep
                      key="whyNot"
                      defaultValues={values}
                      resolver={zodResolver(
                        z.object({
                          whyNot: z.string().min(1, "Required"),
                        }),
                      )}
                    >
                      <FormStepContent>
                        <FormStepHeading>
                          Why are you not interested?
                        </FormStepHeading>
                        <FormStepInputs>
                          <TextInput
                            name="whyNot"
                            label="Why?"
                            placeholder="Write your answer here..."
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
              return: ({ whyNot }) => ({
                interested: "no",
                whyNot,
              }),
            },
          ],
        },
      ],
      default: [
        {
          form: {
            values: () => ({
              whyNotSure: ["", []],
            }),
            render: ({ values, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack}>
                <FormStep
                  key="whyNotSure"
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      whyNotSure: z.string().min(1, "Required"),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>Why are you not sure?</FormStepHeading>
                    <FormStepInputs>
                      <TextInput
                        name="whyNotSure"
                        label="Why?"
                        placeholder="Write your answer here..."
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
          return: ({ whyNotSure }) => ({
            interested: "notSure",
            whyNotSure,
          }),
        },
      ],
    },
  },
];
```

The `branches` property is an array of objects with two properties. The `case` property is a function that returns a boolean value. If it is true, the elements in `then` are used.

The first condition that evaluates to true is the one that is used. If no `case` evaluates to true, the elements in `default` are used.
