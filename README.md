# React gRPC Query Hook

if you are in love React Query and you're dealing with gRPC in you're application, `react-grpc-query` is brought to you to use a simple interface to deal with streaming.

> we use `protobuf-ts` package in the examples v2 and if you use another tools it's up to you.
> https://github.com/timostamm/protobuf-ts

## Project Status

Ready to use in your React.js web app

## Installation and Setup Instructions

`npm i @apingtech/react-grpc-query`

or if you use `yarn`

`yard add @apingtech/react-grpc-query`

## Example:

react-grpc-query uses a global stream handler so you have only one open streams per each key.

At first create a hook for you're stream, and use useStream hook inside.
Three parameters should be specified.

-   the first one the `key` which is a `string`
-   the second one is `stream Function` which is a the callback function which should connect to your stream transport. (more on later)
-   the third parameter is called `options`

### Stream Hook Example

```ts
function useExampleStream() {
    const [data, setData] = useState([]);

    useStream('stream-name', function streamFunction() {}, {
        onSuccess(result) {
            setData(result);
        },
    });

    return {
        data,
        isLoading: !data,
    };
}
```

### Stream Hook Example with Config

you're hook can also accept some config in this scenario you can keep the key as string by converting an object to `json` format.

```ts
function useExampleStream(config = {}) {
    const [data, setData] = useState([]);

    useStream(
        JSON.stringify({
            name: 'stream-name',
            id: config.id,
        }),
        streamFunctionWithConfig(config),
        {
            onSuccess(result) {
                setData(result);
            },
        }
    );

    return {
        data,
        isLoading: !data,
    };
}
```

### what streamFunction actually is?

streamFunction is just a pure function like this:
the cancel property lets the `react-grpc-query` to stop the streaming after unmounting the component or if the key is changed.

> the examples are in typescript

```tsx
function streamFunction() {
    const abortController = new AbortController();

    const call = new ExampleSubscriberClient(transport).subscribe({
        id: 1,
        name: 'Lorem',
    });

    return {
        call,
        cancel: () => abortController.abort()
    };
}
```

### What about onSuccess?

The onSuccess will fire on every stream event fire.
it is recommended to store onSuccess in a useCallback.

The complete example is the [example/useExampleStream.ts](example/useExampleStream.ts)

 folder.
