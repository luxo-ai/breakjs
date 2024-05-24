# Generate Broken CommonJS Code

[Microbundle](https://github.com/developit/microbundle) generates broken CommonJS code when compiling from Typescript. The `continue` keyword compiled JS is not respected. The problem is caused by the usage of `async/await` within the while loop. Commenting out: `await new Promise((resolve) => setTimeout(resolve, 100));` produces the expected result. 

**Input** - `index.ts`

```ts
async function brokenContinue() {
  let j = 0;

  while (j <= 3) {
    j++;
    console.log("AT THE TOP");

    if (j === 1) {
      console.log("\t- j === 1");
      console.log("\t- waiting for 100ms");
      await new Promise((resolve) => setTimeout(resolve, 100));
      console.log('\t- continue, next log will be "AT THE TOP"');
      continue;
    }

    console.log("AT THE BOTTOM");
  }
}

void brokenContinue();
```

**Output** - `dist/index.cjs`

```
$ node dist/index.cjs
AT THE TOP
	- j === 1
	- waiting for 100ms
	- continue, next log will be "AT THE TOP"
AT THE BOTTOM   <--------- This should be "AT THE TOP"
AT THE TOP
AT THE BOTTOM
AT THE TOP
AT THE BOTTOM
AT THE TOP
AT THE BOTTOM
```

**Notes:** 
- Compiling using `tsc` generates JS that works correctly. 
- Compiling to modern JS (with microbundle) generates code that works correctly. 
