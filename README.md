# Dew QR OpenAPI Code Generation

This project uses the OpenAPI Generator to generate Java and TypeScript code from the OpenAPI specification. Follow the steps below to generate the respective code.

## Prerequisites

- Ensure you have Maven installed on your system.
- For TypeScript generation, ensure you have Node.js and npm installed.

---

## Generating Java Code

The Java code is generated using the `openapi-generator-maven-plugin` configured in the `pom.xml`.

### Steps:

1. Place your OpenAPI specification file (`openapi.yaml`) in the `packages/api` directory.
2. Run the following Maven command to generate the Java code:

   ```bash
   mvn clean compile
   ```

3. The generated Java code will be located in the `target/generated-sources/openapi/src/main/java` directory.

---

## Generating TypeScript Code

To generate TypeScript code, you can use the OpenAPI Generator CLI.

### Steps:

1. Install the OpenAPI Generator CLI globally using npm. If you encounter a permission error (`EACCES`), use one of the following solutions:

   **Option 1: Use `sudo` (not recommended for security reasons):**
   ```bash
   sudo npm install -g @openapitools/openapi-generator-cli
   ```

   **Option 2: Change npm's default directory to avoid permission issues:**
   ```bash
   mkdir -p ~/.npm-global
   npm config set prefix '~/.npm-global'
   export PATH=~/.npm-global/bin:$PATH
   source ~/.bashrc
   npm install -g @openapitools/openapi-generator-cli
   ```

   **Option 3: Use `npx` to avoid global installation:**
   ```bash
   npx @openapitools/openapi-generator-cli generate \
     -i packages/api/openapi.yaml \
     -g typescript-fetch \
     -o target/generated-sources/openapi/typescript
   ```

2. Run the following command to generate TypeScript code:

   ```bash
   openapi-generator-cli generate \
     -i packages/api/openapi.yaml \
     -g typescript-fetch \
     -o target/generated-sources/openapi/typescript
   ```

   - `-i`: Path to the OpenAPI specification file.
   - `-g`: Specifies the generator type (`typescript-fetch` for TypeScript code).
   - `-o`: Output directory for the generated TypeScript code.

3. The generated TypeScript code will be located in the `target/generated-sources/openapi/typescript` directory.

---

## Notes

- Ensure the OpenAPI specification file (`openapi.yaml`) is valid before running the generation commands.
- You can customize the generation options by modifying the `pom.xml` for Java or passing additional CLI arguments for TypeScript.

For more details, refer to the [OpenAPI Generator documentation](https://openapi-generator.tech/docs/).# dew-qr
