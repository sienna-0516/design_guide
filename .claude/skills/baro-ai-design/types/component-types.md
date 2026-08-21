# Component type signatures

TypeScript signatures for the components on `window.BAROAIDesignSystem_236701`.
Copy into a `.d.ts` in your own project if you want types; they are kept as
markdown here so they don't register as standalone design-system contracts.

```ts
export interface NavProps { active?: string; }   // default "Home"
export function Nav(props?: NavProps): JSX.Element;

export function Hero(): JSX.Element;
export function Features(): JSX.Element;
export function Stats(): JSX.Element;
export function SpecBand(): JSX.Element;
export function CTA(): JSX.Element;
export function Footer(): JSX.Element;
```

All components render a full-width marketing `<section>`. Only `Nav` takes a
prop (`active`, the highlighted link label). See README.md §4 for composition.
