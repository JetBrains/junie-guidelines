# Nuxt.js Guidelines

You are an expert in JavaScript, TypeScript, Vue.js, Nuxt.js, and scalable web application development. You write secure, maintainable, and performant code following Nuxt.js and JavaScript best practices.

## JavaScript Best Practices
- Follow ESLint and Prettier configurations
- Use ES6+ features (arrow functions, destructuring, etc.)
- Prefer const over let, avoid var
- Use async/await for asynchronous operations
- Use template literals for string concatenation

**Explanation:**
- ESLint and Prettier help maintain consistent code style
- Modern JavaScript features improve code readability and maintainability

## Nuxt.js Best Practices
- Use Composition API with `<script setup>` for components
- Leverage auto-imports for Vue and Nuxt composables
- Use Nuxt modules instead of manual configurations
- Implement proper error handling with error.vue

**Explanation:**
- Nuxt 3 has a different architecture than Nuxt 2, focusing on Vite, Vue 3, and TypeScript
- The Composition API provides better type safety and code organization

## Directory Structure
- Follow Nuxt.js 3 standard directory structure for better organization and auto-imports
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

**Explanation:**
- Nuxt's directory structure is designed for auto-imports and convention over configuration
- Proper organization improves maintainability and development speed

## Pages and Routing
- Use dynamic routes appropriately
- Implement nested routes when logical
- Use middleware for route guards
- Leverage route validation with definePageMeta
- Use route parameters for dynamic content

## Components
- Create reusable components in the components directory
- Use props validation
- Implement proper component naming (PascalCase)
- Use slots for flexible component content
- Organize components in subdirectories by feature

**Explanation:**
- Components in Nuxt 3 are auto-imported, so proper organization is crucial

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
- Structure stores by domain/feature
- Implement proper typing for state

**Explanation:**
- Nuxt 3 provides built-in state management with useState
- Pinia is the recommended store for more complex applications

## API Calls
- Use useFetch or useAsyncData only for reactive data fetching
- Implement proper error handling for API calls
- Use $fetch for direct API calls (or when no reactivity is necessary/intended)
- Create API services for complex operations
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
