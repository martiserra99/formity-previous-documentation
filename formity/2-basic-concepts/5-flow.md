# Flow

Learn how we can save the form state to use it later on.

## Flow

We can get the current state of the form by using the `getFlow` function. This function can be accessed as a variable when we reference the components in the schema, or by using the `useFormity` hook.

```tsx
// ...
import {
  DefaultValues,
  Resolver,
  OnNext,
  Variables,
  useFormity,
} from "formity";

// ...

export default function Form({
  defaultValues,
  resolver,
  onNext,
  children,
}: FormProps) {
  const form = useForm({ defaultValues, resolver });
  const { getFlow } = useFormity();

  // ...

  return (
    <form onSubmit={form.handleSubmit(handleSubmit)} className="h-full">
      <FormProvider {...form}>{children}</FormProvider>
    </form>
  );
}
```

In this function, we need to pass the current values of the form, and it will return an object of type `Flow`. This object contains all the data about the flow of steps that have been completed with the values that have been entered.

```tsx
// ...

export default function Form({
  defaultValues,
  resolver,
  onNext,
  children,
}: FormProps) {
  const form = useForm({ defaultValues, resolver });
  const { getFlow } = useFormity();

  useEffect(() => {
    const { unsubscribe } = form.watch((values) => {
      const flow = getFlow(values);
      console.log(flow);
    });
    const flow = getFlow(form.getValues());
    console.log(flow);
    return () => unsubscribe();
  }, [form, getFlow]);

  // ...

  return (
    <form onSubmit={form.handleSubmit(handleSubmit)} className="h-full">
      <FormProvider {...form}>{children}</FormProvider>
    </form>
  );
}
```

We can save this object anywhere and use it to resume the form from where we left off.

```tsx
// ...

useEffect(() => {
  const { unsubscribe } = form.watch((values) => {
    const flow = getFlow(values);
    saveFlow(flow);
  });
  const flow = getFlow(form.getValues());
  saveFlow(flow);
  return () => unsubscribe();
}, [form, getFlow]);

// ...
```

To start the form using the state we saved, we can use the `initialFlow` prop of the `Formity` component:

```tsx
// ...

const initialFlow = getSavedFlow();

export default function Home() {
  // ...

  return (
    <Formity
      components={components}
      schema={schema}
      onReturn={handleReturn}
      initialFlow={initialFlow}
    />
  );
}
```
