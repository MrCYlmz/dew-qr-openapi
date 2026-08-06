# dew-qr-openapi

The API contract for **DewQr**, and the generated Java/TypeScript code both other projects depend on. This is the single source of truth for the DewQr HTTP API — [`dew-qr`](../dew-qr) (backend) and [`dew-qr-ui`](../dew-qr-ui) (frontend) never hand-write request/response types; they consume packages generated from the spec here.

## Layout

```
packages/
  api/
    openapi.yaml              # root spec: paths, tags, servers, security schemes
    components/<domain>/      # per-domain path + schema definitions, $ref'd from openapi.yaml
      api_description.yaml    # operations for that domain
      model.yaml               # request/response schemas for that domain
  typescript/
    src/index.ts               # hand-written entry point re-exporting the generated client
    src/gen/                   # generated typescript-axios client (models/ + api/)
```

Domains: `approval` (pending user approve/reject), `auth` (login), `group` (dining-party groups), `items` (menu items + images), `order`, `table`.

## Prerequisites

- Maven, for the Java generation and for publishing the Java package
- Node.js + npm, for the TypeScript generation and for publishing the npm package

## Changing the API contract

1. Edit `packages/api/openapi.yaml` and/or the relevant `packages/api/components/<domain>/{api_description,model}.yaml`.
2. Regenerate the Java server interfaces (uses the `openapi-generator-maven-plugin`, generator `spring`, configured in `pom.xml`):

   ```bash
   mvn clean compile
   ```

   Output: `target/generated-sources/openapi/src/main/java` (package `com.mrdew.dew-qr.openapi.{api,model}`). This generates **interfaces only** (`interfaceOnly=true`) — e.g. `AdminApi`, `UserApi`, `AuthApi` — which `dew-qr`'s controllers implement; no controller bodies are generated. Model classes get an `ApiDTO` suffix.

3. Regenerate the TypeScript client (config in `openapitools.json`, generator `typescript-axios`):

   ```bash
   npx @openapitools/openapi-generator-cli generate
   ```

   Output: `packages/typescript/src/gen/` (separate `models/` and `api/` folders, enums `UPPERCASE`). If you hit an `EACCES` installing the CLI globally, prefer `npx` as above, or point `npm config set prefix '~/.npm-global'` at a user-owned directory instead of using `sudo`.

4. Validate the spec is well-formed before generating (the Maven plugin runs with `skipValidateSpec=true`, so nothing will catch a broken spec for you):

   ```bash
   npx @openapitools/openapi-generator-cli validate -i packages/api/openapi.yaml
   ```

## Publishing

Both generated artifacts are versioned and published so `dew-qr` and `dew-qr-ui` can pin an exact contract version:

- **Java** → `com.mrdew:dew-qr-openapi` on GitHub Packages (`maven.pkg.github.com/MrCYlmz/dew-qr-openapi`), via `mvn deploy`.
- **TypeScript** → `@mrcylmz/dewqr-api-generator` on GitHub Packages (`npm.pkg.github.com`), via `npm publish` from `packages/typescript`.

After changing the contract, bump the version in `pom.xml` / `packages/typescript/package.json`, publish, then update the dependency version in `dew-qr/pom.xml` and `dew-qr-ui/package.json` respectively — the two consumers do not auto-track the latest contract.

## Notes

- Keep `packages/api/openapi.yaml` valid — the generators will silently produce broken or partial output on a malformed spec since validation is skipped in the Maven build.
- Custom type mappings (e.g. OpenAPI `time` → Java `LocalTime`) live in the `configOptions`/`typeMappings` block of `pom.xml` — extend there rather than post-processing generated code.
- For generator options beyond what's configured here, see the [OpenAPI Generator documentation](https://openapi-generator.tech/docs/).
