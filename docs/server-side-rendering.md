# Server-side rendering

Version 6.0.0 and later of `nhsuk-react-components` support server-side rendering (SSR)
and React Server Components (RSC).

The components render HTML on the server. Components that need JavaScript preserve their
[`'use client'` boundaries](https://react.dev/reference/rsc/use-client) and load the
matching NHS.UK frontend module after hydration. You do not need to opt the components
out of SSR.

SSR helps deliver usable HTML before JavaScript loads, but it does not guarantee
progressive enhancement by itself. Make sure users can still access content and complete
their task when JavaScript is unavailable.

## Load NHS.UK frontend styles

The styles are provided by the `nhsuk-frontend` peer dependency. Install it alongside
`nhsuk-react-components` if it is not already a dependency:

```bash
npm install nhsuk-frontend
```

Load the NHS.UK frontend styles once in your application layout. You can serve the
precompiled stylesheet from:

```text
node_modules/nhsuk-frontend/dist/nhsuk/nhsuk-frontend.min.css
```

and reference it in your page:

```html
<link rel="stylesheet" href="/stylesheets/nhsuk-frontend.min.css" />
```

Alternatively, include NHS.UK frontend in your Sass entry point:

```scss
@forward 'nhsuk-frontend/dist/nhsuk';
```

See the NHS.UK frontend guidance for [other CSS and asset setup
options](https://github.com/nhsuk/nhsuk-frontend/blob/main/docs/installation/installing-with-npm.md).

## Add browser feature detection

Add the NHS.UK frontend feature-detection script at the start of the `<body>`, before the
rendered page content:

```html
<body>
  <script>
    document.body.className +=
      ' js-enabled' +
      ('noModule' in HTMLScriptElement.prototype ? ' nhsuk-frontend-supported' : '');
  </script>
  <!-- rendered page content -->
</body>
```

In a JSX layout, set `suppressHydrationWarning` on the `<body>` because the script changes
its class before React hydrates the page:

```jsx
<body suppressHydrationWarning>{children}</body>
```

## Render components on the server

Use the components normally in a route or page that your React framework renders on the
server:

```jsx
import { Button, Form, TextInput } from 'nhsuk-react-components';

export default function SignInPage() {
  return (
    <Form action="/sign-in" method="post">
      <TextInput id="email" name="email" label="Email address" type="email" />
      <Button>Continue</Button>
    </Form>
  );
}
```

JavaScript-enhanced components initialise their own NHS.UK frontend module after
hydration. Do not also call `initAll()` for components rendered by this package, because
that would initialise them twice. If the page also contains non-React NHS.UK markup,
scope its initialisation to a container that does not include components rendered by
this package.

If you manage rendering without a framework, use [React's server rendering and hydration
APIs](https://react.dev/reference/react-dom/server). React recommends streaming APIs such
as `renderToPipeableStream` for Node.js and `renderToReadableStream` for web streams.

## Use React Server Components

The package preserves client boundaries for interactive components, so compatible RSC
frameworks can import them from Server Components. A component marked with `'use client'`
can still be pre-rendered to HTML on the server and hydrated in the browser.

For multipart components, prefer the named child exports instead of dot notation across
an RSC boundary:

```jsx
import { Breadcrumb, BreadcrumbItem } from 'nhsuk-react-components';

export default function PageBreadcrumb() {
  return (
    <Breadcrumb>
      <BreadcrumbItem href="/">Home</BreadcrumbItem>
      <BreadcrumbItem href="/services">NHS services</BreadcrumbItem>
    </Breadcrumb>
  );
}
```

RSC bundlers may not preserve dynamically assigned properties such as `Breadcrumb.Item`.
Named exports avoid that multipart namespace limitation. If you need to pass event
handlers or other non-serialisable props, create a local Client Component wrapper for
that usage.

## Avoid hydration mismatches

The server and client must produce the same initial markup.

- Pass stable `id` or `idPrefix` props where the component provides them. Form controls
  can use `name` as a fallback, but explicit identifiers make the rendered markup easier
  to reason about.
- Do not calculate initial props from browser-only state during rendering.
- Do not use random values or the current time in initial component content.
- Check the browser console for hydration warnings in a production build.

## Check progressive enhancement

Before releasing an SSR implementation:

1. Inspect the server response or page source and confirm the component HTML is present.
2. Disable JavaScript and confirm users can still access content and complete the task.
3. Enable JavaScript and confirm enhanced components work without hydration warnings.
4. Run your framework's production build and test the server-rendered route.

Read [how NHS.UK frontend supports different
browsers](https://github.com/nhsuk/nhsuk-frontend/blob/main/docs/contributing/browser-support.md)
for more about its baseline HTML and CSS experience.
