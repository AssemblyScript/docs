---
description: How to hook into the compilation process with compiler transforms.
---

# Transforms

AssemblyScript is compiled statically, so code transformation cannot be done at runtime but must instead be performed at compile-time. To enable this, the compiler frontend \(asc\) provides a mechanism to hook into the compilation process before, while and after the module is being compiled.

Specifying `--transform ./myTransform` on the command line will load the node module pointed to by `./myTransform`.

```javascript
const { Transform } = require("assemblyscript/cli/transform");
const assemblyscript = require("assemblyscript");
class MyTransform extends Transform {
  ...
}
module.exports = MyTransform;
```

A transform is an ES6 class/node module with the following inherited properties:

* **program**: `Program` _readonly_ Program reference.
* **baseDir**: `string` _readonly_ Base directory.
* **stdout**: `OutputStream` _readonly_ Output stream used by the compiler.
* **stderr**: `OutputStream` _readonly_ Error stream uses by the compiler.
* **log:** `typeof console.log` _readonly_ Logs a message to console.
* **writeFile**\(filename: `string`, contents: `string | Uint8Array`, baseDir: `string`\): `boolean` Writes a file to disk.
* **readFile**\(filename: `string`, baseDir: `string`\): `string | null` Reads a file from disk.
* **listFiles**\(dirname: `string`, baseDir: `string`\): `string[] | null` Lists all files in a directory.

The frontend will call several hooks, if present on the transform, during the compilation process:

* **afterParse**\(parser: `Parser`\): `void` Called when parsing is complete, before a program is initialized from the AST. Note that types are not yet known at this stage and there is no easy way to obtain them.
* **afterInitialize**\(program: `Program`\): `void` Called once the program is initialized, before it is being compiled. Types are known at this stage, respectively can be resolved where necessary.
* **afterCompile**\(module: `Module`\): `void` Called with the resulting module before it is being emitted. Useful to modify the IR before writing any output, for example to replace imports with actual functionality or to add custom sections.

Transforms are a very powerful feature, but may require profound knowledge of the compiler to utilize them to their full extend, so reading through the compiler sources is a plus.

