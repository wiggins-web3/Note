

老的SubGraph

监听

```
PairCreated(indexed address,indexed address,address,uint256)  【Uniswap v2】
// event PairCreated(address indexed token0, address indexed token1, address pair, uint);
```

创建  TokenPair @entity

```
get or create token0、token1
--> loadOrCreateToken(token0 and token1)
--> 

type TokenPair @entity {
  id: ID!
  token0: Token!
  token1: Token!
  fee: BigInt
  exchange: DEX!
  pool: String!
  tvl0: BigInt!
  tvl1: BigInt!
  totalVolumeUSD: BigInt!
  totalVolumeNative: BigInt!
}

```









```
PoolCreated(indexed address,indexed address,indexed uint24,int24,address) 【Uniswap v3】
```

TVL

https://piperxdb.piperxprotocol.workers.dev/api/piperxapi/tvl

```
{
  "dex": {
    "id": "piperx",
    "name": "PiperX",
    "totalVolumeV2USD": "9234354580185",
    "totalVolumeV3USD": "220564409445845",
    "tvlUSD": "5934752128924",
    "tvlNative": "1616528639158269571984055",
    "totalVolumeUSD": "229798764026030"
  }
}
```

数据流：tvl -> subgraph