# Gabber Example Nuxt App 

This is an example app showing how Gabber can be integrated into a Nuxt App.

## Backend Server

All Gabber apps require a small backend component in order to keep your API key safe. 

To simplify our growing number of example apps, we made an [example-server](https://github.com/gabber-dev/example-server).

This app assumes you are running that example server when it makes a call to http://localhost:4000/token to generate a token.

## Setup

Install dependencies:

```bash
pnpm install
```

## Development Server

Start the development server on `http://localhost:3000`:

```bash
pnpm dev
```