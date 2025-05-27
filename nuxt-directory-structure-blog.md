Understanding the Directory structure in Nuxt 3
Charles Allotey
Charles Allotey
Updated: December 4th 2024
The file structure of a project plays a crucial role in maintaining code organization, simplifying debugging, and ensuring scalability. In this article, we'll explore the Nuxt 3 directory structure and best practices for structuring your next project. Understanding the conventions for organizing files in Nuxt 3 will help streamline your development process and set a solid foundation for building efficient and maintainable applications.

Why does the Nuxt 3 Directory Structure matter?
Unfortunately, in our Vue.js applications, folder structuring can be a huge mess. Messy apps usually start out having a poor folder structure. But one thing I am always proud of with Nuxt 3 is that its directory structure is carefully planned out and implemented.

Let us take a look at some of its benefits;

Saves team members time finding the items they need, sometimes at short notice
Reduces the risk of people using the wrong file
Contributes to an overall good Nuxt project
Encourages team to think about where they place their files rather than using the folder structure as a digital dumping ground
Makes your overall project easier to manage and more sustainable
Supports auto-imports to help devs ship their codes faster.
Let’s jump right into the directory structure in Nuxt 3.

.nuxt
This folder contains everything needed to generate your vue application and is required that you do not make changes to any file in this directory.

.output
.output folder holds all build files when building your Nuxt application to production (nuxt build). It is also required that you do not touch files in this directory when deploying your application to production.

assets
The assets folder holds all website’s assets the build tools will process. Usually your stylesheets, fonts and images that are not going to be served by the server can be placed in this folder.

components
Components is one folder we are all familiar with. It holds all our Vue components to be reused in our pages or other components. Nuxt 3 auto imports all our components created in this folder so we don’t have to manually import them before using them.

//~/components/Header.vue

<template>
    <div>
        <h1>This is my header Component</h1>
    </div>
</template>
//~app.vue

<template>
    <div>
        <Header />
    </div>
</template>
We can further nest components in sub directories and Nuxt 3 is still able to auto import our nested component by adding the subfolder name to our component name.

~Components
-|Button
---|Primary.vue
//~app.vue

<template>
    <div>
        <ButtonHeader />
    </div>
</template>
Composables
Composables are functions that leverage Vue.js composition API to create reusable stateful logic. Composables help in preventing repetition in writing functions that apply in multiple components. Just like components, Nuxt 3 auto imports composables created in this folder.

//~composables/useHello.js

export const useHello = () => {
return useState('hello', () => 'Hello world')
}
Now we can call our composables in our .js, .ts or .vue files with having to import them manually.

//~app.vue

<template>
    <div>
        <p>{{ hello }}</p>
    </div>
</template>

<script>
const hello = useHello()
</script>
For performance, Nuxt by default auto imports composables created in the top level of the composable directory.

~composables
-|useHello.js (auto imported)
-|usecount.js (auto imported)
-|Validation
---|useEmailvalidation.js (not auto imported)
You can however configure Nuxt to auto import composables in subdirectories .

export default defineNuxtConfig({
imports: {
dirs: [
// Scan top-level modules
'composables',
// ... or scan modules nested one level deep with a specific name and file extension
'composables/*/index.{ts,js,mjs,mts}',
// ... or scan all modules within given directory
'composables/**'
]
}
})
Content
The content folder usually goes with the Nuxt Content Module which creates a file based CMS using your .md, .json, .yaml and .csv files.

To install Nuxt content

//yarn
yarn add --dev @nuxt/content

//npm
npm install --save-dev @nuxt/content

//pnpm
pnpm add -D @nuxt/content
Add Nuxt Content to your build modules

//~nuxt.config.ts

export default defineNuxtConfig({  
modules: [
'@nuxt/content'
],
content: {
// https://content.nuxtjs.org/api/configuration  
}})
Now Nuxt content is ready to be used in your Nuxt application. You can now create your markdown files in your content folder and import them as components in your nuxt app.

Layouts
Layouts, as the name implies, provides a structure for your Vue.js pages. As your app complexity increases you may want to define different layouts for different pages or components. The layout folder holds your layout files and are automatically loaded via asynchronous import. Layouts are used by adding to your app.vue, and either setting a layout property as part of your page metadata.

Setting a Default Layout
//~layouts/default.vue

<template>
  <div>
    Some default structure shared across all pages
    <slot />
  </div>
</template>
It is important that your layouts have a single root element to allow Nuxt to apply transitions between layout changes - and this root element cannot be a ``

Now lets use our default layout

//~app.vue

<template>
  <NuxtLayout>
    some page content
  </NuxtLayout>
</template>
Adding another layout
//~layouts/another.vue
<template>
  <div>
    A different structure for specific pages
    <slot />
  </div>
</template>
Next, let’s use our new layout. This can be achieved using 2 different methods

<template>
  <NuxtLayout :name="layout">
    <NuxtPage />
  </NuxtLayout>
</template>

<script setup>
const layout = "custom";
</script>

or

<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>

<script>
// This will work in both `<script setup>` and `<script>`
definePageMeta({
  layout: "custom",
});
</script>
Middleware
With Nuxt 3, you can include a customizable route middleware framework throughout your application, allowing you to extract code you want to run before traversing a particular route. This middleware only runs on the client side which is the Vue.js part of your Nuxt application .

There are 3 types of route middleware;

Anonymous route middleware, which are defined directly in the pages where they are used.
Named route middleware, which are placed in the middleware/ directory and will be automatically loaded via asynchronous import when used on a page.
Global route middleware, which are placed in the middleware/ directory (with a .global suffix) and will be automatically run on every route change.
Now lets create an example middleware

~middleware/check.js

export default defineNuxtRouteMiddleware((to, from) => {
if (to.params.id === 'hello')
{
return abortNavigation()
}
return navigateTo('/')
})
We can now use our example middleware in our page

<script setup>
definePageMeta({
  middleware: ["check"]
  // or middleware: 'auth'
})
</script>
Node modules
This folder contains all dependencies needed to run our nuxt application. This folder is usually managed by your package manager (npm, yarn or pnpm)

Pages
Pages is where all our routes are defined. This is one exciting feature in Nuxt 3 that i am always pumped about. Vue router has been installed under-the-hood so creating routes for our nuxt application has become so simple. Vue-router is optional as to activate it you will need to create your pages directory.

First let’s setup our Nuxt app to include page routing

//~app.vue

<template>
  <div>
    <NuxtPage />
  </div>
</template>
Now to create a route simply create the .vue file in the pages directory with the route name or create a subfolder with the route name and create an index.vue file in it. Simple as that, your new route is created

~pages
--|index.vue (/)
--|about.vue (/about)
--|product
----|index.vue (/product)
Plugins
Plugins *are self-contained code that usually add app-level functionality to your nuxt app.* Nuxt automatically reads the files in your pluginsdirectory and loads them at the creation of the Vue application. All plugins in your plugins/ directory are auto-registered, so you should not add them to your nuxt.config separately. Just like composables plugins on the top level of the plugins directory can be registered and also index files in subdirectories .

plugins
| - myPlugin.ts (registered)
| - myOtherPlugin
| --- supportingFile.ts (not registered)
| --- componentToRegister.vue (not registered)
| --- index.ts (registerd)
Public
This was previously known as the static folder in Nuxt 2 but in Nuxt 3 the public/ directory is directly served at the server root and contains public files that have to keep their names (e.g. robots.txt) or likely won't change (e.g. favicon.ico).

Server
The server folder contains all files needed on the server side of your Nuxt application. This includes apis , server middleware, server routes, server plugins and server utilities.

Understanding the Nuxt 4 Directory Structure
With Nuxt 4, the directory structure is getting a major update to improve flexibility and scalability. Key changes include the introduction of the app directory, which now handles layouts, pages, and middleware, offering a more modular approach. For developers looking to upgrade, understanding these changes is crucial. You can explore the full details on What’s New in Nuxt 4, check out An Overview of Changes in Nuxt 4 for a summary of updates, and follow How to Upgrade to Nuxt 4 for a complete upgrade guide.

Conclusion
Hope you enjoyed reading this article on Understanding the Directory structure in Nuxt 3. I have been able to provide you with fundamental knowledge to the purpose of these directories to help you on your Nuxt journey. Our Mastering Nuxt course will dive deep down into explaining and using these concepts and directories.

