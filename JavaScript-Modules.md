# JavaScript Modules Cheatsheet

## Chart

| -                        | ES Modules                                             | Common JS Modules                                       |
| ------------------------ | ------------------------------------------------------- | ------------------------------------------------------- |
| default export           | `export default <something>`                            | `module.exports = <something>`                          |
| named export             | `export <something>`                                    | `module.exports = { <somename>: <something> }`          |
| default import           | `import <something> from "module.js"`                   | `const <something> = require("module")`                 |
| named import             | `import { <something > } from "module.js"`              | `const { <something> } = require("module");`            |
| renaming named imports   | `import { <something> as <somename> } from "module.js"` | `const { <something>:<somename> } = require("module");` |
| renaming default imports | `import <something> as <somename> from "module.js"`     | `const <somename> = require("module")`                  |

## Examples

### Common JS

#### exports (module.exports)

```js
module.exports = 12 // Exporting a single number
module.exports = function () {} // exporting a single function
module.exports = { // exporting an object
    key: value
}
```

### imports (require)

```js
const variable = require("module"); // Importing a single thing
const { key } = require("module"); // Importing a thing from the exported object using destructuring
```

### ES Modules

#### exports 

```js
export const num = 12; // Exporting a named number
export function foo() { } // Exporting a named function
export const obj = { key: value } // Exporting a named object
export default 12 // Exporting a default number
export default function () { } // Exporting a default function
export default { // Exporting a default object
    key: value
}
```

#### imports

```js
import variable from "module.js" // Importing a default export
import { num } from "module.js" // importing a named export
import variable { num } // Doing both at once.
import { num as number } from "module.js" // Renaming a named import (aliasing)
```
