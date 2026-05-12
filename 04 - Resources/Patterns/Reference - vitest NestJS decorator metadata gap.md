---
type: reference
tags: [vitest, nestjs, esbuild, decorator-metadata, dependency-injection, cross-workspace]
created: 2026-05-12
updated: 2026-05-12
applies-to: [D:/ECOSIRE.COM, any-future-NestJS-workspace]
---

# vitest + esbuild does NOT emit TypeScript decorator metadata

## The rule

When a NestJS controller is exercised by an integration test using `Test.createTestingModule({ imports: [...] }).compile()` AND the controller source lives in `apps/<api>/src/` (compiled on-the-fly by vitest at test time), **always add explicit `@Inject(ServiceClass)` to each constructor parameter**. Do not rely on TypeScript constructor-parameter type metadata.

## Why

vitest uses esbuild under the hood for TypeScript compilation. esbuild does NOT emit `design:paramtypes` reflect-metadata even when `tsconfig.json` declares `emitDecoratorMetadata: true`. Without that metadata, NestJS's reflective DI can't resolve constructor parameter types and silently passes `undefined`. The controller boots, the HTTP request arrives, and the very first `this.someService.method()` call throws `TypeError: Cannot read properties of undefined (reading 'method')`.

Pre-built packages (e.g. `@ecosire/auth-core` compiled to `dist/` by tsc before tests run) work fine because tsc emits the metadata into the compiled .js files. Only source files compiled at vitest runtime hit this gap.

## How to apply

| Layer | What works | What breaks |
|---|---|---|
| Unit tests (direct instantiation: `new SomeService(mockDep)`) | ✅ Always — no DI involved | — |
| Mocked integration tests (`vi.mock` + manually wired stubs) | ✅ Always — no DI involved | — |
| Real-DB integration tests with full Nest DI from sources in `apps/*/src/` | ❌ Without `@Inject`, all constructor-injected params arrive as `undefined` | Production runtime is fine — ts-node + swc both honor `emitDecoratorMetadata` |

**Defensive default** for any new NestJS controller: write the constructor as

```typescript
constructor(@Inject(SomeService) private readonly someService: SomeService) {}
```

Yes it's verbose. Yes production runtime doesn't strictly need it. But it's the difference between an integration test that works at first commit vs. one that silently fails with `Cannot read properties of undefined` and wastes a CI cycle to diagnose.

## The proper fleet-wide fix (not yet done)

Add `unplugin-swc` to the workspace's `apps/api/vitest.config.ts` so SWC (which honors `emitDecoratorMetadata`) compiles the source instead of esbuild:

```bash
pnpm --filter @ecosire/api add -D unplugin-swc
# @swc/core is usually already installed
```

```typescript
// apps/api/vitest.config.ts
import swc from 'unplugin-swc';
export default defineConfig({
  plugins: [swc.vite()],
  // ... rest
});
```

This fixes ALL controllers without needing per-class `@Inject`. Not done in D:/ECOSIRE.COM 2026-05-12 because explicit `@Inject` was more surgical and SWC integration could affect other tests in the suite. Worth doing as a separate cleanup task.

## Symptom signature to recognize

- Unit tests in `src/modules/foo/foo.service.spec.ts`: ✅ pass
- Mocked integration in `src/modules/__integration__/*.spec.ts`: ✅ pass
- Real-DB integration in `test/integration/*.spec.ts`: ❌ fail at controller line with `Cannot read properties of undefined (reading '<method-on-service>')`

If you see this exact pattern, it's the decorator-metadata gap. Add `@Inject(ServiceClass)` to the constructor param and move on.

## Source

Discovered 2026-05-12 during D:/ECOSIRE.COM Phase 1 marketplace CI. `OrdersController` failed real-DB integration tests with `TypeError: Cannot read properties of undefined (reading 'findBySession')` at `orders.controller.ts:30`. Root cause: `constructor(private readonly ordersService: OrdersService) {}` couldn't be wired by DI without metadata. Fix: explicit `@Inject(OrdersService)` (commit `d716645a`). All affected tests went green immediately.

Cross-link: `~/.claude/projects/D--ECOSIRE-COM/memory/feedback_vitest_decorator_metadata.md` (workspace-local detailed feedback memory).
