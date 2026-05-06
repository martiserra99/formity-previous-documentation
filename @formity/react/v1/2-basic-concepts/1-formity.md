---
title: Formity
nextjs:
  metadata:
    title: Formity - Docs
    description: Basic concepts about the main component of this package.
---

Basic concepts about the main component of this package.

---

## Formity{% id="formity" %}

The `Formity` component is the main component of this package. It renders the multi-step form and it receives the following props:

- `schema`: Defines the structure and behavior of the multi-step form.
- `inputs`: Defines values that can be used within the schema (optional).
- `params`: Defines values that can be used when rendering each form (optional).
- `onYield`: A callback function that is triggered when values are yielded (optional).
- `onReturn`: A callback function that is triggered when the form completes (optional).
- `initialState`: The initial state of the multi-step form (optional).

```tsx
import { useCallback, useState } from "react";

import {
  Formity,
  type OnYield,
  type OnReturn,
  type ReturnOutput,
} from "@formity/react";

import { Output } from "./components/output";

import { schema, type Values } from "./schema";

export default function App() {
  const [output, setOutput] = useState<ReturnOutput<Values> | null>(null);

  const onYield = useCallback<OnYield<Values>>((output) => {
    console.log(output);
  }, []);

  const onReturn = useCallback<OnReturn<Values>>((output) => {
    setOutput(output);
  }, []);

  if (output) {
    return <Output output={output} onStart={() => setOutput(null)} />;
  }

  return (
    <Formity<Values> schema={schema} onYield={onYield} onReturn={onReturn} />
  );
}
```
