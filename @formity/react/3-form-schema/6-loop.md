---
title: Loop
nextjs:
  metadata:
    title: Loop - Docs
    description: Learn how the loop element is used in the schema.
---

Learn how the loop element is used in the schema.

---

## Usage{% id="usage" %}

The **loop** element is used to define a loop.

To understand how it is used let's look at this example.

```tsx
import type { Schema, Form, Return, Variables, Loop } from "@formity/react";

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
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Variables<{
    i: number;
    questions: {
      question: string;
      options: { value: string; label: string }[];
      default: string;
      correct: string;
    }[];
    right: number;
    wrong: number;
  }>,
  Loop<
    [
      Variables<{
        question: {
          question: string;
          options: { value: string; label: string }[];
          default: string;
          correct: string;
        };
      }>,
      Form<{ answer: string }>,
      Variables<{ i: number; right: number; wrong: number }>,
    ]
  >,
  Return<{ right: number; wrong: number }>,
];

export const schema: Schema<Values> = [
  {
    variables: () => ({
      i: 0,
      questions: [
        {
          question: "What is the capital of Australia?",
          options: [
            { value: "sydney", label: "Sydney" },
            { value: "melbourne", label: "Melbourne" },
            { value: "canberra", label: "Canberra" },
          ],
          default: "sydney",
          correct: "canberra",
        },
        {
          question: "Who painted 'The Starry Night'?",
          options: [
            { value: "picasso", label: "Pablo Picasso" },
            { value: "vangogh", label: "Vincent van Gogh" },
            { value: "dali", label: "Salvador Dalí" },
          ],
          default: "picasso",
          correct: "vangogh",
        },
        {
          question: "Which planet is known as the 'Red Planet'?",
          options: [
            { value: "earth", label: "Earth" },
            { value: "mars", label: "Mars" },
            { value: "jupiter", label: "Jupiter" },
          ],
          default: "earth",
          correct: "mars",
        },
        {
          question: "What is water's symbol?",
          options: [
            { value: "h2o", label: "H₂O" },
            { value: "co2", label: "CO₂" },
            { value: "o2", label: "O₂" },
          ],
          default: "h2o",
          correct: "h2o",
        },
        {
          question: "Who wrote 'Romeo and Juliet'?",
          options: [
            { value: "shakespeare", label: "William Shakespeare" },
            { value: "dickens", label: "Charles Dickens" },
            { value: "twain", label: "Mark Twain" },
          ],
          default: "shakespeare",
          correct: "shakespeare",
        },
      ],
      right: 0,
      wrong: 0,
    }),
  },
  {
    loop: {
      while: ({ i, questions }) => i < questions.length,
      do: [
        {
          variables: ({ i, questions }) => ({
            question: questions[i],
          }),
        },
        {
          form: {
            values: ({ question, i }) => ({
              answer: [question.default, [i]],
            }),
            render: ({ inputs, values, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack}>
                <FormStep
                  key={`answer_${inputs.i}`}
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      answer: z.string(),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>
                      {inputs.question.question}
                    </FormStepHeading>
                    <FormStepInputs>
                      <Select
                        name="answer"
                        label="Answer"
                        options={inputs.question.options}
                      />
                    </FormStepInputs>
                    <FormStepRow>
                      {inputs.i > 0 ? <BackButton>Back</BackButton> : null}
                      {inputs.i < inputs.questions.length - 1 ? (
                        <NextButton>Next</NextButton>
                      ) : (
                        <NextButton>Submit</NextButton>
                      )}
                    </FormStepRow>
                  </FormStepContent>
                </FormStep>
              </MultiStep>
            ),
          },
        },
        {
          variables: ({ i, question, answer, right, wrong }) => ({
            i: i + 1,
            right: question.correct === answer ? right + 1 : right,
            wrong: question.correct === answer ? wrong : wrong + 1,
          }),
        },
      ],
    },
  },
  {
    return: ({ right, wrong }) => ({
      right,
      wrong,
    }),
  },
];
```

We need to use the `Loop` type with the corresponding types.

```tsx
export type Values = [
  // ...
  Loop<
    [
      Variables<{
        question: {
          question: string;
          options: { value: string; label: string }[];
          default: string;
          correct: string;
        };
      }>,
      Form<{ answer: string }>,
      Variables<{ i: number; right: number; wrong: number }>,
    ]
  >,
  // ...
];
```

Then, in the schema we need to create an object with the following structure.

```tsx
export const schema: Schema<Values> = [
  // ...
  {
    loop: {
      while: ({ i, questions }) => i < questions.length,
      do: [
        {
          variables: ({ i, questions }) => ({
            question: questions[i],
          }),
        },
        {
          form: {
            values: ({ question, i }) => ({
              answer: [question.default, [i]],
            }),
            render: ({ inputs, values, onNext, onBack }) => (
              <MultiStep onNext={onNext} onBack={onBack}>
                <FormStep
                  key={`answer_${inputs.i}`}
                  defaultValues={values}
                  resolver={zodResolver(
                    z.object({
                      answer: z.string(),
                    }),
                  )}
                >
                  <FormStepContent>
                    <FormStepHeading>
                      {inputs.question.question}
                    </FormStepHeading>
                    <FormStepInputs>
                      <Select
                        name="answer"
                        label="Answer"
                        options={inputs.question.options}
                      />
                    </FormStepInputs>
                    <FormStepRow>
                      {inputs.i > 0 ? <BackButton>Back</BackButton> : null}
                      {inputs.i < inputs.questions.length - 1 ? (
                        <NextButton>Next</NextButton>
                      ) : (
                        <NextButton>Submit</NextButton>
                      )}
                    </FormStepRow>
                  </FormStepContent>
                </FormStep>
              </MultiStep>
            ),
          },
        },
        {
          variables: ({ i, question, answer, right, wrong }) => ({
            i: i + 1,
            right: question.correct === answer ? right + 1 : right,
            wrong: question.correct === answer ? wrong : wrong + 1,
          }),
        },
      ],
    },
  },
  // ...
];
```

The `while` property is a function that takes the input values and returns a boolean value. While it is true, the elements in `do` are used.

Forms in a loop need a dynamic `key` to ensure a unique value. Moreover, a value must be passed in the array of the `values` function to avoid persistence across iterations.
