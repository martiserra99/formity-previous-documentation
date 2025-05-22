# Introduction

Take a look at this introduction to have a better idea of what Formity is about.

## Overview

Creating a multi-step form in which there are questions dependent on previous answers can be a really complex task. Additionally, the challenges intensify when considering the need to save such forms in a file or a database.

This is where Formity comes in, a solution designed to simplify the process. With Formity, building these kinds of forms is as straightforward as using JSON. Say goodbye to complexities and experience form development like never before.

## Installation

To install this package you have to run the following command.

```bash
npm install formity expry react-hook-form
```

As you can see, Formity depends on two packages, [Expry](https://expry.dev) and [React Hook Form](https://www.react-hook-form.com). We suggest you to be a little familiar with these packages before learning about Formity.

## Usage

The core of this package is the `Formity` component. This component is responsible for rendering the form, and we need to provide the following props:

- `components`: The components that can be used in the form.

- `schema`: The JSON that defines the form.

- `onReturn`: The function that is called when the form is submitted.

```tsx
import { Formity, OnReturn } from "formity";

import components from "./components";
import schema from "./schema";

interface FormProps {
  onReturn: OnReturn;
}

export default function Form({ onReturn }: FormProps) {
  return (
    <Formity components={components} schema={schema} onReturn={onReturn} />
  );
}
```
