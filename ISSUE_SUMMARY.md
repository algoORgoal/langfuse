# Build Hanging Issue with `custom-intelligence` and `langfuse-web`

## Note on `custom-intelligence` Directory

- The `custom-intelligence` directory was **not part of the original repository**. It is a custom service added to the monorepo, intended to provide additional intelligence or analytics features alongside the main Langfuse services. Its structure and build process are similar to the official services, but it is maintained separately and may have unique dependencies or configuration requirements.

## Context

- Monorepo using TurboRepo, Next.js, and Docker Compose.
- Services: `custom-intelligence`, `langfuse-web`, and `langfuse-worker`.
- Building with: `docker compose -f docker-compose.build.yml up --build`

## Symptoms

- `langfuse-worker` builds successfully.
- `custom-intelligence` and `langfuse-web` hang for a very long time (10+ minutes) at the build step:
  ```sh
  RUN NODE_OPTIONS='--max-old-space-size=4096' turbo run build --filter=custom-intelligence...
  # or
  RUN NODE_OPTIONS='--max-old-space-size=4096' turbo run build --filter=web...
  ```
- No out-of-memory or crash error; just extremely slow or seemingly infinite build.
- Memory and disk space have been checked and are not exhausted.

## Troubleshooting Steps Taken

1. **Memory Limits**
   - Tried various `--max-old-space-size` values (3GB, 4GB, 12GB).
   - Docker Desktop memory set to 12GB (host has 16GB RAM).
2. **Disk Space**
   - Verified sufficient disk space on host and Docker VM.
3. **Build Outside Docker**
   - (TODO: To be tried) Run `pnpm build` or `yarn build` locally to see if the issue is Docker-specific.
4. **Environment Variables**
   - Checked for missing required env vars.
   - Considered using `SKIP_ENV_VALIDATION=1` to skip env validation during build.
5. **Codebase Review**
   - Inspected `next.config.mjs`, `src/env.mjs`, and `entrypoint.sh` for infinite loops or blocking code.
   - No obvious infinite loops or blocking calls found.
6. **Build Output**
   - No `.next` directory present (build never completes).
   - No errors in logs, just a hang.

## Remaining Questions / Help Needed

- What could cause `next build` (via TurboRepo) to hang indefinitely without errors?
- Are there known issues with environment validation, TurboRepo, or Next.js that could cause this?
- What additional debugging steps can be taken to isolate the root cause?
- Is there a way to get more verbose logs from TurboRepo or Next.js during the build?

## Additional Info

- Large OpenAPI YAML file in `public/generated/api/openapi.yml` (192KB, 7466 lines), but not huge enough to explain the hang alone.
- No custom build scripts or hooks found that obviously block the build.

---

**Any advice or pointers would be greatly appreciated!**
