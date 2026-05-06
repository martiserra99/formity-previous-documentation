---
title: Introduction
nextjs:
  metadata:
    title: Introduction - Docs
    description: Take a look at this introduction to have a better idea of what Formity is about.
---

Take a look at this introduction to have a better idea of what Formity is about.

---

## Overview{% id="overview" %}

Formity is an advanced form-building package designed to help React developers create advanced multi-step forms.

What sets Formity apart from other form libraries is its unique ability to adapt the form's questions based on the user's previous answers.

This powerful feature enables developers to craft highly personalized and interactive forms without the need for complex conditional logic or cumbersome code.

## Installation{% id="installation" %}

To install this package you have to run the following command.

```bash
npm install @formity/react
```

Formity works seamlessly with libraries like [React Hook Form](https://react-hook-form.com/), [Formik](https://formik.org/), and [TanStack Form](https://tanstack.com/form/latest), so you'll typically install it alongside one of them.

## Usage{% id="usage" %}

The core of this package is the `Formity` component. It renders the multi-step form and these are the most important props:

- `schema`: Defines the structure and behavior of the multi-step form.
- `onReturn`: A callback function that is triggered when the form is completed.

```tsx
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

To learn how to define the schema and leverage conditional logic, proceed to the next section, where we'll guide you through the process of creating a multi-step form.
