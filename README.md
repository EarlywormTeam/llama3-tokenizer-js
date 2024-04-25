# 🦙 llama3-tokenizer-js 🦙

JavaScript tokenizer for LLaMA 3 which works client-side in the browser (and also in Node) (now also with TypeScript support).

Intended use case is calculating token count accurately on the client-side.

<a href="https://belladoreai.github.io/llama3-tokenizer-js/example-demo/build/">Click here for demo</a>

## Features

- Easy to use: 0 dependencies, code and data baked into a [single file](llama-tokenizer.js).
- Compatible with most LLaMA 3 models (see [Compatibility](#compatibility))
- Optimized running time (highly efficient BPE implementation)
- Somewhat optimized bundle size, though it's still ugly (data is written in a custom format where it takes up 3MB before minification and gzipping, which is ugly, yes, but still better than the original 9MB raw json data file)

## Import

Recommended way: Install as an npm package and import as ES6 module

```
npm install llama3-tokenizer-js
```

```
import llama3Tokenizer from 'llama3-tokenizer-js'

console.log(llama3Tokenizer.encode("Hello world!").length)
```

Alternative: for CommonJS projects this should work:

```
async function main() {
    const llama3Tokenizer = await import('llama3-tokenizer-js')
    console.log(llama3Tokenizer.default.encode("Hello world!").length)
}

main();
```

## Usage

Once you have the module imported, you can encode or decode with it. Training is not supported.

When used in browser, llama3-tokenizer-js pollutes global namespace with `llama3Tokenizer`.

Encode:

```
llama3Tokenizer.encode("Hello world!")
> [128000, 9906, 1917, 0, 128001]
```

Decode:

```
llama3Tokenizer.decode([128000, 9906, 1917, 0, 128001])
> '<|begin_of_text|>Hello world!<|end_of_text|>'
```

Note the special tokens in the beginning and end. These affect token count. You can pass an options object if you don't want to add these:

```
llama3Tokenizer.encode("Hello world!", { bos: false, eos: false })
> [9906, 1917, 0]
```

Note that, contrary to LLaMA 1 tokenizer, the LLaMA 3 tokenizer does not add a preceding space (please open an issue if there are circumstances in which a preceding space is still added).

## Tests

You can run tests with:

```
llama3Tokenizer.runTests()
```

Note that tests can be run both in browser and in Node (this is necessary because some parts of the code work differently in different environments).

## Compatibility

This tokenizer is compatible with all models which have been trained on top of checkpoints released by Facebook in April 2024 ("LLaMA 3").

What this means in practice:
- ✅ LLaMA 3 models released by Facebook: yes, they are compatible
- ✅ New LLaMA 3 based fine tune by somebody other than Facebook: yes, it's compatible
- ❌ New LLaMA 3 model trained from scratch by somebody other than Facebook: probably not compatible, depends if they also retrained the tokenizer
- ❌ LLaMA 1 or LLaMA 2 based models: no, not compatible (use [llama-tokenizer-js](https://github.com/belladoreai/llama-tokenizer-js) instead)
- ❌ OpenAI models: no, not compatible
- ❌ Mistral models: no, not compatible

If you are unsure about compatibility, try it and see if the token ids are the same (compared to running the model with, for example, the transformers library).

If you want to make this library work with different tokenizer data, you may be interested in [this script](data-conversion.py) which was used to convert the data.

You can pass custom vocab and merge data to the tokenizer by instantiating it like this:

```
import { Llama3Tokenizer } from 'llama3-tokenizer-js'
const tokenizer = new Llama3Tokenizer(custom_vocab, custom_merge_data);
```

Please note that if you try to adapt this library to work for a different tokenizer, there are many footguns and it's easy to set up something that almost works. If the only thing that needs to change is vocab and merge data, and they are of same size as the previous vocab and merge data, you should be fine. But if anything else in addition to vocab and merge data needs to change, you have to read and understand the full source code and make changes where needed.

## Repo maintenance

Release steps:

1. node test-llama-tokenizer.js
2. open test.html (with live-server or similar)
3. do you need to update this README?
4. bump version number in root package.json
5. push tokenizer changes to github
6. npm publish --dry-run
7. npm publish
8. bump version number in example-demo/package.json
9. cd example-demo && npm run build && live-server
10. push example demo changes to github

## Who did this

LLaMA3-tokenizer-js is a fork of my earlier LLaMA 1 tokenizer [llama-tokenizer-js](https://github.com/belladoreai/llama-tokenizer-js). Several helper functions used in LLaMA 3 pretokenization were adapted from the fantastic [transformers.js](https://github.com/xenova/transformers.js) library. The BPE implementation, which is the core of this library, is original work and [was adapted into transformers.js](https://github.com/belladoreai/llama-tokenizer-js/issues/9). In other words, some work has been adapted from llama-tokenizer-js into transformers.js, and some work has been adapted the other way, from transformers.js into llama3-tokenizer-js.

The example-demo (tokenizer playground) is a fork of [gpt-tokenizer playground](https://github.com/niieani/gpt-tokenizer).

Developed by [belladore.ai](https://belladore.ai) with contributions from [xenova](https://github.com/xenova), [blaze2004](https://github.com/blaze2004), [imoneoi](https://github.com/imoneoi) and [ConProgramming](https://github.com/ConProgramming).