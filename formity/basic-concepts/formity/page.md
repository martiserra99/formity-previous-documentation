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

The `Formity` component is the main component of this package. It has the responsability to render the form and it receives the following props:

- `components`: The components that can be used in the form.

- `variables`: The additional variables to use in the schema (optional).

- `schema`: The JSON that defines the form.

- `onReturn`: The function that is called when the form is submitted.

- `initialFlow`: The initial state of the form (optional).

```tsx
// ...

import { Formity, Value } from "formity";

import components from "@/components";
import variables from "@/variables";
import schema from "@/schema";
import initialFlow from "@/initial-flow";

// ...

export default function Home() {
  // ...

  return (
    <Formity
      components={components}
      variables={variables}
      schema={schema}
      onReturn={handleReturn}
      initialFlow={initialFlow}
    />
  );
}
```
