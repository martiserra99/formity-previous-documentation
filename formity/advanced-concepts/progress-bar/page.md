# Progress bar

Learn how to add a progress bar in these forms.

## Initial steps

There is a really simple way to add a progress bar in these forms. To learn how to do it, clone the following GitHub repository so that you don't need to start from scratch.

```bash
git clone https://github.com/martiserra99/formity-previous-progress-bar
```

Make sure you run the following command to install all the dependencies:

```bash
npm install
```

## Create progress bar

To add the progress bar we will create the `components/screen.tsx` file with the following component:

```tsx
// src/components/screen.tsx

import { motion } from "framer-motion";

interface ScreenProps {
  progress: { total: number; current: number };
  children: React.ReactNode;
}

export default function Screen({ progress, children }: ScreenProps) {
  return (
    <div className="relative h-full w-full">
      <Progress total={progress.total} current={progress.current} />
      {children}
    </div>
  );
}

interface ProgressProps {
  total: number;
  current: number;
}

function Progress({ total, current }: ProgressProps) {
  return (
    <div className="absolute left-0 right-0 top-0 h-1 bg-indigo-500/50">
      <motion.div
        className="h-full bg-indigo-500"
        animate={{ width: `${(current / total) * 100}%` }}
        initial={false}
      />
    </div>
  );
}
```

The `Screen` component is a wrapper for the form and it contains the progress bar.

```tsx
// ...

interface ScreenProps {
  progress: { total: number; current: number };
  children: React.ReactNode;
}

export default function Screen({ progress, children }: ScreenProps) {
  return (
    <div className="relative h-full w-full">
      <Progress total={progress.total} current={progress.current} />
      {children}
    </div>
  );
}

// ...
```

The `Progress` component is the progress bar, and it receives two props: the total number of steps in the form and the step that we are currently on.

```tsx
// ...

interface ProgressProps {
  total: number;
  current: number;
}

function Progress({ total, current }: ProgressProps) {
  return (
    <div className="absolute left-0 right-0 top-0 h-1 bg-indigo-500/50">
      <motion.div
        className="h-full bg-indigo-500"
        animate={{ width: `${(current / total) * 100}%` }}
        initial={false}
      />
    </div>
  );
}
```

As we can see, we are using [Framer Motion](https://www.framer.com/motion/) to animate the progress bar when we go to the next or previous step.

After creating the component, we need to update the `components.tsx` file to add this new component:

```tsx
// src/components.tsx

// ...

import Screen from "@/components/screen";
// ...

type Parameters = {
  screen: {
    progress: { total: number; current: number };
    children: Value;
  };
  // ...
};

const components: Components<Parameters> = {
  screen: ({ progress, children }, render) => (
    <Screen progress={progress}>{render(children)}</Screen>
  ),
  // ...
};

export default components;
```

## Create schema

Now that we have the component created, we can use it in the schema. We can update the `schema.ts` file and use the screen component to show the progress bar:

```tsx
// src/schema.ts

import { Schema } from "formity";

const schema: Schema = [
  {
    form: {
      defaultValues: {
        name: ["", []],
      },
      resolver: {
        name: [[{ "#$ne": ["#$name", ""] }, "Required"]],
      },
      render: {
        screen: {
          progress: { total: 3, current: 1 },
          children: {
            form: {
              step: "$step",
              defaultValues: "$defaultValues",
              resolver: "$resolver",
              onNext: "$onNext",
              children: {
                formLayout: {
                  heading: "What is your name?",
                  description: "We would want to know your name",
                  fields: [
                    {
                      textField: {
                        name: "name",
                        label: "Name",
                      },
                    },
                  ],
                  button: {
                    next: { text: "Next" },
                  },
                },
              },
            },
          },
        },
      },
    },
  },
  {
    form: {
      defaultValues: {
        surname: ["", []],
      },
      resolver: {
        surname: [[{ "#$ne": ["#$surname", ""] }, "Required"]],
      },
      render: {
        screen: {
          progress: { total: 3, current: 2 },
          children: {
            form: {
              step: "$step",
              defaultValues: "$defaultValues",
              resolver: "$resolver",
              onNext: "$onNext",
              children: {
                formLayout: {
                  heading: "What is your surname?",
                  description: "We would want to know your surname",
                  fields: [
                    {
                      textField: {
                        name: "surname",
                        label: "Surname",
                      },
                    },
                  ],
                  button: {
                    next: { text: "Next" },
                  },
                  back: {
                    back: { onBack: "$onBack" },
                  },
                },
              },
            },
          },
        },
      },
    },
  },
  {
    form: {
      defaultValues: {
        age: [18, []],
      },
      resolver: {
        age: [[{ "#$ne": ["#$age", ""] }, "Required"]],
      },
      render: {
        screen: {
          progress: { total: 3, current: 3 },
          children: {
            form: {
              step: "$step",
              defaultValues: "$defaultValues",
              resolver: "$resolver",
              onNext: "$onNext",
              children: {
                formLayout: {
                  heading: "What is your age?",
                  description: "We would want to know your age",
                  fields: [
                    {
                      textField: {
                        name: "age",
                        label: "Age",
                      },
                    },
                  ],
                  button: {
                    next: { text: "Next" },
                  },
                  back: {
                    back: { onBack: "$onBack" },
                  },
                },
              },
            },
          },
        },
      },
    },
  },
  {
    return: {
      name: "$name",
      surname: "$surname",
      age: "$age",
    },
  },
];

export default schema;
```

If the form has conditions or loops, we can't know the number of steps the user will go through to complete the form. In that case, we can pass the minimum number of steps that we know for sure the user will go through to the `total` prop.
