## A Simple Project to Implement Hexagonal Architecture and DDD Concepts

I've used the Result pattern in this repo, which is explained very well in [this article](https://khalilstemmler.com/articles/enterprise-typescript-nodejs/handling-errors-result-class/).

The implementation of the Result pattern depends on the person and their philosophy. Since I advocate minimalism, this is what I came up with:

```ts
type Result<V, E> = { failed: true; error: E } | { failed: false; value: V };
```

Here is an example of its usage:

```ts
async function sendOTP(data: { /* ... */ }): Promise<Result<string, Error>> {
  try {
    const response = await fetch(/* ... */);

    // Check for errors from the third party (400, 404, 5xx, ...)
    if (!response.ok) {
      const errorData = await response.json();
      return { failed: true, error: new Error(errorData.message) };
    }

    return { failed: false, value: "OTP sent." };
  } catch (err) {
    // Network issue (e.g. no connection, DNS failure, timeout)
    return {
      failed: true,
      error: err instanceof Error ? err : new Error(String(err)),
    };
  }
}
```
