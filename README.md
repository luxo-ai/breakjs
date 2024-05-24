# Broken TS -> JS Code

Mircobundle generates broken commonJS code. In the following example, the continue keyword is ignored. This only happens when with an `async`/`await` statement in the loop.

Input (`index.ts`):

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

Output (`dist/index.cjs`)

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
