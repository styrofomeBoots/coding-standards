# So Long, and Thanks for All the Bits: A Mostly Harmless Approach to Clean, Maintainable Code

## Introduction

> "Debugging code is twice as hard as writing the code in the first place. Therefore, if you write code as cleverly as possible, you are, by definition, not smart enough to debug it."  
> — **Brian W. Kernighan**

As a developer—as in most things—experience gained often leads to decisions scorned. With hindsight, I often find myself asking, "What were you thinking?" I know there are codebases where someone checks the git blame, sees my name, and wonders the same. I've gone full circle on what "good, clean code" is, more than once. From over-documenting to under-documenting, from cryptic variable names to overly verbose, the only constant in my code was a continual change.

Now, I see good code as akin to a well-written book. Some parts are thrilling, some are mundane, and some no one really wants to read. But a great book is written with the reader in mind, and I strive to write code that the next person can look at and quickly say, "Ah, I get it."

**My goal is to contribute to scalable, maintainable, and easily navigable codebases by establishing clear definitions, guidelines, and naming standards that can evolve to meet the needs of the team.**

While I primarily work in Vue 3 and TypeScript, these principles are transferable, and I’ve noted where specific context might be necessary.

Lastly, a quick disclaimer: I see these as guidelines, not strict rules. There will always be situations where flexibility is necessary, and certain approaches may be better suited to the context.

## Table of Contents

- [Vue 3 Specific](#vue-3-specific)
  - [Components](#components)
  - [Composables](#composables)
  - [Utilities](#utilities)
  - [Helpers](#helpers)
  - [Single Files or Directories](#single-files-or-directories)
  - [File Names](#file-names)
- [In General](#in-general)
  - [Variable Names](#variable-names)
  - [Function Names](#function-names)
  - [Avoid Clever Code](#avoid-clever-code)
  - [Avoid Deeply Nested Conditionals](#avoid-deeply-nested-conditionals)
  - [Documentation and Comments](#documentation-and-comments)
  - [Testing Guidelines](#testing-guidelines)
  - [Error Handling \& Logging](#error-handling--logging)

## Vue 3 Specific

### Components

Components are the building blocks of UI/UX and contain the necessary logic to make them functional within the client. Components should follow the script setup and composition API syntax to maintain consistency across the codebase.

When a component file grows too large—typically when its responsibilities become unclear or it exceeds 200-300 lines—functions that are not directly tied to the component’s core functionality should be moved to either a helper or composable file. Use helper files for logic specific to that component and composables for reusable, stateful logic that can be shared across components.

Component files should be named using PascalCase.  
Example: `ColorPicker.vue`

### Composables

Composables are reusable functions designed to encapsulate stateful logic, side effects, or interaction with lifecycle hooks. A function or group of functions qualifies as a composable if it meets one or more of the following criteria (derived from [this video](https://www.youtube.com/watch?v=N0QrFKBZuqA&t=544s&ab_channel=AlexanderLichter)):

1. The function uses other composables or is primarily used in a lifecycle hook.
2. The function incorporate a lifecycle hook within itself.
3. The function contain or utilize stateful logic.

By convention, composable function names should start with "use" as recommended in [the vue docs](https://vuejs.org/guide/reusability/composables). This naming convention improves discoverability and signals the purpose of the function.

Composable files should be placed in the same directory as the main file or service they support to maintain proximity to related logic. For more general-purpose composables—those that provide utilities across the entire application—place them in the `./src/composables/` directory to ensure reusability and accessibility.

### Utilities

Utilities are stateless, reusable functions designed to solve common, repetitive problems that can arise across components, composables, or features. They are typically pure functions, meaning they do not rely on external state and return consistent outputs for the same inputs. Examples of utilities include color math functions (e.g., converting RGB values to hex codes) and general-purpose calculations, such as formatting dates or manipulating arrays.

Utility files should follow a consistent naming convention, using camelCase with a `.utils.ts` suffix to differentiate them from other types of code.  
Example: `colorMath.utils.ts`

To keep the codebase organized, utility files should be placed in the `./src/utilities/` directory, where they can be easily accessed and shared across the application.

### Helpers

Helpers are collections of functions specifically designed to support a particular component, composable, feature, or similar entity. Their primary purpose is to abstract out more complex or lengthy logic from the main file, making it more concise, readable, and maintainable. Helpers are typically specific to the entity they assist and are not intended to be shared across the application in the way utilities are.

Helper files should follow a consistent naming convention, using the prefix of the main file they support and the `.helpers.ts` suffix to clearly indicate their purpose.  
Example: `ColorPicker.helpers.ts`

Helper files should be located in the same directory as the main file they assist to maintain close proximity to related logic. If a helper file contains application-wide logic, it should be placed in the `./src/helpers/` directory. In cases where a composable has a small number of helper functions, these can be defined directly in the composable file, placed above the exported composable function for convenience.

### Single Files or Directories

When a component, composable, feature, or similar entity is self-contained, it should be placed directly in the root directory of the relevant functionality. However, if the entity requires additional supporting files (e.g., helpers, styles, or tests), a dedicated directory should be created. This directory should use the entity’s prefix and appropriate casing, and the main file, along with its supporting files, should be moved into this new directory.

This approach keeps the codebase organized and ensures related files are grouped together, improving maintainability and clarity. However, this structure should be re-evaluated as the project grows to ensure it remains scalable and appropriate for the increasing complexity.

```tree
src/
├──components/
|  ├─-BaseHeader.vue
|  ├──ColorPicker/
|  |  ├──ColorPicker.vue
|  |  └──ColorPicker.helpers.vue
├──utilities/
|  ├─-dateTime.utils.ts
|  ├──colorMath/
|  |  ├──colorMath.utils.ts
|  |  └──colorMath.helpers.ts
```

### File Names

- **Component files** should be named using PascalCase and consist of at least two words to avoid overly generic names.  
  Example: `SearchBar.vue`
- **TypeScript files** should be named using camelCase to align with variable and function naming conventions.
  Example: `timeWarp.ts`
- **Static files** (e.g., text, JSON, or other assets) should use kebab-case to maintain readability and consistency across file types.
  Example: `state-abbreviations.json`
- **Stylesheets** (e.g., SCSS, CSS) should also use kebab-case.
  Example: `theme-colors.scss`
- **Test files** should reflect the functionality they are testing and use PascalCase or camelCase depending on the file type, with a `.spec.ts` suffix.
  Example: `SearchBar.spec.ts`
- **Configuration files** (e.g., `.eslintrc.js`, `.prettierrc.json`) should typically follow project or tool-specific conventions but generally use kebab-case or dot notation.
  Example: `.prettierrc.json`

## In General

### Variable Names

- **Use camelCase:** (TypeScript) Start with a lowercase letter and capitalize the first letter of each subsequent word.  
  Good: `totalPrice`, `userAge`  
  Bad: `TotalPrice`, `user_age`
- **Descriptive names:** Variable names should clearly describe their purpose or the data they hold. Avoid vague or abbreviated names.  
  Good: `userList`, `currentTemperature`  
  Bad: `ul`, `temp`
- **Avoid single-letter names:** Except for loop counters or very small, local-scope variables, avoid single-letter names.  
  Good: `index`, `itemCount`  
  Bad: `i`, `c`
- **Consistency is key:** Maintain consistency across the codebase for similar types of variables, especially for booleans, arrays, or objects.  
  Consistent: `isUserLoggedIn`, `hasUserPermission`  
  Inconsistent: `isUserLoggedIn`, `userHasPermission`
- **Boolean prefixes:** Prefix Boolean variables with "is", "has", "can", or "should" to indicate that they hold a boolean value. Avoid double negatives in names.  
  Good: `isAvailable`, `hasAccess`, `canDelete`, `shouldUpdate`  
  Bad: `available`, `access`, `delete`, `update`, `isNotAvailable`
- **Arrays in plural:** Indicate that a variable is an array by using plural names.  
  Good: `users`, `items`  
  Bad: `userList`, `itemArray`
- **Avoid abbreviations:** Use complete words to prevent confusion.  
  Good: `userInformation`, `maximumValue`  
  Bad: `usrInfo`, `maxVal`
- **Provide context:** If the variable’s purpose isn’t immediately obvious, include context in the name.  
  Good: `userAge`, `orderTotal`  
  Bad: `age`, `total`
- **Keep names concise:** Don’t include unnecessary words in variable names to keep them concise but meaningful.  
   Good: `price`  
   Bad: `priceValue`
- **Use uppercase for constants:** To distinguish constants from regular variables, use all uppercase with underscores for separation.  
  Good: `MAX_USERS`, `API_URL`  
  Bad: `maxUsers`, `apiUrl`
- **Acronyms:** If using acronyms, prefer camelCase for acronyms of two letters.  
  Good: `userId`, `HTTPResponse`  
  Bad: `userID`, `ApiKey`

### Function Names

- **Descriptive names:** Function names should clearly describe what the function does. Avoid vague or generic names that do not convey the function’s purpose.  
  Good:`calculateTotalPrice()`  
  Bad: `doCalculation()`
- **camelCase convention:** (TypeScript) Use camelCase for function names, starting with a lowercase letter and capitalizing the first letter of each subsequent word.  
  Good: `fetchUserData()`  
  Bad: `Fetch_User_Data()`
- **Use verbs:** Functions typically represent actions, so name them with verbs or verb phrases to indicate the action they perform.  
  Good: `sendEmail()`, `updateProfile()`  
  Bad: `emailSender()`, `profileUpdater()`
- **Consistency is key:** Maintain consistent naming conventions throughout the codebase to improve readability and maintainability.  
  Consistent: `getUserData()`, `setUserData()`  
  Inconsistent: `fetchUserData()`, `assignUserData()`
- **Avoid abbreviations:** Use full words in function names to avoid confusion. Abbreviations can lead to misunderstandings or reduce clarity.  
  Good: `generateReport()`  
  Bad: `genRpt()`
- **Boolean function prefixes:** For functions that return a boolean value, use prefixes like "is", "has", or "can" to make the return type obvious.  
  Good: `isValidUser()`, `hasAccess()`, `canEdit()`  
  Bad: `validUser()`, `access()`, `edit()`
- **Avoid single-letter names:** Single-letter names lack clarity and should be avoided, except for simple loop counters or extremely small functions. Meaningful names enhance readability, especially when debugging.  
  Good: `initializeApp()`  
  Bad: `a()`
- **Do one thing, and do it well:** A function should have a single responsibility. This leads naturally to more descriptive names.  
  Good: `validateEmail()`  
  Bad: `processUserInputAndSendEmail()`
- **Add context when needed:** If the function’s context isn’t clear from the name alone, include additional context in the name. This is particularly useful in larger codebases.  
  Good: `calculateCartTotal()`  
  Bad: `calculateTotal()`
- **Avoid redundant words:** Avoid including unnecessary words that don’t add meaningful information to the function name.  
  Good: `fetchData()`  
  Bad: `fetchDataFunction()`

### Avoid Clever Code

While it can be tempting to write code that demonstrates clever tricks or shortcuts, doing so can lead to confusion and make the code harder to maintain or debug. The goal should always be to write clear, readable, and understandable code, even if it seems more verbose or simple. Clear code is a mark of a skilled developer. Readability and maintainability should always come first.

- **Prioritize readability:** Code should be easily understood by others (or by yourself in the future). Avoid using obscure syntax, cryptic shortcuts, or overly complex solutions just to save a few lines of code.  
  Good: `const isValid = user.age >= 18;`  
  Bad: `const isValid = !!~(user.age - 18);`
- **Self-explanatory code:** Avoid code that requires extensive comments to explain what it does. The clearer the code itself, the fewer comments you’ll need. If the code seems tricky or too concise, break it up into smaller, more readable parts.
- **Long-term maintainability:** Consider how future developers will interact with the code. Clever code might work now, but it can create issues later when someone unfamiliar with your approach tries to make changes.
- **Explicit is better than implicit:** Write code that directly expresses its intent. Clever tricks can sometimes obscure the actual purpose of the code, leading to misunderstandings or mistakes.  
  Good: `if (isUserLoggedIn)`  
  Bad: `if (!!isUserLoggedIn)`
- **Resist over-optimization:** Focus on writing clear, simple code first. Avoid premature optimization that can make the code harder to read and maintain. Only optimize where it is necessary and after profiling performance.

### Avoid Deeply Nested Conditionals

Deeply nested conditionals can make code harder to read and maintain. To improve readability and clarity, it's often better to **return early** or handle the "negative" case first, so the main logic remains at the outermost level. Avoiding nesting can improve readability and maintain clarity.

- **Handle error or invalid states first:** Check for invalid or edge cases at the start of the function and return early, so the main logic is not buried under layers of conditionals.
- **Keep the happy path clear:** After handling the negative cases, the code that runs under normal conditions should be at the outermost level.
- **Reduce unnecessary else blocks:** If you've returned or exited a function early, you usually don't need an else block, keeping the code flat and readable.

**Before (nested conditionals):**

```typescript
function processUser(user: User) {
  if (user) {
    if (user.isActive) {
      if (user.hasPermission) {
        // Main logic here
      }
    }
  }
}
```

**After (return early):**

```typescript
function processUser(user: User) {
  if (!user) return;
  if (!user.isActive) return;
  if (!user.hasPermission) return;

  // Main logic here
}
```

### Documentation and Comments

Code should aim to be as self-documenting as possible through the use of clear naming conventions, logical structure, and readable patterns. Comments should be reserved for situations where additional context is necessary—explaining why certain decisions were made rather than how the code works, which should be evident through well-written code.

- **When to use comments:** Use comments sparingly and only when the logic or decision-making process is not immediately clear from the code itself. This includes explaining complex algorithms, providing context for non-obvious solutions, or documenting workarounds.
- **Self-documenting code:** Strive for variable and function names that make the code understandable without needing inline comments. Clear, concise, and descriptive names reduce the need for excessive comments.
- **Exported functions:** For functions that will be exported and used across different parts of the codebase, provide comments to enhance IntelliSense support, especially in TypeScript. This can also help other developers quickly understand the function’s purpose and parameters.
- **Use a standard format:** Whenever documenting functions, follow a consistent documentation standard such as JSDoc or TSDoc for structured and uniform comments. Example:
  ```typescript
  /**
   * Calculates the total price of the cart.
   *
   * @param {number[]} prices - An array of item prices.
   * @param {number} tax - The tax percentage to apply.
   * @returns {number} - The total price including tax.
   */
  function calculateTotalPrice(prices: number[], tax: number): number {
    // Implementation here
  }
  ```
- Commenting prompt: The following ChatGPT prompt can be used for generating TypeScript documentation:
  ```text
  Write TypeScript documentation for this function. Only provide the comment and do not include examples:
  ```

### Testing Guidelines

- **Test File Naming**: Test files should mirror the name of the file they are testing and be placed in a `__tests__` folder or at the same directory level.
  Example: `ColorPicker.spec.ts`
- **Unit Tests**: Aim to cover individual functions or components with concise, focused unit tests. Each test should validate a single behavior.
- **Integration Tests**: For more complex interactions, write integration tests that cover how multiple functions or components interact. Name these with a `.integration.spec.ts` suffix.
- **Use Descriptive Test Names**: Test names should clearly explain the behavior being tested, making it easy to understand what’s failing when a test breaks.
  Example: `it("should return the correct total price with tax applied")`
- **Mocking and Stubbing**: Use mocks and stubs judiciously to isolate the functionality being tested and avoid external dependencies.

### Error Handling & Logging

- **Error Messages**: Ensure that error messages are meaningful and provide actionable information to developers or users. Avoid vague messages like "Something went wrong."
- **Try-Catch Blocks**: Use try-catch blocks in areas where errors are expected and handle them gracefully. Provide fallback options when possible.
- **Logging**: Use logging for debugging and monitoring, ensuring that sensitive information is not logged in production environments. Structure logs consistently to facilitate easy searching and filtering.
- **Throw Specific Errors**: Where possible, throw specific error types to make error handling easier and more precise.
