---
sidebar_position: 7
---

# useTransactor

Use this hook to interact with the chain and get UI feedback on the transaction status. You can pass in anything that is a valid parameter to [Viem's `sendTransaction` function](https://viem.sh/docs/actions/wallet/sendTransaction#parameters).

```ts
const transactor = useTransactor();
const writeTx = transactor({
  to: "0x97843608a00e2bbc75ab0C1911387E002565DEDE",
  value: 1000000000000000000n,
});
await writeTx();
```

This example tries to send 1 ETH to the address `buidlguidl.eth`, prompting the connected [`WalletClient`](https://wagmi.sh/react/api/hooks/useWalletClient#usewalletclient) for a signature. And in the case of a successful transaction, it will show a popup in the UI with the message: "🎉 Transaction completed successfully!".

It's also possible to pass in a promise from [`writeContractAsync` from `useWriteContract`](/hooks/useScaffoldWriteContract) as the first argument. This way, you will also have access to data from the transaction, such as the current status and the result.
```ts
const { isPending, writeContractAsync: writeYourContractAsync } = useScaffoldWriteContract("YourContract");
const transactor = useTransactor();

...

<button
  className="btn btn-primary"
  disabled={isPending}
  onClick={async () => {
    try {
      const contractWritePromise = writeYourContractAsync({
        functionName: "setGreeting",
        args: ["The value to set"],
        value: parseEther("0.1"),
      });
      await transactor(contractWritePromise);
    } catch (e) {
      console.error("Error setting greeting:", e);
    }
  }}
>
  Set Greeting
</button>
```

## Configuration
### useTransactor
| Parameter            | Type        | Description                                                                                                         |
| :------------------- | :---------- | :------------------------------------------------------------------------------------------------------------------ |
| **_walletClient** (optional)     | [`WalletClient`](https://viem.sh/docs/clients/wallet.html)    | The wallet client that should sign the transaction. Defaults to the connected wallet client, and is only needed if the transaction is not already sent using `writeContractAsync` |

### callback function
| Parameter            | Type        | Description                                                                                                         |
| :------------------- | :---------- | :------------------------------------------------------------------------------------------------------------------ |
| **tx**     |  [`sendTransaction`-parameters](https://viem.sh/docs/actions/wallet/sendTransaction#parameters) or `Promise<Hash>`   | Either valid parameters for [`sendTransaction`-parameters](https://viem.sh/docs/actions/wallet/sendTransaction#parameters) or a promise that resolves with the transaction hash. |
| **options** (optional)  | `object` | Additional options for the confirmation. |
| **└─options.blockConfirmations** (optional)  | `number` | The number of block confirmations to wait for before resolving. Defaults to 1. |
| **└─options.onBlockConfirmation** (optional)  | `function` | A callback function that is called once all `blockConfirmations` is reached. |

## Return Values
### useTransactor
- The callback function that is used to inialize the UI feedback flow.

### callback function
- A promise that resolves with the transaction hash once the transaction is mined.

## UI Feedback
A small popup will appear in the UI with the following messages:

1. "Awaiting for user confirmation."
2. "Waiting for transaction to complete."
3. "Transaction completed successfully!"

![Happy Flow](/img/transactorHappyFlow.gif)

Any error will instead show a popup with the error message.

![Error Example](/img/transactorFail.gif)
