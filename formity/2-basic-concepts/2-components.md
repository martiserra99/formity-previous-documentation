# Components

Learn how the components need to be defined and used with Formity.

## Components

When creating a form, the first thing that needs to be done is to create the components that we will use. After creating these components, we will be able to reference them in the schema that defines the form.

To understand how to create them, it is helpful to first know how they need to be referenced. Here is an example:

```json
{
  "form": {
    "step": "$step",
    "defaultValues": "$defaultValues",
    "resolver": "$resolver",
    "onNext": "$onNext",
    "children": {
      // ...
    }
  }
}
```

We have an object with a key that is the name of the component, and all the properties of the object are the parameters used to configure the component.

The values of the properties can be components as well:

```json
{
  "form": {
    "step": "$step",
    "defaultValues": "$defaultValues",
    "resolver": "$resolver",
    "onNext": "$onNext",
    "children": {
      "formLayout": {
        "heading": "Tell us about yourself",
        "description": "We would want to know about you",
        "fields": [
          {
            "textField": {
              "name": "name",
              "label": "Name"
            }
          },
          {
            "numberField": {
              "name": "age",
              "label": "Age"
            }
          }
        ],
        "button": {
          "next": { "text": "Next" }
        }
      }
    }
  }
}
```

These components need to be created so that we can reference them in the schema. For that, we need to create a `components` object like the following one:

```tsx
import { Fragment } from "react";
import {
  Components,
  Step,
  DefaultValues,
  Resolver,
  OnNext,
  OnBack,
} from "formity";
import { Value } from "expry";

import Form from "@/components/form";
import FormLayout from "@/components/form-layout";
import Next from "@/components/navigation/next";
import Back from "@/components/navigation/back";
import TextField from "@/components/react-hook-form/text-field";
import NumberField from "@/components/react-hook-form/number-field";
import YesNo from "@/components/react-hook-form/yes-no";

type Parameters = {
  form: {
    step: Step;
    defaultValues: DefaultValues;
    resolver: Resolver;
    onNext: OnNext;
    children: Value;
  };
  formLayout: {
    heading: string;
    description: string;
    fields: Value[];
    button: Value;
    back?: Value;
  };
  next: {
    text: string;
  };
  back: {
    onBack: OnBack;
  };
  textField: {
    name: string;
    label: string;
  };
  numberField: {
    name: string;
    label: string;
  };
  yesNo: {
    name: string;
    label: string;
  };
};

const components: Components<Parameters> = {
  form: ({ step, defaultValues, resolver, onNext, children }, render) => (
    <Form
      key={step}
      defaultValues={defaultValues}
      resolver={resolver}
      onNext={onNext}
    >
      {render(children)}
    </Form>
  ),
  formLayout: ({ heading, description, fields, button, back }, render) => (
    <FormLayout
      heading={heading}
      description={description}
      fields={fields.map((field, index) => (
        <Fragment key={index}>{render(field)}</Fragment>
      ))}
      button={render(button)}
      back={back ? render(back) : undefined}
    />
  ),
  next: ({ text }) => <Next>{text}</Next>,
  back: ({ onBack }) => <Back onBack={onBack} />,
  textField: ({ name, label }) => <TextField name={name} label={label} />,
  numberField: ({ name, label }) => <NumberField name={name} label={label} />,
  yesNo: ({ name, label }) => <YesNo name={name} label={label} />,
};

export default components;
```

This object is the one that we need to pass to the `Formity` component. It has a property for each component, and each of these properties is a function that receives some parameters and renders the component with some props.

We also have a second parameter called `render`. This parameter is a function that receives a value of type `Value`, which in this case corresponds to the reference of a component, and is responsible for rendering the component.

### Parameters

As you may have noticed, the `components` object is of type `Components`, and it receives a type called `Parameters`. This type defines the parameters that must be used for each component.

### Variables

If you look closer at how the components are referenced in the schema, you will see that some values are strings that start with `$`.

```json
{
  "form": {
    "step": "$step",
    "defaultValues": "$defaultValues",
    "resolver": "$resolver",
    "onNext": "$onNext",
    "children": {
      // ...
    }
  }
}
```

These values are variables, and there are some predefined variables that can be used whenever we reference the components in the schema. These are the ones that exist:

- `step`: Number that defines the current step.
- `defaultValues`: Default values that we need to pass to the `useForm` hook.
- `resolver`: Validation rules that we need to pass to the `useForm` hook.
- `onNext`: Function that we need to call to go to the next step.
- `onBack`: Function that we need to call to go to the previous step.
- `getFlow`: Function that is used to get the current state of the form.

### Step

Whenever we go to the next step, if the rendered components are the same, the state of the form will remain unchanged. To reset the state of the components, we can pass the step variable as the value to the `key` prop of the form.

```tsx
// ...

const components: Components<Parameters> = {
  form: ({ step, defaultValues, resolver, onNext, children }, render) => (
    <Form
      key={step}
      defaultValues={defaultValues}
      resolver={resolver}
      onNext={onNext}
    >
      {render(children)}
    </Form>
  ),
  // ...
};

// ...
```

## useFormity

Apart from accessing these variables from the schema, we can also access them by using the `useFormity` hook. The only thing to keep in mind is that this hook will only work if it is being used in a component that is rendered from the schema.

```tsx
// ...
import { useFormity } from "formity";

// ...

export default function Form({ children }: FormProps) {
  const { defaultValues, resolver, onNext } = useFormity();
  const form = useForm({ defaultValues, resolver });
  return (
    <form onSubmit={form.handleSubmit(onNext)} className="h-full">
      <FormProvider {...form}>{children}</FormProvider>
    </form>
  );
}
```
