# Viem vs Ethers

This repository contains 2 next apps that function the same, but one uses Viem and one uses ethers.

### Viem

Viem is a library that allows you to use the same syntax for both server and client side code. It is a wrapper around ethers, and it is a work in progress.

### Code Level Difference

In both of our NextJS apps, we have a page that displays the balance of an address. The only difference is the way we get the balance.

To get the balance of an ERC-20 with Viem, we use the following code:

```js
import { createPublicClient, http } from "viem";
import { goerli } from "viem/chains";

const client = createPublicClient({
  chain: goerli,
  transport: http(
    `https://eth-goerli.g.alchemy.com/v2/${process.env.NEXT_PUBLIC_ALCHEMY}`
  ),
});

...
const [loading, setLoading] = useState(true);
const [balance, setBalance] = useState<BigInt>();
...

// useEffect() here but could be a standard function
useEffect(() => {
  const getBalance = async () => {
    const data = await client.readContract({
      address: process.env.NEXT_PUBLIC_CONTRACT_ADDRESS, // erc20Contract
      abi,
      functionName: "balanceOf",
      args: ["0xB1aaA2ba9beaBC47178EE8F24eDd54Aa0d8A0ad6"], // my dev wallet
    });

    setBalance(data);
    setLoading(false);
  };
  if (!balance) {
    getBalance();
  }
}, [balance]);
...
```

compared to ethers:

```js
import { ethers } from "ethers";
...
const [loading, setLoading] = useState(true);
const [balance, setBalance] = useState<BigNumber>();
...
useEffect(() => {
  const getBalance = async () => {
    const provider = new ethers.providers.JsonRpcProvider(
      `https://eth-goerli.g.alchemy.com/v2/${process.env.NEXT_PUBLIC_ALCHEMY}`
    );
    const erc20Contract = new ethers.Contract(
      process.env.NEXT_PUBLIC_CONTRACT_ADDRESS,
      abi,
      provider
    );
    const data = await erc20Contract.balanceOf(
      "0xB1aaA2ba9beaBC47178EE8F24eDd54Aa0d8A0ad6" // my dev wallet
    );

    console.log(data);
    setBalance(data);
    setLoading(false);
  };

  if (!balance) {
    getBalance();
  }
}, [balance]);
...
```

## Compare the pair

Ethers (left) and Viem (right)

![Screenshot of Build Sizes and Time](https://user-images.githubusercontent.com/99215416/228748353-965d6375-ecb1-461d-9a42-7ee40c9024d8.png)



Viem has a first load of 109kB, where ethers has a first load of 170kB - a decrease of 61kB.

### You can read more about Viem here:

https://viem.sh/docs/
