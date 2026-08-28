# NHS.UK React Components

[![CI](https://github.com/NHSDigital/nhsuk-react-components/actions/workflows/main.yml/badge.svg)](https://github.com/NHSDigital/nhsuk-react-components/actions/workflows/main.yml)
[![Known Vulnerabilities](https://snyk.io/test/github/NHSDigital/nhsuk-react-components/badge.svg?targetFile=package.json)](https://snyk.io/test/github/NHSDigital/nhsuk-react-components?targetFile=package.json)
[![npm](https://img.shields.io/npm/v/nhsuk-react-components)](https://www.npmjs.com/package/nhsuk-react-components)

A React component library based on the [NHS.UK frontend library](https://github.com/nhsuk/nhsuk-frontend).

## Installation

Install from npm:

```bash
npm install nhsuk-react-components
```

or using Yarn:

```bash
yarn add nhsuk-react-components
```

## Usage

Import the components you need:

```tsx
import { Button, DateInput, Form } from 'nhsuk-react-components';

const Example = () => (
  <Form>
    <DateInput
      legend="What is your date of birth?"
      legendProps={{ size: 'l' }}
      id="date-of-birth"
      error="Date of birth must include a day"
    >
      <DateInput.Day />
      <DateInput.Month error={false} />
      <DateInput.Year error={false} />
    </DateInput>

    <Button>Save and continue</Button>
  </Form>
);
```

## Server-side rendering

Version 6.0.0 and later support server-side rendering (SSR) and React Server Components
(RSC). Components render HTML on the server, then components that need JavaScript are
enhanced after hydration.

Read the [server-side rendering guide](docs/server-side-rendering.md) for the required
NHS.UK frontend styles, browser feature detection, React Server Component imports and
progressive enhancement checks.

## Development

To run this project locally, set up Yarn using [Node.js corepack](https://github.com/nodejs/corepack#readme)

```bash
npm install -g corepack
corepack enable
yarn install
```

Then run the following:

1. **Build and watch package**

   ```bash
   yarn build --watch
   ```

2. **Run Storybook**

   ```bash
   yarn storybook
   ```

3. **Run tests**

   ```bash
   yarn test
   ```

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidance.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
