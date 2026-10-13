# Trámites Digitales

Sistema de gestión de trámites. Cada trámite avanza por estados:

`iniciado` → `en_revision` → `aprobado` | `rechazado`

Cada cambio de estado se registra en un historial (quién, cuándo, de qué estado a cuál). El historial es append-only: nunca se edita ni se borra una entrada, los errores se corrigen con un cambio nuevo.

> Estado actual: `src/lib/server/db/schema.ts` todavía tiene el scaffold (tabla `task`). El dominio de arriba es el objetivo, no lo que ya está implementado.

## Stack

- SvelteKit 3 + Svelte 5 + TypeScript + Vite
- Drizzle ORM sobre PostgreSQL (`postgres-js`)
- Vitest (unit, browser mode) + Playwright (e2e)

**Svelte 5 con runes, obligatorio.** Usá `$state`, `$derived`, `$effect`, `$props`, `{@render}` y snippets. Prohibida la sintaxis de Svelte 4: `export let`, `$:`, `on:click`, `<slot>`, `createEventDispatcher`, stores para estado local de componente.

Los imports internos usan `#lib` (definido en `package.json`), no `$lib`.

## Comandos

| Comando               | Para qué                         |
| --------------------- | -------------------------------- |
| `npm run dev`         | Servidor de desarrollo           |
| `npm run check`       | Typecheck (svelte-check)         |
| `npm run lint`        | Prettier + ESLint en modo check  |
| `npm run format`      | Formatea con Prettier            |
| `npm run test:unit`   | Tests unitarios (Vitest, watch)  |
| `npm run test`        | Unit (una pasada) + e2e          |
| `npm run test:e2e`    | Playwright                       |
| `npm run db:start`    | Postgres local (docker compose)  |
| `npm run db:generate` | Genera la migración desde schema |
| `npm run db:migrate`  | Aplica las migraciones           |
| `npm run db:studio`   | Drizzle Studio                   |

## Reglas

- **Base de datos solo en el servidor.** Queries únicamente en archivos `.server.ts` o bajo `src/lib/server/`. Reusá el `db` de `src/lib/server/db/index.ts`, no crees otro cliente.
- **Schema.** Todo cambio en `schema.ts` va con `npm run db:generate` + `npm run db:migrate`, y la migración entra en el mismo PR que el cambio. **`npm run db:push` está prohibido.**
- **`.env` no se lee ni se modifica.** Si falta una variable, pedila.
- **Componentes Svelte.** Antes de escribir uno, consultá el MCP de Svelte. Después pasá el código por `svelte-autofixer` y corregí lo que marque.
- **Commits.** Conventional commits en español (`feat:`, `fix:`, `chore:`, `docs:`). Nunca commitear sin aprobación explícita.

## Workflow

1. Branch desde `develop`: `tipo/dg-XX-descripcion` (ej. `feat/dg-12-historial-estados`).
2. Implementar con tests.
3. `npm run check` y `npm run lint` en verde antes de pedir aprobación.
4. PR contra `develop`, referenciando el issue de Linear.
