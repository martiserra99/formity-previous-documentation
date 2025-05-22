# Animations

Learn how to add animations in these forms by using Framer Motion.

## Initial steps

To create animations in these forms we can use the [Framer Motion](https://www.framer.com/motion/) package. To learn how to do it, clone the following GitHub repository so that you don't need to start from scratch.

```bash
git clone https://github.com/martiserra99/formity-previous-animations
```

Make sure you run the following command to install all the dependencies:

```bash
npm install
```

Additionally, you also need to install [Framer Motion](https://www.framer.com/motion/) by doing the following:

```bash
npm install framer-motion
```

## Animate form

To animate form transitions, we will use the `AnimatePresence` component in the file `components/form.tsx`. The content of this component will be the form, and we will use the `key` prop with the `step` value to animate when it enters and when it leaves.

```tsx
// src/components/form.tsx

import { forwardRef, ReactElement, useCallback } from "react";
import { FormProvider, useForm } from "react-hook-form";
import { Step, DefaultValues, Resolver, OnNext, Variables } from "formity";
import { AnimatePresence, motion } from "framer-motion";

interface FormProps {
  step: Step;
  defaultValues: DefaultValues;
  resolver: Resolver;
  onNext: OnNext;
  children: ReactElement;
}

export default function Form({
  step,
  defaultValues,
  resolver,
  onNext,
  children,
}: FormProps) {
  return (
    <AnimatePresence mode="popLayout" initial={false}>
      <MotionComponent
        key={step}
        defaultValues={defaultValues}
        resolver={resolver}
        onNext={onNext}
        initial={{ opacity: 0, x: 100 }}
        animate={{
          x: 0,
          opacity: 1,
          transition: { delay: 0.25, duration: 0.5 },
        }}
        exit={{
          x: -100,
          opacity: 0,
          transition: { delay: 0, duration: 0.25 },
        }}
      >
        {children}
      </MotionComponent>
    </AnimatePresence>
  );
}

interface ComponentProps {
  defaultValues: DefaultValues;
  resolver: Resolver;
  onNext: OnNext;
  children: ReactElement;
}

const Component = forwardRef<HTMLFormElement, ComponentProps>(
  function Component({ defaultValues, resolver, onNext, children }, ref) {
    const form = useForm({ defaultValues, resolver });

    const handleSubmit = useCallback(
      (formData: Variables) => {
        onNext(formData);
      },
      [onNext]
    );

    return (
      <form
        ref={ref}
        onSubmit={form.handleSubmit(handleSubmit)}
        className="h-full"
      >
        <FormProvider {...form}>{children}</FormProvider>
      </form>
    );
  }
);

const MotionComponent = motion(Component);
```

We will also need to change the `components.tsx` file so that we use the `step` prop instead of the `key` prop.

```tsx
// src/components.tsx

// ...

const components: Components<Parameters> = {
  form: ({ step, defaultValues, resolver, onNext, children }, render) => (
    <Form
      step={step}
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

Now that we have done that, we can see that there are already some animations when we go to the next or previous step.

## Different animations

We may want to use different animations depending on whether we are going to the next or previous step. To do it, we will create an `animate.ts` file with a context that contains data about the current animation that needs to be performed.

```tsx
// src/animate.ts

import { createContext, Dispatch, SetStateAction, useContext } from "react";

export type Animate = "none" | "next" | "back";

export interface AnimateValue {
  animate: Animate;
  setAnimate: Dispatch<SetStateAction<Animate>>;
}

export const AnimateContext = createContext<AnimateValue | null>(null);

export function useAnimate(): AnimateValue {
  const context = useContext(AnimateContext);
  if (!context) {
    throw new Error(
      "useAnimate must be used within an AnimateContext.Provider"
    );
  }
  return context;
}
```

Then, we will provide the context in the `Form` component, and we will apply a different animation depending on whether we are going to the next or previous step.

```tsx
// src/components/form.tsx

import {
  forwardRef,
  ReactElement,
  useCallback,
  useState,
  useMemo,
} from "react";
import { FormProvider, useForm } from "react-hook-form";
import { Step, DefaultValues, Resolver, OnNext, Variables } from "formity";
import { AnimatePresence, motion, MotionProps } from "framer-motion";

import { Animate, AnimateContext, useAnimate } from "@/animate";

interface FormProps {
  step: Step;
  defaultValues: DefaultValues;
  resolver: Resolver;
  onNext: OnNext;
  children: ReactElement;
}

export default function Form({
  step,
  defaultValues,
  resolver,
  onNext,
  children,
}: FormProps) {
  const [animate, setAnimate] = useState<Animate>("none");
  const value = useMemo(() => ({ animate, setAnimate }), [animate, setAnimate]);
  return (
    <AnimateContext.Provider value={value}>
      <AnimatePresence
        mode="popLayout"
        onExitComplete={() => setAnimate("none")}
        initial={false}
      >
        <MotionComponent
          key={step}
          defaultValues={defaultValues}
          resolver={resolver}
          onNext={onNext}
          animate={{
            x: 0,
            opacity: 1,
            transition: { delay: 0.25, duration: 0.5 },
          }}
          {...motionProps(animate)}
        >
          {children}
        </MotionComponent>
      </AnimatePresence>
    </AnimateContext.Provider>
  );
}

function motionProps(animate: Animate): MotionProps {
  if (animate === "next") {
    return {
      initial: { x: 100, opacity: 0 },
      exit: {
        x: -100,
        opacity: 0,
        transition: { delay: 0, duration: 0.25 },
      },
    };
  }
  if (animate === "back") {
    return {
      initial: { x: -100, opacity: 0 },
      exit: {
        x: 100,
        opacity: 0,
        transition: { delay: 0, duration: 0.25 },
      },
    };
  }
  return {};
}

interface ComponentProps {
  defaultValues: DefaultValues;
  resolver: Resolver;
  onNext: OnNext;
  children: ReactElement;
}

const Component = forwardRef<HTMLFormElement, ComponentProps>(
  function Component({ defaultValues, resolver, onNext, children }, ref) {
    const form = useForm({ defaultValues, resolver });

    const { setAnimate } = useAnimate();

    const handleSubmit = useCallback(
      (formData: Variables) => {
        setAnimate("next");
        setTimeout(() => onNext(formData), 0);
      },
      [onNext, setAnimate]
    );

    return (
      <form
        ref={ref}
        onSubmit={form.handleSubmit(handleSubmit)}
        className="h-full"
      >
        <FormProvider {...form}>{children}</FormProvider>
      </form>
    );
  }
);

const MotionComponent = motion(Component);
```

As you may have noticed we are using a `setTimeout` in the `handleSubmit` function. The reason for that is that we need to go to the next step after we have changed the value of the context to ensure that we perform the correct animation. If we don't do that it won't work as expected.

The same thing needs to be done in the `Back` component.

```tsx
// src/components/navigation/back.tsx

import { useCallback } from "react";
import { useFormContext } from "react-hook-form";
import { OnBack } from "formity";

import { ChevronLeftIcon } from "@heroicons/react/20/solid";

import { cn } from "@/utils";

import { useAnimate } from "@/animate";

interface BackProps {
  onBack: OnBack;
}

export default function Back({ onBack }: BackProps) {
  const { getValues } = useFormContext();
  const { setAnimate } = useAnimate();

  const handleClick = useCallback(() => {
    setAnimate("back");
    setTimeout(() => onBack(getValues()), 0);
  }, [onBack, setAnimate, getValues]);

  return (
    <button
      type="button"
      onClick={handleClick}
      className={cn(
        "block rounded-full border border-neutral-800 bg-neutral-950 px-6 py-2 hover:bg-neutral-800",
        "focus:outline-none focus:ring-2 focus:ring-white/10 focus:ring-offset-2 focus:ring-offset-black",
        "disabled:bg-neutral-950 disabled:opacity-60"
      )}
    >
      <ChevronLeftIcon className="pointer-events-none size-5 fill-white" />
    </button>
  );
}
```

As you can see, we have a different animation when we go to the next or previous step. However, there is one last thing that needs to be addressed to avoid some weird behaviors.

## Disable navigation

If we go to the next or previous step while the animation is being performed we may face some weird behaviors. To ensure that doesn't happen we can disable the buttons while the animation is being performed.

We need to update the `Next` component:

```tsx
// src/components/navigation/next.tsx

// ...

import { useAnimate } from "@/animate";

interface NextProps {
  children: ReactNode;
}

export default function Next({ children }: NextProps) {
  const { animate } = useAnimate();
  return <Button disabled={animate !== "none"}>{children}</Button>;
}
```

We also need to update the `Back` component:

```tsx
// src/components/navigation/back.tsx

// ...

import { useAnimate } from "@/animate";

interface BackProps {
  onBack: OnBack;
}

export default function Back({ onBack }: BackProps) {
  const { getValues } = useFormContext();
  const { animate, setAnimate } = useAnimate();

  const handleClick = useCallback(() => {
    setAnimate("back");
    setTimeout(() => onBack(getValues()), 0);
  }, [onBack, setAnimate, getValues]);

  return (
    <button
      type="button"
      onClick={handleClick}
      className={cn(
        "block rounded-full border border-neutral-800 bg-neutral-950 px-6 py-2 hover:bg-neutral-800",
        "focus:outline-none focus:ring-2 focus:ring-white/10 focus:ring-offset-2 focus:ring-offset-black",
        "disabled:bg-neutral-950 disabled:opacity-60"
      )}
      disabled={animate !== "none"}
    >
      <ChevronLeftIcon className="pointer-events-none size-5 fill-white" />
    </button>
  );
}
```
