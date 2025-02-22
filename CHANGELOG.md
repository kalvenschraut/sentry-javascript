# Changelog

## Unreleased

- "You miss 100 percent of the chances you don't take. — Wayne Gretzky" — Michael Scott

## 8.7.0

### Important Changes

- **feat(react): Add TanStack Router integration (#12095)**

  This release adds instrumentation for TanStack router with a new `tanstackRouterBrowserTracingIntegration` in the
  `@sentry/react` SDK:

  ```javascript
  import * as Sentry from '@sentry/react';
  import { createRouter } from '@tanstack/react-router';

  const router = createRouter({
    // Your router options...
  });

  Sentry.init({
    dsn: '___PUBLIC_DSN___',
    integrations: [Sentry.tanstackRouterBrowserTracingIntegration(router)],
    tracesSampleRate: 1.0,
  });
  ```

### Other Changes

- fix(nextjs): Do not hide `sourceMappingURL` comment on client when `nextConfig.productionBrowserSourceMaps: true` is
  set (#12278)

## 8.6.0

### Important Changes

- **feat(metrics): Add `timings` method to metrics (#12226)**

  This introduces a new method, `metrics.timing()`, which can be used in two ways:

  1. With a numeric value, to simplify creating a distribution metric. This will default to `second` as unit:

  ```js
  Sentry.metrics.timing('myMetric', 100);
  ```

  2. With a callback, which will wrap the duration of the callback. This can accept a sync or async callback. It will
     create an inactive span around the callback and at the end emit a metric with the duration of the span in seconds:

  ```js
  const returnValue = Sentry.metrics.timing('myMetric', measureThisFunction);
  ```

- **feat(react): Add `Sentry.reactErrorHandler` (#12147)**

  This PR introduces `Sentry.reactErrorHandler`, which you can use in React 19 as follows:

  ```js
  import * as Sentry from '@sentry/react';
  import { hydrateRoot } from 'react-dom/client';

  ReactDOM.hydrateRoot(
    document.getElementById('root'),
    <React.StrictMode>
      <App />
    </React.StrictMode>,
    {
      onUncaughtError: Sentry.reactErrorHandler(),
      onCaughtError: Sentry.reactErrorHandler((error, errorInfo) => {
        // optional callback if users want custom config.
      }),
    },
  );
  ```

  For more details, take a look at [the PR](https://github.com/getsentry/sentry-javascript/pull/12147). Our
  documentation will be updated soon!

### Other Changes

- feat(sveltekit): Add request data to server-side events (#12254)
- fix(core): Pass in cron monitor config correctly (#12248)
- fix(nextjs): Don't capture suspense errors in server components (#12261)
- fix(tracing): Ensure sent spans are limited to 1000 (#12252)
- ref(core): Use versioned carrier on global object (#12206)

## 8.5.0

### Important Changes

- **feat(react): Add React 19 to peer deps (#12207)**

This release adds support for React 19 in the `@sentry/react` SDK package.

- **feat(node): Add `@sentry/node/preload` hook (#12213)**

This release adds a new way to initialize `@sentry/node`, which allows you to use the SDK with performance
instrumentation even if you cannot call `Sentry.init()` at the very start of your app.

First, run the SDK like this:

```bash
node --require @sentry/node/preload ./app.js
```

Now, you can initialize and import the rest of the SDK later or asynchronously:

```js
const express = require('express');
const Sentry = require('@sentry/node');

const dsn = await getSentryDsn();
Sentry.init({ dsn });
```

For more details, head over to the
[PR Description of the new feature](https://github.com/getsentry/sentry-javascript/pull/12213). Our docs will be updated
soon with a new guide.

### Other Changes

- feat(browser): Do not include metrics in base CDN bundle (#12230)
- feat(core): Add `startNewTrace` API (#12138)
- feat(core): Allow to pass custom scope to `captureFeedback()` (#12216)
- feat(core): Only allow `SerializedSession` in session envelope items (#11979)
- feat(nextjs): Use Vercel's `waitUntil` to defer freezing of Vercel Lambdas (#12133)
- feat(node): Ensure manual OTEL setup works (#12214)
- fix(aws-serverless): Avoid minifying `Module._resolveFilename` in Lambda layer bundle (#12232)
- fix(aws-serverless): Ensure lambda layer uses default export from `ImportInTheMiddle` (#12233)
- fix(browser): Improve browser extension error message check (#12146)
- fix(browser): Remove optional chaining in INP code (#12196)
- fix(nextjs): Don't report React postpone errors (#12194)
- fix(nextjs): Use global scope for generic event filters (#12205)
- fix(node): Add origin to redis span (#12201)
- fix(node): Change import of `@prisma/instrumentation` to use default import (#12185)
- fix(node): Only import `inspector` asynchronously (#12231)
- fix(replay): Update matcher for hydration error detection to new React docs (#12209)
- ref(profiling-node): Add warning when using non-LTS node (#12211)

## 8.4.0

### Important Changes

- **feat(nextjs): Trace pageloads in App Router (#12157)**

If you are using Next.js version `14.3.0-canary.64` or above, the Sentry Next.js SDK will now trace clientside pageloads
with React Server Components. This means, that client-side errors like
`Error: An error occurred in the Server Components render.`, which previously didn't give you much information on how
that error was caused, can now be traced back to a specific error in a server component.

- **feat(angular): Add Support for Angular 18 (#12183)**

This release guarantees support for Angular 18 with `@sentry/angular`.

### Other Changes

- feat(deps): Bump @opentelemetry/instrumentation-aws-lambda from 0.41.0 to 0.41.1 (#12078)
- fix(metrics): Ensure string values are interpreted for metrics (#12165)

## 8.3.0

### Important Changes

- **Better Node Framework Span Data**

This release improves data quality of spans emitted by Express, Fastify, Connect, Koa, Nest.js and Hapi.

- feat(node): Ensure connect spans have better data (#12130)
- feat(node): Ensure express spans have better data (#12107)
- feat(node): Ensure fastify spans have better data (#12106)
- feat(node): Ensure hapi spans have better data (#12140)
- feat(node): Ensure koa spans have better data (#12108)
- feat(node): Ensure Nest.js spans have better data (#12139)
- feat(deps): Bump @opentelemetry/instrumentation-express from 0.38.0 to 0.39.0 (#12079)

- **feat(node): No-code init via `--import=@sentry/node/init` (#11999)**

When using Sentry in ESM mode, you can now use Sentry without manually calling init like this:

```bash
 SENTRY_DSN=https://examplePublicKey@o0.ingest.sentry.io/0 node --import=@sentry/node/init app.mjs
```

When using CommonJS, you can do:

```bash
 SENTRY_DSN=https://examplePublicKey@o0.ingest.sentry.io/0 node --require=@sentry/node/init app.js
```

### Other Changes

- chore: Align and update MIT license dates (#12143)
- chore: Resolve or postpone a random assortment of TODOs (#11977)
- doc(migration): Add entry for runWithAsyncContext (#12153)
- docs: Add migration docs to point out that default import does not work (#12100)
- docs(sveltekit): process.env.SENTRY_AUTH_TOKEN (#12118)
- feat(browser): Ensure `browserProfilingIntegration` is published to CDN (#12158)
- feat(google-cloud): Expose ESM build (#12149)
- feat(nextjs): Ignore Prisma critical dependency warnings (#12144)
- feat(node): Add app.free_memory info to events (#12150)
- feat(node): Do not create GraphQL resolver spans by default (#12097)
- feat(node): Use `node:` prefix for node built-ins (#11895)
- feat(replay): Use unwrapped `setTimeout` to avoid e.g. angular change detection (#11924)
- fix(core): Add dsn to span envelope header (#12096)
- fix(feedback): Improve feedback border color in dark-mode, and prevent auto-dark mode when a theme is picked (#12126)
- fix(feedback): Set optionOverrides to be optional in TS definition (#12125)
- fix(nextjs): Don't put `undefined` values in props (#12131)
- fix(nextjs): Fix legacy configuration method detection for emitting warning (#12136)
- fix(node): Ensure fetch/http breadcrumbs are created correctly (#12137)
- fix(node): Update `@prisma/instrumentation` from 5.13.0 to 5.14.0 (#12081)
- ref(node): Add log for running in ESM/CommonJS mode (#12134)
- ref(node): Handle failing hook registration gracefully (#12135)
- ref(node): Only show instrumentation warning when tracing is enabled (#12141)

Work in this release contributed by @pboling. Thank you for your contribution!

## 8.2.1

- fix(aws-serverless): Fix build of lambda layer (#12083)
- fix(nestjs): Broaden nest.js type (#12076)

## 8.2.0

- feat(redis-cache): Create cache-span with prefixed keys (get/set commands) (#12070)
- feat(core): Add `beforeSendSpan` hook (#11886)
- feat(browser): Improve idle span handling (#12065)
- fix(node): Set transactionName for unsampled spans in httpIntegration (#12071)
- fix(core): Export Scope interface as `Scope` (#12067)
- fix(core): Avoid looking up client for `hasTracingEnabled()` if possible (#12066)
- fix(browser): Use consistent timestamps (#12063)
- fix(node): Fix check for performance integrations (#12043)
- ref(sveltekit): Warn to delete source maps if Sentry plugin enabled source maps generation (#12072)

## 8.1.0

This release mainly fixes a couple of bugs from the initial [8.0.0 release](#800). In addition to the changes below, we
updated some initially missed points in our migration guides and documentation.

- feat(aws-serverless): Fix tree-shaking for aws-serverless package (#12017)
- feat(node): Bump opentelemetry instrumentation to latest version (#12028)
- feat(scope): Bring back `lastEventId` on isolation scope (#11951) (#12022)
- fix(aws-serverless): Export `awslambda-auto`
- fix(node): Do not warn for missing instrumentation if SDK is disabled (#12041)
- fix(react): Set dependency-injected functions as early as possible (#12019)
- fix(react): Warn and fall back gracefully if dependency injected functions are not available (#12026)
- ref(core): Streamline `parseSampleRate` utility function (#12024)
- ref(feedback): Make `eventId` optional and use `lastEventId` in report dialog (#12029)

## 8.0.0

The Sentry JS SDK team is proud to announce the release of version `8.0.0` of Sentry's JavaScript SDKs - it's been a
long time coming! Thanks to everyone for your patience and a special shout out to the brave souls testing preview builds
and reporting issues - we appreciate your support!

---

### How to Upgrade to Version 8:

We recommend reading the
[migration guide docs](https://docs.sentry.io/platforms/javascript/migration/v7-to-v8/#migration-codemod) to find out
how to address any breaking changes in your code for your specific platform or framework.

To automate upgrading to v8 as much as possible, use our migration codemod `@sentry/migr8`:

```sh
npx @sentry/migr8@latest
```

All deprecations from the v7 cycle, with the exception of `getCurrentHub()`, have been removed and can no longer be used
in v8. If you have an advanced Sentry SDK setup, we additionally recommend reading the
[in-depth migration guide](./MIGRATION.md) in our repo which highlights all changes with additional details and
information.

The rest of this changelog highlights the most important (breaking) changes and links to more detailed information.

### Version Support

With v8, we dropped support for several old runtimes and browsers

**Node SDKs:** The Sentry JavaScript SDK v8 now supports **Node.js 14.8.0 or higher**. This applies to `@sentry/node`
and all of our node-based server-side sdks (`@sentry/nextjs`, `@sentry/remix`, etc.). Furthermore, version 8 now ships
with full support for ESM-based node apps using **Node.js 18.19.0 or higher**.

**Browser SDKs:** The browser SDKs now require
[**ES2018+**](https://caniuse.com/?feats=mdn-javascript_builtins_regexp_dotall,js-regexp-lookbehind,mdn-javascript_builtins_regexp_named_capture_groups,mdn-javascript_builtins_regexp_property_escapes,mdn-javascript_builtins_symbol_asynciterator,mdn-javascript_functions_method_definitions_async_generator_methods,mdn-javascript_grammar_template_literals_template_literal_revision,mdn-javascript_operators_destructuring_rest_in_objects,mdn-javascript_operators_destructuring_rest_in_arrays,promise-finally)
compatible browsers. New minimum browser versions:

- Chrome 63
- Edge 79
- Safari/iOS Safari 12
- Firefox 58
- Opera 50
- Samsung Internet 8.2

For more details, please see the
[version support section in our migration guide](./MIGRATION.md#1-version-support-changes).

### Initializing Server-side SDKs (Node, Bun, Deno, Serverless):

In v8, we support a lot more node-based packages than before. In order to ensure auto-instrumentation works, the SDK now
needs to be imported and initialized before any other import in your code.

We recommend creating a new file (e.g. `instrumentation.js`) to import and initialize the SDK. Then, import the file on
top of your entry file or detailed instructions, check our updated SDK setup docs
[initializing the SDK in v8](https://docs.sentry.io/platforms/javascript/guides/node/).

### Performance Monitoring Changes

The API around performance monitoring and tracing has been streamlined, and we've added support for more integrations
out of the box.

- [Performance Monitoring API](./MIGRATION.md#performance-monitoring-api)
- [Performance Monitoring Integrations](./MIGRATION.md#performance-monitoring-integrations)

### Functional Integrations

Integrations are now simple functions instead of classes. Class-based integrations
[have been removed](./MIGRATION.md#removal-of-class-based-integrations):

```javascript
// old (v7)
Sentry.init({
  integrations: [new Sentry.BrowserTracing()],
});

// new (v8)
Sentry.init({
  integrations: [Sentry.browserTracingIntegration()],
});
```

### Package removal

The following packages have been removed or replaced and will no longer be published:

- [`@sentry/hub`](./MIGRATION.md#sentryhub)
- [`@sentry/tracing`](./MIGRATION.md#sentrytracing)
- [`@sentry/integrations`](./MIGRATION.md#sentryintegrations)
- [`@sentry/serverless`](./MIGRATION.md#sentryserverless)
- [`@sentry/replay`](./MIGRATION.md#sentryreplay)

### Changes since `8.0.0-rc.3`

- **feat(nextjs): Remove `transpileClientSDK` (#11978)**

  As we are dropping support for Internet Explorer 11 and other other older browser versions wih version `8.0.0`, we are
  also removing the `transpileClientSDK` option from the Next.js SDK. If you need to support these browser versions,
  please configure Webpack and Next.js to down-compile the SDK.

- **feat(serverless): Do not include performance integrations by default (#11998)**

  To keep Lambda bundle size reasonable, the SDK no longer ships with all performance (database) integrations by
  default. Add the Sentry integrations of the databases and other tools you're using manually to your `Sentry.init` call
  by following
  [this guide](https://docs.sentry.io/platforms/javascript/configuration/integrations/#modifying-default-integrations).
  Note that this change does not apply if you use the SDK with the Sentry AWS Lambda layer.

- feat(feedback): Simplify public css configuration for feedback (#11985)
- fix(feedback): Check for empty user (#11993)
- fix(replay): Fix type for `replayCanvasIntegration` (#11995)
- fix(replay): Fix user activity not being updated in `start()` (#12001)

## 8.0.0-rc.3

### Important Changes

- **feat(bun): Add Bun Global Unhandled Handlers (#11960)**

The Bun SDK will now capture global unhandled errors.

### Other Changes

- feat(node): Log process and thread info on initialisation (#11972)
- fix(aws-serverless): Include ESM artifacts in package (#11973)
- fix(browser): Only start `http.client` spans if there is an active parent span (#11974)
- fix(feedback): Improve CSS theme variable names and layout (#11964)
- fix(node): Ensure `execArgv` are not sent to worker threads (#11963)
- ref(feedback): Simplify feedback function params (#11957)

## 8.0.0-rc.2

### Important Changes

- **feat(node): Register ESM patching hooks in init for supported Node.js versions**

This release includes adds support for ESM when `Sentry.init()` is called within a module imported via the `--import`
Node.js flag:

```sh
node --import ./your-file-with-sentry-init.mjs your-app.mjs
```

Note that the SDK only supports ESM for node versions `18.19.0` and above, and `20.6.0` above.

### Other Changes

- deps(node): Bump `@opentelemetry/core` to `1.24.1` and `@opentelemetry/instrumentation` to `0.51.1` (#11941)
- feat(connect): Warn if connect is not instrumented (#11936)
- feat(express): Warn if express is not instrumented (#11930)
- feat(fastify): Warn if fastify is not instrumented (#11917)
- feat(hapi): Warn if hapi is not instrumented (#11937)
- feat(koa): Warn if koa is not instrumented (#11931)
- fix(browser): Continuously record CLS web vital (#11934)
- fix(feedback): Pick user from any scope (#11928)
- fix(node): Fix cron instrumentation and add tests (#11811)

## 8.0.0-rc.1

This release contains no changes and was done for technical purposes. This version is considered stable.

For the sake of completeness this changelog entry includes the changes from the previous release candidate:

We recommend to read the detailed [migration guide](https://docs.sentry.io/platforms/javascript/migration/v7-to-v8/) in
the docs.

### Important Changes

- **feat(node): Support hapi v21 & fix E2E test (#11906)**

We now support hapi v21 and added tests for it.

- **feat(node): Warn if ESM mode is detected (#11914)**

When running Sentry in ESM mode, we will now warn you that this is not supported as of now. We are working on ensuring
support with ESM builds.

### Other Changes

- feat(feedback): Iterate on css for better scrolling & resizing when browser is small (#11893)
- fix(node): Ensure prisma integration creates valid DB spans (#11908)
- fix(node): Include loader hook files in package.json (#11911)

## 8.0.0-rc.0

This is the first release candidate of Sentry JavaScript SDK v8.

We recommend to read the detailed [migration guide](https://docs.sentry.io/platforms/javascript/migration/v7-to-v8/) in
the docs.

### Important Changes

- **feat(node): Support hapi v21 & fix E2E test (#11906)**

We now support hapi v21 and added tests for it.

- **feat(node): Warn if ESM mode is detected (#11914)**

When running Sentry in ESM mode, we will now warn you that this is not supported as of now. We are working on ensuring
support with ESM builds.

### Other Changes

- feat(feedback): Iterate on css for better scrolling & resizing when browser is small (#11893)
- fix(node): Ensure prisma integration creates valid DB spans (#11908)
- fix(node): Include loader hook files in package.json (#11911)

## 8.0.0-beta.6

This beta release contains various bugfixes and improvements for the v8 beta cycle.

- feat: Add `tunnel` support to multiplexed transport (#11806)
- feat: Export `spanToBaggageHeader` utility (#11881)
- feat(browser): Disable standalone `http.client` spans (#11879)
- feat(ember): Update ember dependencies (#11753)
- feat(fedback): Convert CDN bundles to use async feedback for lower bundle sizes (#11791)
- feat(feedback): Add `captureFeedback` method (#11428)
- feat(feedback): Have screenshot by default (#11839)
- feat(integrations): Add zod integration (#11144)
- feat(ioredis): Add integration for `ioredis` (#11856)
- feat(nextjs): Add transaction name to scope of server component (#11850)
- feat(nextjs): Be smarter in warning about old ways of init configuration (#11882)
- feat(nextjs): Set transaction names on scope for route handlers and generation functions (#11869)
- feat(node): Support Node 22 (#11871)
- fix(angular): Run tracing calls outside Angular (#11748)
- fix(feedback): Be consistent about whether screenshot should and can render (#11859)
- fix(nestjs): Ensure Nest.js interceptor works with non-http context (#11880)
- fix(node): Fix nest.js error handler (#11874)
- fix(react): Fix react router v4/v5 instrumentation (#11855)
- ref: Add geo location types (#11847)

## 8.0.0-beta.5

This beta release contains various bugfixes and improvements for the v8 beta cycle.

### Important Changes

- **feat(svelte): Add Svelte 5 support (#11807)**

We now officially support Svelte 5.

- **feat(browser): Send standalone fetch and XHR spans if there's no active parent span (#11783)**

Starting with this version, spans for outgoing fetch/xhr requests will be captured even if no pageload/navigation span
is ongoing. This means that you will be able to have a more complete trace, especially for web applications that make a
lot of HTTP requests on longer lived pages.

### Other Changes

- feat(astro): Add `transactionName` to isolation scope for requests (#11786)
- feat(browser): Create standalone INP spans via `startInactiveSpan` (#11788)
- feat(core): Add `trace` envelope header to span envelope (#11699)
- feat(core): Add options to start standalone (segment) spans via `start*Span` APIs (#11696)
- feat(core): Set default scope for BaseClient methods (#11775)
- feat(core): Wrap cron `withMonitor` callback in `withIsolationScope` (#11797)
- feat(feedback): New feedback button design (#11641)
- feat(nextjs): Add `transactionName` to isolation scope for Next.js server side features (#11782)
- feat(nextjs): Mute webpack warnings about critical dependencies inside `@opentelemetry/instrumentation` (#11810)
- feat(node): Upgrade @prisma/instrumentation to 5.13.0 (#11779)
- feat(react): type error as unknown in ErrorBoundary (#11819)
- feat(remix): Add `wrapHandleErrorWithSentry` (#10370)
- feat(remix): Set `formData` as `action` span data. (#10836)
- feat(remix): Update scope `transactionName` for Remix server features (#11784)
- fix(angular): Call `showReportDialog` in root context (#11703)
- fix(core): Capture only failed console.assert calls (#11799)
- fix(ember): Ensure unnecessary spans are avoided (#11846)
- fix(feedback): Clarify the difference between createWidget and create Form in the feedback public api (#11838)
- fix(feedback): Fix feedback type (#11787)
- fix(feedback): Vendor preact into bundle (#11845)
- fix(remix): Rethrow `loader`, `action` and `documentRequest` errors (#11793)
- ref: Always return an immediately generated event ID from `captureException()`, `captureMessage()`, and
  `captureEvent()` (#11805)
- ref(core): Remove transaction name extraction from `requestDataIntegration` (#11513)
- ref(svelte): Use `onlyIfParent` for recording component update spans (#11809)

## 8.0.0-beta.4

### Important Changes

- **feat(browser): Add INP support for v8 (#11650)**

INP web vital support was now forward-ported to version 8. Recording of INP data is enabled by default.

- **feat(core): Increase default transport buffer size from 30 to 64 (#11764)**

The default limit of queued events to be sent was increased from 30 to 64 events. You may observe a higher memory
footprint of the SDK. You can override this limit by setting the `transportOptions.bufferSize` option in
`Sentry.init()`.

- **feat(replay): Add "maxCanvasSize" option for replay canvases (#11617)**

A `maxCanvasSize` option was added to the `replayCanvasIntegration` to disallow capturing of canvases larger than a
certain size. This value defaults to `1280` which will not capture canvases bigger than 1280x1280 pixels.

### Other Changes

- deps: Downgrade `@opentelemetry/instrumentation-http` to `0.48.0` (#11745)
- deps(nextjs): Remove unnecessary and faulty `@opentelemetry/api` dependency from Next.js package (#11717)
- feat(aws): Add OTEL based integrations (#11548)
- feat(core): Ensure trace context only includes relevant data (#11713)
- feat(deps): Bump @opentelemetry/instrumentation-fastify from 0.33.0 to 0.35.0 (#11690)
- feat(deps): Bump @opentelemetry/instrumentation-graphql from 0.37.0 to 0.39.0 (#11692)
- feat(deps): Bump @opentelemetry/instrumentation-http from 0.48.0 to 0.50.0 (#11725)
- feat(deps): Bump @opentelemetry/instrumentation-mongoose from 0.35.0 to 0.37.0 (#11693)
- feat(deps): Bump @opentelemetry/instrumentation-mysql2 from 0.35.0 to 0.37.0 (#11726)
- feat(deps): Bump @opentelemetry/instrumentation-nestjs-core from 0.34.0 to 0.36.0 (#11727)
- feat(deps): Bump @opentelemetry/sdk-metrics from 1.21.0 to 1.23.0 (#11695)
- feat(deps): Bump @prisma/instrumentation from 5.9.0 to 5.12.1 (#11724)
- feat(feedback): Create async bundles and code to resolve helper integrations (#11621)
- feat(nextjs): Sample out low-quality spans on older Next.js versions (#11722)
- feat(opentelemetry): Support new http method attribute (#11756)
- feat(opentelemetry): Use rest args for addOpenTelemetryInstrumentation (#11721)
- feat(replay): Upgrade rrweb packages to 2.15.0 (#11736)
- fix(browser): Ensure `lazyLoadIntegration` works in NPM mode (#11673)
- fix(browser): Set custom sentry source correctly (#11735)
- fix(ember): Do not create rendering spans without transaction (#11749)
- fix(serverless): Check if cloud event callback is a function (#9044) (#11701)
- ref(nextjs): Remove unnecessary logic to filter symbolification/sentry spans (#11714)

## 8.0.0-beta.3

### Important Changes

- **feat(opentelemetry): Add `addOpenTelemetryInstrumentation` (#11667)**

A utility function `addOpenTelemetryInstrumentation` was added that allows for the registration of instrumentations that
conform to the OpenTelemetry JS API without having to specify `@opentelemetry/instrumentation` as a dependency.

- **ref(core): Don't start transaction for trpc middleware (#11697)**

Going forward, the Sentry `trpcMiddleware` will only create spans. Previously it used to always create a transaction.
This change was made to integrate more nicely with the HTTP instrumentation added in earlier versions to avoid creating
unnecessary transactions.

### Other Changes

- feat(nextjs): Instrument outgoing http requests (#11685)
- feat(opentelemetry): Remove setupGlobalHub (#11668)
- fix: Missing ErrorEvent export are added to node, browser, bun, deno, vercel-edge sub-packages (#11649)
- fix(nextjs): Do not sample next spans if they have remote parent (#11680)
- fix(nextjs): Re-enable OTEL fetch instrumentation and disable Next.js fetch instrumentation (#11686)
- fix(node): Ensure DSC on envelope header uses root span (#11683)
- ref(browser): Streamline pageload span creation and scope handling (#11679)
- ref(core): Directly use endSession (#11669)

## 8.0.0-beta.2

### Important Changes

- **feat(browser): Update `propagationContext` on `spanEnd` to keep trace consistent**

To ensure consistency throughout a route's duration, we update the scope's propagation context when the initial page
load or navigation span ends. This keeps span-specific attributes like the sampled decision and dynamic sampling context
on the scope, even after the transaction has ended.

- **fix(browser): Don't assume window.document is available (#11602)**

We won't assume `window.dodument` is available in the browser SDKs anymore. This should prevent errors in environments
where `window.document` is not available (such as web workers).

### Other changes

- feat(core): Add `server.address` to browser `http.client` spans (#11634)
- feat(opentelemetry): Update OTEL packages & relax some version ranges (#11580)
- feat(deps): bump @opentelemetry/instrumentation-hapi from 0.34.0 to 0.36.0 (#11496)
- feat(deps): bump @opentelemetry/instrumentation-koa from 0.37.0 to 0.39.0 (#11495)
- feat(deps): bump @opentelemetry/instrumentation-pg from 0.38.0 to 0.40.0 (#11494)
- feat(nextjs): Skip OTEL root spans emitted by Next.js (#11623)
- feat(node): Collect Local Variables via a worker (#11586)
- fix(nextjs): Escape Next.js' OpenTelemetry instrumentation (#11625)
- fix(feedback): Fix timeout on feedback submission (#11619)
- fix(node): Allow use of `NodeClient` without calling `init` (#11585)
- fix(node): Ensure DSC is correctly set in envelope headers (#11628)

## 8.0.0-beta.1

This is the first beta release of Sentry JavaScript SDK v8. With this release, there are no more planned breaking
changes for the v8 cycle.

Read the [in-depth migration guide](./MIGRATION.md) to find out how to address any breaking changes in your code. All
deprecations from the v7 cycle, with the exception of `getCurrentHub()`, have been removed and can no longer be used in
v8.

### Version Support

The Sentry JavaScript SDK v8 now supports Node.js 14.8.0 or higher. This applies to `@sentry/node` and all of our
node-based server-side sdks (`@sentry/nextjs`, `@sentry/remix`, etc.).

The browser SDKs now require
[ES2018+](https://caniuse.com/?feats=mdn-javascript_builtins_regexp_dotall,js-regexp-lookbehind,mdn-javascript_builtins_regexp_named_capture_groups,mdn-javascript_builtins_regexp_property_escapes,mdn-javascript_builtins_symbol_asynciterator,mdn-javascript_functions_method_definitions_async_generator_methods,mdn-javascript_grammar_template_literals_template_literal_revision,mdn-javascript_operators_destructuring_rest_in_objects,mdn-javascript_operators_destructuring_rest_in_arrays,promise-finally)
compatible browsers. New minimum browser versions:

- Chrome 63
- Edge 79
- Safari/iOS Safari 12
- Firefox 58
- Opera 50
- Samsung Internet 8.2

For more details, please see the [version support section in migration guide](./MIGRATION.md#1-version-support-changes).

### Package removal

The following packages will no longer be published

- [@sentry/hub](./MIGRATION.md#sentryhub)
- [@sentry/tracing](./MIGRATION.md#sentrytracing)
- [@sentry/integrations](./MIGRATION.md#sentryintegrations)
- [@sentry/serverless](./MIGRATION.md#sentryserverless)
- [@sentry/replay](./MIGRATION.md#sentryreplay)

### Initializing Server-side SDKs (Node, Bun, Next.js, SvelteKit, Astro, Remix):

Initializing the SDKs on the server-side has been simplified. More details in our migration docs about
[initializing the SDK in v8](./MIGRATION.md/#initializing-the-node-sdk).

### Performance Monitoring Changes

The API around performance monitoring and tracing has been vastly improved, and we've added support for more
integrations out of the box.

- [Performance Monitoring API](./MIGRATION.md#performance-monitoring-api)
- [Performance Monitoring Integrations](./MIGRATION.md#performance-monitoring-integrations)

### Important Changes since v8.0.0-alpha.9

- **feat(browser): Create spans as children of root span by default (#10986)**

Because execution context isolation in browser environments does not work reliably, we deciced to keep a flat span
hierarchy by default in v8.

- **feat(core): Deprecate `addTracingExtensions` (#11579)**

Instead of calling `Sentry.addTracingExtensions()` if you want to use performance in a browser SDK without using
`browserTracingIntegration()`, you should now call `Sentry.registerSpanErrorInstrumentation()`.

- **feat(core): Implement `suppressTracing` (#11468)**

You can use the new `suppressTracing` API to ensure a given callback will not generate any spans:

```js
return Sentry.suppressTracing(() => {
  // Ensure this fetch call does not generate a span
  return fetch('/my-url');
});
```

- **feat: Rename ESM loader hooks to `import` and `loader` (#11498)**

We renamed the loader hooks for better clarity:

```sh
# For Node.js <= 18.18.2
node --loader=@sentry/node/loader app.js

# For Node.js >= 18.19.0
node --import=@sentry/node/import app.js
```

- **feat(node): Do not exit process by default when other `onUncaughtException` handlers are registered in
  `onUncaughtExceptionIntegration` (#11532)**

In v8, we will no longer exit the node process by default if other uncaught exception handlers have been registered by
the user.

- **Better handling of transaction name for errors**

We improved the way we keep the transaction name for error events, even when spans are not sampled or performance is
disabled.

- feat(fastify): Update scope `transactionName` when handling request (#11447)
- feat(hapi): Update scope `transactionName` when handling request (#11448)
- feat(koa): Update scope `transactionName` when creating router span (#11476)
- feat(sveltekit): Update scope transactionName when handling server-side request (#11511)
- feat(nestjs): Update scope transaction name with parameterized route (#11510)

### Removal/Refactoring of deprecated functionality

- feat(core): Remove `getCurrentHub` from `AsyncContextStrategy` (#11581)
- feat(core): Remove `getGlobalHub` export (#11565)
- feat(core): Remove `Hub` class export (#11560)
- feat(core): Remove most Hub class exports (#11536)
- feat(nextjs): Remove webpack 4 support (#11605)
- feat(vercel-edge): Stop using hub (#11539)

### Other Changes

- feat: Hoist `getCurrentHub` shim to core as `getCurrentHubShim` (#11537)
- feat(core): Add default behaviour for `rewriteFramesIntegration` in browser (#11535)
- feat(core): Ensure replay envelopes are sent in order when offline (#11413)
- feat(core): Extract errors from props in unkown inputs (#11526)
- feat(core): Update metric normalization (#11518)
- feat(feedback): Customize feedback placeholder text color (#11417)
- feat(feedback): Maintain v7 compat in the @sentry-internal/feedback package (#11461)
- feat(next): Handle existing root spans for isolation scope (#11479)
- feat(node): Ensure tracing without performance (TWP) works (#11564)
- feat(opentelemetry): Export `getRequestSpanData` (#11508)
- feat(opentelemetry): Remove otel.attributes in context (#11604)
- feat(ratelimit): Add metrics rate limit (#11538)
- feat(remix): Skip span creation for `OPTIONS` and `HEAD` requests. (#11149)
- feat(replay): Merge packages together & ensure bundles are built (#11552)
- feat(tracing): Adds span envelope and datacategory (#11534)
- fix(browser): Ensure pageload trace remains active after pageload span finished (#11600)
- fix(browser): Ensure tracing without performance (TWP) works (#11561)
- fix(nextjs): Fix `tunnelRoute` matching logic for hybrid cloud (#11576)
- fix(nextjs): Remove Http integration from Next.js (#11304)
- fix(node): Ensure isolation scope is correctly cloned for non-recording spans (#11503)
- fix(node): Make fastify types more broad (#11544)
- fix(node): Send ANR events without scope if event loop blocked indefinitely (#11578)
- fix(tracing): Fixes latest route name and source not updating correctly (#11533)
- ref(browser): Move browserTracing into browser pkg (#11484)
- ref(feedback): Configure font size (#11437)
- ref(feedback): Refactor Feedback types into @sentry/types and reduce the exported surface area (#11355)

## 8.0.0-beta.0

This release failed to publish correctly. Use 8.0.0-beta.1 instead.

## 8.0.0-alpha.9

This is the eighth alpha release of Sentry JavaScript SDK v8, which includes a variety of breaking changes.

Read the [in-depth migration guide](./MIGRATION.md) to find out how to address any breaking changes in your code.

### Important Changes

- **feat: Add @sentry-internal/browser-utils (#11381)**

A big part of the browser-runtime specific exports of the internal `@sentry/utils` package were moved into a new package
`@sentry-internal/browser-utils`. If you imported any API from `@sentry/utils` (which is generally not recommended but
necessary for some workarounds), please check that your import statements still point to existing exports after
upgrading.

- **feat: Add loader file to node-based SDKs to support ESM monkeypatching (#11338)**

When using ESM, it is necessary to use a "loader" to be able to instrument certain third-party packages and Node.js API.
The server-side SDKs now ship with a set of ESM loader hooks, that should be used when using ESM. Use them as follows:

```sh
# For Node.js <= 18.18.2
node --experimental-loader=@sentry/node/hook your-app.js

# For Node.js >= 18.19.0
node --import=@sentry/node/register your-app.js
```

Please note that due to an upstream bug, these loader hooks will currently crash or simply not work. We are planning to
fix this in upcoming versions of the SDK - definitely before a stable version 8 release.

- **feat(node): Add Koa error handler (#11403)**
- **feat(node): Add NestJS error handler (#11375)**

The Sentry SDK now exports integrations and error middlewares for Koa (`koaIntegration()`, `setupKoaErrorHandler()`) and
NestJS (`setupNestErrorHandler()`) that can be used to instrument your Koa and NestJS applications with error
monitoring.

### Removal/Refactoring of deprecated functionality

- feat(core): Remove hub check in isSentryRequestUrl (#11407)
- feat(opentelemetry): Remove top level startActiveSpan (#11380)
- feat(types): `beforeSend` and `beforeSendTransaction` breaking changes (#11354)
- feat(v8): Remove all class based integrations (#11345)
- feat(v8/core): Remove span.attributes top level field (#11378)
- ref: Remove convertIntegrationFnToClass (#11343)
- ref(node): Remove the old `node` package (#11322)
- ref(tracing): Remove `span.startChild()` (#11376)
- ref(v8): Remove `addRequestDataToTransaction` util (#11369)
- ref(v8): Remove `args` on `HandlerDataXhr` (#11373)
- ref(v8): Remove `getGlobalObject` utility method (#11372)
- ref(v8): Remove `metadata` on transaction (#11397)
- ref(v8): Remove `pushScope`, `popScope`, `isOlderThan`, `shouldSendDefaultPii` from hub (#11404)
- ref(v8): Remove `shouldCreateSpanForRequest` from vercel edge options (#11371)
- ref(v8): Remove deprecated `_reportAllChanges` option (#11393)
- ref(v8): Remove deprecated `scope.getTransaction()` (#11365)
- ref(v8): Remove deprecated methods on scope (#11366)
- ref(v8): Remove deprecated span & transaction properties (#11400)
- ref(v8): Remove Transaction concept (#11422)

### Other Changes

- feat: Add `trpcMiddleware` back to serverside SDKs (#11374)
- feat: Implement timed events & remove `transaction.measurements` (#11398)
- feat(browser): Bump web-vitals to 3.5.2 (#11391)
- feat(feedback): Add `getFeedback` utility to get typed feedback instance (#11331)
- feat(otel): Do not sample `options` and `head` requests (#11467)
- feat(remix): Update scope `transactionName` when resolving route (#11420)
- feat(replay): Bump `rrweb` to 2.12.0 (#11314)
- feat(replay): Use data sentry element as fallback for the component name (#11383)
- feat(sveltekit): Update scope `transactionName` when pageload route name is updated (#11406)
- feat(tracing-internal): Reset propagation context on navigation (#11401)
- feat(types): Add View Hierarchy types (#11409)
- feat(utils): Use `globalThis` (#11351)
- feat(vue): Update scope's `transactionName` when resolving a route (#11423)
- fix(core): unref timer to not block node exit (#11430)
- fix(node): Fix baggage propagation (#11363)
- fix(web-vitals): Check for undefined navigation entry (#11311)
- ref: Set preserveModules to true for browser packages (#11452)
- ref(core): Remove duplicate logic in scope.update (#11384)
- ref(feedback): Add font family style to actor (#11432)
- ref(feedback): Add font family to buttons (#11414)
- ref(gcp-serverless): Remove setting `.__sentry_transaction` (#11346)
- ref(nextjs): Replace multiplexer with conditional exports (#11442)

## 8.0.0-alpha.8

This is a partially broken release and was superseded by version `8.0.0-alpha.9`.

## 8.0.0-alpha.7

This is the seventh alpha release of Sentry JavaScript SDK v8, which includes a variety of breaking changes.

Read the [in-depth migration guide](./MIGRATION.md) to find out how to address any breaking changes in your code.

### Important Changes

- **feat(nextjs): Use OpenTelemetry for performance monitoring and tracing (#11016)**

We now use OpenTelemetry under the hood to power performance monitoring and tracing in the Next.js SDK.

- **feat(v8/gatsby): Update SDK initialization for gatsby (#11292)**

In v8, you cannot initialize the SDK anymore via Gatsby plugin options. Instead, you have to configure the SDK in a
`sentry.config.js` file.

We also removed the automatic initialization of `browserTracingIntegration`. You now have to add this integration
yourself.

### Removal/Refactoring of deprecated functionality

- feat(v8): Remove addGlobalEventProcessor (#11255)
- feat(v8): Remove deprecated span id fields (#11180)
- feat(v8): Remove makeMain export (#11278)
- feat(v8/core): Remove deprecated span.sampled (#11274)
- feat(v8/core): Remove getActiveTransaction (#11280)
- feat(v8/core): Remove spanMetadata field (#11271)
- feat(v8/ember): Remove deprecated StartTransactionFunction (#11270)
- feat(v8/replay): Remove deprecated replay options (#11268)
- feat(v8/svelte): Remove deprecated componentTrackingPreprocessor export (#11277)
- ref: Remove more usages of getCurrentHub in the codebase (#11281)
- ref(core): Remove `scope.setSpan()` and `scope.getSpan()` methods (#11051)
- ref(profiling-node): Remove usage of getCurrentHub (#11275)
- ref(v8): change integration.setupOnce signature (#11238)
- ref: remove node-experimental references (#11290)

### Other Changes

- feat(feedback): Make "required" text for input elements configurable (#11152) (#11153)
- feat(feedback): Update user feedback screenshot and cropping to align with designs (#11227)
- feat(nextjs): Remove `runtime` and `vercel` tags (#11291)
- feat(node): Add scope to ANR events (#11256)
- feat(node): Do not include `prismaIntegration` by default (#11265)
- feat(node): Ensure `tracePropagationTargets` are respected (#11285)
- feat(node): Simplify `SentrySpanProcessor` (#11273)
- feat(profiling): Use OTEL powered node package (#11239)
- feat(utils): Allow text encoder/decoder polyfill from global **SENTRY** (#11283)
- fix(nextjs): Show misconfiguration warning (no `instrumentation.ts`) (#11266)
- fix(node): Add logs when node-fetch cannot be instrumented (#11289)
- fix(node): Skip capturing Hapi Boom error responses. (#11151)
- fix(node): Use `suppressTracing` to avoid capturing otel spans (#11288)
- fix(opentelemetry): Do not stomp span status when `startSpan` callback throws (#11170)

## 8.0.0-alpha.6

This version did not publish correctly due to a configuration issue.

## 8.0.0-alpha.5

This is the fifth alpha release of Sentry JavaScript SDK v8, which includes a variety of breaking changes.

Read the [in-depth migration guide](./MIGRATION.md) to find out how to address any breaking changes in your code.

### Important Changes

- **feat(nextjs): Remove `client.(server|client).config.ts` functionality in favor of `instrumentation.ts` (#11059)**
  - feat(nextjs): Bump minimum required Next.js version to `13.2.0` (#11097)

With version 8 of the SDK we will no longer support the use of `sentry.server.config.ts` and `sentry.edge.config.ts`
files. Instead, please initialize the Sentry Next.js SDK for the serverside in a
[Next.js instrumentation hook](https://nextjs.org/docs/app/building-your-application/optimizing/instrumentation).
**`sentry.client.config.ts|js` is still supported and encouraged for initializing the clientside SDK.** Please see the
[Migration Guide](./MIGRATION.md#updated-the-recommended-way-of-calling-sentryinit) for more details.

In addition, the Next.js SDK now requires a minimum Next.js version of `13.2.0`.

- **feat(v8/angular): Merge angular and angular-ivy packages and start Angular support at v14 (#11091)**

The `@sentry/angular-ivy` package has been removed. The `@sentry/angular` package now supports Ivy by default and
requires at least Angular 14. See the [Migration Guide](./MIGRATION.md#removal-of-sentryangular-ivy-package) for more
details.

### Removal/Refactoring of deprecated functionality

- feat(aws-serverless): Remove deprecated `rethrowAfterCapture` option (#11126)
- feat(node): Remove deprecated/duplicate/unused definitions (#11120)
- feat(v8): Remove deprecated integration methods on client (#11134)
- feat(v8/browser): Remove class export for linked errors (#11129)
- feat(v8/browser): Remove deprecated wrap export (#11127)
- feat(v8/core): Remove deprecated client.setupIntegrations method (#11179)
- feat(v8/core): Remove deprecated integration classes (#11132)
- feat(v8/ember): Remove InitSentryForEmber export (#11202)
- feat(v8/nextjs): Remove usage of class integrations (#11182)
- feat(v8/replay): Delete deprecated types (#11177)
- feat(v8/utils): Remove deprecated util functions (#11143)
- ref(node): Remove class based export for local variable integration (#11128)

### Other Changes

- feat(browser): Make fetch the default transport for offline (#11209)
- feat(core): Filter out noisy GoogleTag error by default (#11208)
- feat(deps): Bump @sentry/cli from 2.30.0 to 2.30.2 (#11168)
- feat(nextjs): Prefix webpack plugin log messages with runtime (#11173)
- feat(node-profiling): Output ESM and remove Sentry deps from output (#11135)
- feat(node): Allow Anr worker to be stopped and restarted (#11214)
- feat(node): Support `tunnel` option for ANR (#11163)
- feat(opentelemetry): Do not capture exceptions for timed events (#11221)
- feat(serverless): Add Node.js 20 to compatible runtimes (#11103)
- feat(sveltekit): Switch to Otel-based `@sentry/node` package (#11075)
- fix(attachments): Add missing `view_hierarchy` attachment type (#11197)
- fix(build): Ensure tree shaking works properly for ESM output (#11122)
- fix(feedback): Only allow screenshots in secure contexts (#11188)
- fix(feedback): Reduce force layout in screenshots (#11181)
- fix(feedback): Smoother cropping experience and better UI (#11165)
- fix(feedback): Fix screenshot black bars in Safari (#11233)
- fix(metrics): use correct statsd data category (#11184)
- fix(metrics): use web-vitals ttfb calculation (#11185)
- fix(node): Export `initOpenTelemetry` (#11158)
- fix(node): Clear ANR timer on stop (#11229)
- fix(node): Time zone handling for `cron` (#11225)
- fix(node): Use unique variable for ANR context transfer (#11161)
- fix(opentelemetry): Do not stomp span error status (#11169)
- fix(types): Fix incorrect `sampled` type on `Transaction` (#11115)

## 8.0.0-alpha.4

This is the fourth Alpha release of the v8 cycle, which includes a variety of breaking changes.

Read the [in-depth migration guide](./MIGRATION.md) to find out how to address any breaking changes in your code.

### Important Changes

- **feat: Set required node version to >=14.18.0 for all packages (#10968)**

The minimum Node version required for the SDK is now `14.18.0`.

- **Serverless SDK Changes**
  - feat(google-cloud): Add @sentry/google-cloud package (#10993)
  - feat(v8): Add @sentry/aws-serverless package (#11052)
  - feat(v8): Rename gcp package to `@sentry/google-cloud-serverless` (#11065)

`@sentry/serverless` is no longer published, and is replaced by two new packages: `@sentry/google-cloud-serverless` and
`@sentry/aws-serverless`. These packages are now the recommended way to instrument serverless functions. See the
[migration guide](./MIGRATION.md#sentryserverless) for more details.

- **build(bundles): Use ES2017 for bundles (drop ES5 support) (#10911)**

The Browser SDK and CDN bundles now emits ES2017 compatible code and drops support for IE11. This also means that the
Browser SDKs (`@sentry/browser`, `@sentry/react`, `@sentry/vue`, etc.) requires the fetch API to be available in the
environment. If you need to support older browsers, please transpile your code to ES5 using babel or similar and add
required polyfills.

New minimum supported browsers:

- Chrome 58
- Edge 15
- Safari/iOS Safari 11
- Firefox 54
- Opera 45
- Samsung Internet 7.2

### Removal/Refactoring of deprecated functionality

- feat(browser): Remove IE parser from the default stack parsers (#11035)
- feat(bun/v8): Remove all deprecations from Bun SDK (#10971)
- feat(core): Remove `startTransaction` export (#11015)
- feat(v8/core): Move addTracingHeadersToFetchRequest and instrumentFetchRequest to core (#10918)
- feat(v8/deno): Remove deprecations from deno SDK (#10972)
- feat(v8/remix): Remove remixRouterInstrumentation (#10933)
- feat(v8/replay): Opt-in options for `unmask` and `unblock` (#11049)
- feat(v8/tracing): Delete BrowserTracing class (#10919)
- feat(v8/vercel-edge): Remove vercel-edge sdk deprecations (#10974)
- feat(replay/v8): Delete deprecated `replaySession` and `errorSampleRates` (#11045)
- feat(v8): Remove deprecated Replay, Feedback, ReplayCanvas exports (#10814)
- ref: Remove `spanRecorder` and all related code (#10977)
- ref: Remove deprecated `origin` field on span start options (#11058)
- ref: Remove deprecated properties from `startSpan` options (#11054)
- ref(core): Remove `startTransaction` & `finishTransaction` hooks (#11008)
- ref(nextjs): Remove `sentry` field in Next.js config as a means of configuration (#10839)
- ref(nextjs): Remove last internal deprecations (#11019)
- ref(react): Streamline browser tracing integrations & remove old code (#11012)
- ref(svelte): Remove `startChild` deprecations (#10956)
- ref(sveltekit): Update trace propagation & span options (#10838)
- ref(v8/angular): Remove instrumentAngularRouting and fix tests (#11021)

### Other Changes

- feat: Ensure `getRootSpan()` does not rely on transaction (#10979)
- feat: Export `getSpanDescendants()` everywhere (#10924)
- feat: Make ESM output valid Node ESM (#10928)
- feat: Remove tags from spans & transactions (#10809)
- feat(angular): Update scope `transactionName` when route is resolved (#11043)
- feat(angular/v8): Change decorator naming and add `name` parameter (#11057)
- feat(astro): Update `@sentry/astro` to use OpenTelemetry (#10960)
- feat(browser): Remove `HttpContext` integration class (#10987)
- feat(browser): Use idle span for browser tracing (#10957)
- feat(build): Allow passing Sucrase options for rollup (#10747)
- feat(build): Core packages into single output files (#11030)
- feat(core): Allow custom tracing implementations (#11003)
- feat(core): Allow metrics aggregator per client (#10949)
- feat(core): Decouple `scope.transactionName` from root spans (#10991)
- feat(core): Ensure trace APIs always return a span (#10942)
- feat(core): Implement `startIdleSpan` (#10934)
- feat(core): Move globals to `__SENTRY__` singleton (#11034)
- feat(core): Move more scope globals to `__SENTRY__` (#11074)
- feat(core): Undeprecate setTransactionName (#10966)
- feat(core): Update `continueTrace` to be callback-only (#11044)
- feat(core): Update `spanToJSON` to handle OTEL spans (#10922)
- feat(deps): bump @sentry/cli from 2.29.1 to 2.30.0 (#11024)
- feat(feedback): New feedback integration with screenshots (#10590)
- feat(nextjs): Bump Webpack Plugin to version 2 and rework config options (#10978)
- feat(nextjs): Support Hybrid Cloud DSNs with `tunnelRoute` option (#10959)
- feat(node): Add `setupFastifyErrorHandler` utility (#11061)
- feat(node): Rephrase wording in http integration JSDoc (#10947)
- feat(opentelemetry): Do not use SentrySpan & Transaction classes (#10982)
- feat(opentelemetry): Remove hub from context (#10983)
- feat(opentelemetry): Use core `getRootSpan` functionality (#11004)
- feat(profiling-node): Refactor deprecated methods & non-hook variant (#10984)
- feat(react): Update scope's `transactionName` in React Router instrumentations (#11048)
- feat(remix): Refactor to use new performance APIs (#10980)
- feat(remix): Update remix SDK to be OTEL-powered (#11031)
- feat(sveltekit): Export `unstable_sentryVitePluginOptions` for full Vite plugin customization (#10930)
- feat(v8/bun): Update @sentry/bun to use OTEL node (#10997)
- fix(ember): Ensure browser tracing is correctly lazy loaded (#11026)
- fix(nextjs): Use passthrough `createReduxEnhancer` on server (#11005)
- fix(node): Add missing core re-exports (#10931)
- fix(node): Correct SDK name (#10961)
- fix(node): Do not assert in vendored proxy code (#11011)
- fix(node): Export spotlightIntegration from OTEL node (#10973)
- fix(node): support undici headers as strings or arrays (#10938)
- fix(opentelemetry): Fix span & sampling propagation (#11092)
- fix(react): Passes the fallback function through React's createElement function (#10623)
- fix(react): Set `handled` value in ErrorBoundary depending on fallback (#10989)
- fix(types): Add `addScopeListener` to `Scope` interface (#10952)
- fix(types): Add `AttachmentType` and use for envelope `attachment_type` property (#10946)
- fix(types): Remove usage of `allowSyntheticDefaultImports` (#11073)
- fix(v8/utils): Stack parser skip frames (not lines of stack string) (#10560)
- ref(angular): Refactor usage of `startChild` (#11056)
- ref(browser): Store browser metrics as attributes instead of tags (#10823)
- ref(browser): Update `scope.transactionName` on pageload and navigation span creation (#10992)
- ref(browser): Update browser metrics to avoid deprecations (#10943)
- ref(browser): Update browser profiling to avoid deprecated APIs (#11007)
- ref(feedback): Move UserFeedback type into feedback.ts (#11032)
- ref(nextjs): Clean up browser tracing integration (#11022)
- ref(node-experimental): Refactor usage of `startChild()` (#11047)
- ref(node): Use new performance APIs in legacy `http` & `undici` (#11055)
- ref(opentelemetry): Remove parent span map (#11014)
- ref(opentelemetry): Remove span metadata handling (#11020)

Work in this release contributed by @MFoster and @jessezhang91. Thank you for your contributions!

## 8.0.0-alpha.3

This alpha was released in an incomplete state. We recommend skipping this release and using the `8.0.0-alpha.4` release
instead.

## 8.0.0-alpha.2

This alpha release fixes a build problem that prevented 8.0.0-alpha.1 from being properly released.

### Important Changes

- **feat: Remove `@sentry/opentelemetry-node` package (#10906)**

The `@sentry/opentelemetry-node` package has been removed. Instead, you can either use `@sentry/node` with built-in
OpenTelemetry support, or use `@sentry/opentelemetry` to manually connect Sentry with OpenTelemetry.

### Removal/Refactoring of deprecated functionality

- ref: Refactor some deprecated `startSpan` options (#10825)
- feat(v8/core): remove void from transport return (#10794)
- ref(integrations): Delete deprecated class integrations (#10887)

### Other Changes

- feat(core): Use serialized spans in transaction event (#10912)
- feat(deps): bump @sentry/cli from 2.28.6 to 2.29.1 (#10908)
- feat(node): Allow to configure `skipOpenTelemetrySetup` (#10907)
- feat(esm): Import rather than require `inspector` (#10910)
- fix(browser): Don't use chrome variable name (#10874)
- chore(sveltekit): Fix punctuation in a console.log (#10895)
- fix(opentelemetry): Ensure DSC propagation works correctly (#10904)
- feat(browser): Exclude span exports from non-performance CDN bundles (#10879)
- ref: Refactor span status handling to be OTEL compatible (#10871)
- feat(core): Fix span scope handling & transaction setting (#10886)
- ref(ember): Avoid namespace import to hopefully resolve minification issue (#10885)

Work in this release contributed by @harish-talview & @bfontaine. Thank you for your contributions!

## 8.0.0-alpha.1

This is the first Alpha release of the v8 cycle, which includes a variety of breaking changes.

Read the [in-depth migration guide](./MIGRATION.md) to find out how to address any breaking changes in your code.

### Important Changes

**- feat(node): Make `@sentry/node` powered by OpenTelemetry (#10762)**

The biggest change is the switch to use OpenTelemetry under the hood in `@sentry/node`. This brings with it a variety of
changes:

- There is now automated performance instrumentation for Express, Fastify, Nest.js and Koa. You can remove any
  performance and request isolation code you previously wrote manually for these frameworks.
- All performance instrumention is enabled by default, and will only take effect if the instrumented package is used.
  You don't need to use `autoDiscoverNodePerformanceMonitoringIntegrations()` anymore.
- You need to ensure to call `Sentry.init()` _before_ you import any other packages. Otherwise, the packages cannot be
  instrumented:

```js
const Sentry = require('@sentry/node');
Sentry.init({
  dsn: '...',
  // ... other config here
});
// now require other things below this!
const http = require('http');
const express = require('express');
// ....
```

- Currently, we only support CJS-based Node application out of the box. There is experimental ESM support, see
  [the instructions](./packages/node-experimental/README.md#esm-support).
- `startTransaction` and `span.startChild()` are no longer supported. This is due to the underlying change to
  OpenTelemetry powered performance instrumentation. See
  [docs on the new performance APIs](./docs/v8-new-performance-apis.md) for details.

Related changes:

- feat(node-experimental): Add missing re-exports (#10679)
- feat(node-experimental): Move `defaultStackParser` & `getSentryRelease` (#10722)
- feat(node-experimental): Move `errorHandler` (#10728)
- feat(node-experimental): Move cron code over (#10742)
- feat(node-experimental): Move integrations from node (#10743)
- feat(node-experimental): Properly set request & session on http requests (#10676)
- feat(opentelemetry): Support `forceTransaction` in OTEL (#10807)
- feat(opentelemetry): Align span options with core span options (#10761)
- feat(opentelemetry): Do not capture span events as breadcrumbs (#10612)
- feat(opentelemetry): Ensure DSC & attributes are correctly set (#10806)
- feat(opentelemetry): Fix & align isolation scope usage in node-experimental (#10570)
- feat(opentelemetry): Merge node-experimental changes into opentelemetry (#10689)
- ref(node-experimental): Cleanup re-exports (#10741)
- ref(node-experimental): Cleanup tracing intergations (#10730)
- ref(node-experimental): Copy transport & client to node-experimental (#10720)
- ref(node-experimental): Remove custom `isInitialized` (#10607)
- ref(node-experimental): Remove custom hub & scope (#10616)
- ref(node-experimental): Remove deprecated class integrations (#10675)
- ref(node-experimental): Rename `errorHandler` to `expressErrorHandler` (#10746)
- ref(node-integration-tests): Migrate to new Http integration (#10765)
- ref(node): Align semantic attribute handling (#10827)

**- feat: Remove `@sentry/integrations` package (#10799)**

This package is no longer published. You can instead import these pluggable integrations directly from your SDK package
(e.g. `@sentry/browser` or `@sentry/react`).

**- feat: Remove `@sentry/hub` package (#10783)**

This package is no longer published. You can instead import directly from your SDK package (e.g. `@sentry/react` or
`@sentry/node`).

**- feat(v8): Remove @sentry/tracing (#10625)**

This package is no longer published. You can instead import directly from your SDK package (e.g. `@sentry/react` or
`@sentry/node`).

**- feat: Set required node version to >=14.8.0 for all packages (#10834)**

The minimum required node version is now 14.8+. If you need support for older node versions, you can stay on the v7
branch.

**- Removed class-based integrations**

We have removed most of the deprecated class-based integrations. Instead, you can use the functional styles:

```js
import * as Sentry from '@sentry/browser';
// v7
Sentry.init({
  integrations: [new Sentry.BrowserTracing()],
});
// v8
Sentry.init({
  integrations: [new Sentry.browserTracingIntegration()],
});
```

- ref: Remove `BrowserTracing` (#10653)
- feat(v8/node): Remove LocalVariables class integration (#10558)
- feat(v8/react): Delete react router exports (#10532)
- feat(v8/vue): Remove all deprecated exports from vue (#10533)
- feat(v8/wasm): Remove deprecated exports (#10552)

**- feat(v8/browser): Remove XHR transport (#10703)**

We have removed the XHR transport, and are instead using the fetch-based transport now by default. This means that if
you are using Sentry in a browser environment without fetch, you'll need to either provide a fetch polyfill, or provide
a custom transport to Sentry.

**- feat(sveltekit): Update `@sentry/vite-plugin` to 2.x and adjust options API (#10813)**

We have updated `@sentry/sveltekit` to use the latest version of `@sentry/vite-plugin`, which lead to changes in
configuration options.

### Other Changes

- feat: Ensure `withActiveSpan` is exported everywhere (#10878)
- feat: Allow passing `null` to `withActiveSpan` (#10717)
- feat: Implement new Async Context Strategy (#10647)
- feat: Remove `hub` from global, `hub.run` & hub utilities (#10718)
- feat: Update default trace propagation targets logic in the browser (#10621)
- feat: Ignore ResizeObserver and undefined error (#10845)
- feat(browser): Export `getIsolationScope` and `getGlobalScope` (#10658)
- feat(browser): Prevent initialization in browser extensions (#10844)
- feat(core): Add metric summaries to spans (#10554)
- feat(core): Decouple metrics aggregation from client (#10628)
- feat(core): Lookup client on current scope, not hub (#10635)
- feat(core): Make `setXXX` methods set on isolation scope (#10678)
- feat(core): Make custom tracing methods return spans & set default op (#10633)
- feat(core): Make global `addBreadcrumb` write to the isolation scope instead of current scope (#10586)
- feat(core): Remove health check transaction filters (#10818)
- feat(core): Streamline custom hub creation for node-experimental (#10555)
- feat(core): Update `addEventProcessor` to add to isolation scope (#10606)
- feat(core): Update `Sentry.addBreadcrumb` to skip hub (#10601)
- feat(core): Use global `TextEncoder` and `TextDecoder` (#10701)
- feat(deps): bump @sentry/cli from 2.26.0 to 2.28.0 (#10496)
- feat(deps): bump @sentry/cli from 2.28.0 to 2.28.5 (#10620)
- feat(deps): bump @sentry/cli from 2.28.5 to 2.28.6 (#10727)
- feat(integrations): Capture error arguments as exception regardless of level in `captureConsoleIntegration` (#10744)
- feat(metrics): Remove metrics method from `BaseClient` (#10789)
- feat(node): Remove unnecessary URL imports (#10860)
- feat(react): Drop support for React 15 (#10115)
- feat(remix): Add Vite dev-mode support to Express instrumentation. (#10784)
- fix: Export session API (#10711)
- fix(angular-ivy): Add `exports` field to `package.json` (#10569)
- fix(angular): Ensure navigations always create a transaction (#10646)
- fix(core): Add lost scope tests & fix update case (#10738)
- fix(core): Fix scope capturing via `captureContext` function (#10735)
- fix(feedback): Replay breadcrumb for feedback events was incorrect (#10536)
- fix(nextjs): Remove `webpack://` prefix more broadly from source map `sources` field (#10642)
- fix(node): import `worker_threads` and fix node v14 types (#10791)
- fix(node): Record local variables with falsy values, `null` and `undefined` (#10821)
- fix(stacktrace): Always use `?` for anonymous function name (#10732)
- fix(sveltekit): Avoid capturing Http 4xx errors on the client (#10571)
- fix(sveltekit): Ensure navigations and redirects always create a new transaction (#10656)
- fix(sveltekit): Properly await sourcemaps flattening (#10602)
- fix(types): Improve attachment type (#10832)
- fx(node): Fix anr worker check (#10719)
- ref: Cleanup browser profiling integration (#10766)
- ref: Collect child spans references via non-enumerable on Span object (#10715)
- ref: Make scope setters on hub only write to isolation scope (#10572)
- ref: Store runtime on isolation scope (#10657)
- ref(astro): Put request as SDK processing metadata instead of span data (#10840)
- ref(core): Always use a (default) ACS (#10644)
- ref(core): Make `on` and `emit` required on client (#10603)
- ref(core): Make remaining client methods required (#10605)
- ref(core): Rename `Span` class to `SentrySpan` (#10687)
- ref(core): Restructure hub exports (#10639)
- ref(core): Skip hub in top level `captureXXX` methods (#10688)
- ref(core): Allow `number` as span `traceFlag` (#10855)
- ref(core): Remove `status` field from Span (#10856)
- ref(remix): Make `@remix-run/router` a dependency. (#10479)
- ref(replay): Use `beforeAddBreadcrumb` hook instead of scope listener (#10600)
- ref(sveltekit): Hard-pin Vite plugin version (#10843)

### Other Deprecation Removals/Changes

We have also removed or updated a variety of deprecated APIs.

- feat(v8): Remove `extractTraceparentData` export (#10559)
- feat(v8): Remove defaultIntegrations deprecated export (#10691)
- feat(v8): Remove deprecated `span.isSuccess` method (#10699)
- feat(v8): Remove deprecated `traceHeaders` method (#10776)
- feat(v8): Remove deprecated addInstrumentationHandler (#10693)
- feat(v8): Remove deprecated configureScope call (#10565)
- feat(v8): Remove deprecated runWithAsyncContext API (#10780)
- feat(v8): Remove deprecated spanStatusfromHttpCode export (#10563)
- feat(v8): Remove deprecated trace and startActiveSpan methods (#10593)
- feat(v8): Remove requestData deprecations (#10626)
- feat(v8): Remove Severity enum (#10551)
- feat(v8): Remove span.origin (#10753)
- feat(v8): Remove span.toTraceparent method (#10698)
- feat(v8): Remove usage of span.description and span.name (#10697)
- feat(v8): Update eventFromUnknownInput to only use client (#10692)
- feat(v8/astro): Remove deprecated exports from Astro SDK (#10611)
- feat(v8/browser): Remove `_eventFromIncompleteOnError` usage (#10553)
- feat(v8/browser): Remove XHR transport (#10703)
- feat(v8/browser): Rename TryCatch integration to `browserApiErrorsIntegration` (#10755)
- feat(v8/core): Remove deprecated setHttpStatus (#10774)
- feat(v8/core): Remove deprecated updateWithContext method (#10800)
- feat(v8/core): Remove getters for span.op (#10767)
- feat(v8/core): Remove span.finish call (#10773)
- feat(v8/core): Remove span.instrumenter and instrumenter option (#10769)
- feat(v8/ember): Remove deprecated exports (#10535)
- feat(v8/integrations): Remove deprecated exports (#10556)
- feat(v8/node): Remove deepReadDirSync export (#10564)
- feat(v8/node): Remove deprecated anr methods (#10562)
- feat(v8/node): Remove getModuleFromFilename export (#10754)
- feat(core): Remove deprecated props from `Span` interface (#10854)
- fix(v8): Remove deprecated tracing config (#10870)
- ref: Make `setupOnce` optional in integrations (#10729)
- ref: Migrate transaction source from metadata to attributes (#10674)
- ref: Refactor remaining `makeMain` usage (#10713)
- ref(astro): Remove deprecated Replay and BrowserTracing (#10768)
- feat(core): Remove deprecated `scope.applyToEvent()` method (#10842)
- ref(integrations): Remove offline integration (#9456)
- ref(nextjs): Remove all deprecated API (#10549)
- ref: Remove `lastEventId` (#10585)
- ref: Remove `reuseExisting` option for ACS (#10645)
- ref: Remove `tracingOrigins` options (#10614)
- ref: Remove deprecated `showReportDialog` APIs (#10609)
- ref: Remove usage of span tags (#10808)
- ref: Remove user segment (#10575)

## 7.x

A full list of changes in the `7.x` release of the SDK can be found in the [7.x Changelog](./docs/changelog/v7.md).

## 6.x

A full list of changes in the `6.x` release of the SDK can be found in the [6.x Changelog](./docs/changelog/v6.md).

## 5.x

A full list of changes in the `5.x` release of the SDK can be found in the [5.x Changelog](./docs/changelog/v5.md).

## 4.x

A full list of changes in the `4.x` release of the SDK can be found in the [4.x Changelog](./docs/changelog/v4.md).
