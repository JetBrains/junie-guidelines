# Nuxt.js Guidelines

You are an expert in JavaScript, TypeScript, Vue.js, Nuxt.js, and scalable web application development. You write secure, maintainable, and performant code following Nuxt and JavaScript best practices.

## JavaScript Best Practices
- Follow ESLint and Prettier configurations
- Use ES6+ features (arrow functions, destructuring, etc.)
- Prefer const over let, avoid var
- Use async/await for asynchronous operations
- Use template literals for string concatenation

## Nuxt Best Practices
- Use Composition API with `<script setup>` for components
- Leverage auto-imports for Vue and Nuxt composables
- Use Nuxt modules instead of manual configurations
- Implement proper error handling with error.vue

## Directory Structure
- Follow Nuxt 3 standard directory structure for better organization and auto-imports
- Keep generated directories (.nuxt, .output, node_modules) untouched
- Store stylesheets, fonts, and images in the assets directory
- Place Vue components in the components directory, organized by feature
- Store reusable logic in the composables directory with "use" prefix
- Use the layouts directory for page templates and structural components
- Place route guards in the middleware directory with appropriate naming
- Define routes in the pages directory following the file-based routing convention
- Store app-level functionality in the plugins directory
- Place static files in the public directory (formerly static in Nuxt 2)
- Use the server directory for server-side code (API routes, middleware)

## Pages and Routing
- Use dynamic routes appropriately
- Implement nested routes when logical
- Use middleware for route guards
- Leverage route validation with definePageMeta
- Use route parameters for dynamic content

## Components
- Create reusable components in the components directory
- Use TypeScript for props
- Use defineModel instead of a manual implementation of custom v-model
- Use script setup (with TS by default)
- Use props destructuring instead of withDefaults
- Implement proper component naming (PascalCase)
- Use slots for flexible component content
- Organize components in subdirectories by feature

## Composables
- Place reusable logic in composables directory
- Follow the "use" prefix naming convention
- Keep composables focused on a single responsibility
- Properly type composables with TypeScript
- Use built-in composables when available

## State Management
- prefer useState when possible
- use Pinia for more complex state management
- Avoid global state when component or page-level state is sufficient
- Do not use ref for global state
- Structure stores by domain/feature
- Implement proper typing for state

## API Calls
- Use useFetch or useAsyncData only for reactive data fetching
- Composables should only be used in `setup` or in another composable - not `onMounted` or in a function triggered later on
- Implement proper error handling for API calls
- Use $fetch for direct API calls (or when no reactivity is necessary/intended)
- Create composables for complex operations
- Leverage server routes for sensitive operations

## TypeScript
- Use TypeScript for better type safety
- Define interfaces and types for data structures
- Use generics when appropriate
- Leverage auto-imports for types
- Avoid using "any" type
- write erasableSyntaxOnly compliant code only (no enums, namespaces, and class parameter properties)

## Performance
- Implement proper code-splitting
- Use lazy loading for components when appropriate
- Optimize images with Nuxt Image
- Implement proper caching strategies
- Use server components for data-heavy operations

## SEO
- Use definePageMeta for page-level metadata
- Implement proper head management with useHead
- Use semantic HTML elements
- Ensure accessibility compliance

## Testing
- Write unit tests for components and composables
- Implement end-to-end tests for critical user flows
- Test both positive and negative scenarios
