![image-20251228151612366](/Users/wiggins/Library/Application Support/typora-user-images/image-20251228151612366.png)

通过orderfilled 过滤出相应的market

```
SELECT 
    o.id AS order_id,
    o.transaction_hash,
    -- 逻辑判断：如果 taker_asset_id 不是 USDC，则它就是市场 Token
    CASE 
        WHEN o.taker_asset_id != '0' THEN o.taker_asset_id
        ELSE o.maker_asset_id 
    END AS market_token_id,
    m.condition,
    m.outcome_index,
    o.taker_amount_filled,
    o.timestamp
FROM order_filled_events o
LEFT JOIN market_data m ON (
    -- 尝试用两个资产 ID 去匹配市场表的 ID
    o.taker_asset_id = m.id OR o.maker_asset_id = m.id
)
WHERE m.id IS NOT NULL; -- 只选出匹配到市场数据的交易
```

通过condition_id查询相应的info

```
curl "https://gamma-api.polymarket.com/markets?condition_id=0x..." | jq '.[0].question'
0xb63fb6918f40795a6ea80bb233e438c416f836158bc651fbf854e90ae6aba3ea

curl "https://gamma-api.polymarket.com/markets?condition_id={4909838449214109982491245089051410988902525055666196}" | jq '.[0].question'


https://gamma-api.polymarket.com/markets?market_id=49098384492022932436093422259530513214109982491245089051410988902525055666196

```

0x81B51b6586b18319AE099C56F8eACF49E2d3A0E4

```
SELECT 
    datetime(timestamp, 'unixepoch', 'localtime') AS readable_time, *
FROM order_filled_events 
WHERE taker = '0x30862fadf22b566537c1cf4494ed705019ccad48' 
ORDER BY timestamp DESC 
LIMIT 1;
```





```
SELECT 
    o.id AS order_id,
    o.taker AS whale_address,          -- 成交的巨鲸地址
    o.taker_amount_filled AS amount,    -- 成交数量
    m.question AS market_name,          -- 市场问题
    
    -- 核心逻辑：提取匹配到的结果名称 (Yes/No)
    -- j.key 就是匹配到的索引，比如 0 或 1
    json_extract(m.outcomes, '$[' || j.key || ']') AS trade_outcome,
    
    -- 提取成交时的市场价格
    json_extract(m.outcome_prices, '$[' || j.key || ']') AS market_price,
    
    m.image_url,
    o.timestamp
FROM 
    order_filled_events o
JOIN 
    markets m
-- 关键变动：通过 json_each 将数组拆解并关联
JOIN 
    json_each(m.clob_token_ids) j ON j.value = o.taker_asset_id
ORDER BY 
    o.timestamp DESC;
```









```
curl -X POST http://localhost:8787/webhook/orders \
  -H "Content-Type: application/json" \
  -d '[{
    "id": "0xd159518e2c0f9f5338dab6a885dee1492bc8d4d13af27ace86552074268f59cb_0xa29503716e8ee5487a94bfe6a3988128946f8d4bba9496c5c717673749747ca0",
    "transaction_hash": "0VlRjiwPn1M42raohd7hSSvI1NE68nrOhlUgdCaPWcs=",
    "timestamp": 1766934667,
    "order_hash": "opUDcW6O5Uh6lL/mo5iBKJRvjUu6lJbFxxdnN0l0fKA=",
    "maker": "0xcdb1f1ef8213c06911e4ba6a884176c5571e276c",
    "taker": "0x30862fadf22b566537c1cf4494ed705019ccad48",
    "maker_asset_id": "44528029102356085806317866371026691780796471200782980570839327755136990994869",
    "taker_asset_id": "0",
    "maker_amount_filled": "1003008",
    "taker_amount_filled": "999998",
    "fee": "0"
  }]'
```





polygon地址

0x30862fadf22b566537c1cf4494ed705019ccad48



whale address 0xe00740bce98a594e26861838885ab310ec3b548c


恢复cloudflare数据库到本地

npx wrangler d1 export <数据库名称或ID> --remote --output=./backup.sql

~~npx wrangler d1 export  5dddcf47-ee73-4686-b789-b537dd5b5cb7 --remote --output=./backup.sql~~

npx wrangler d1 export  sonar-db --remote --output=./backup.sql

npx wrangler d1 execute sonar-db --local --file=backup.sql --config wrangler.toml

```
curl -X POST http://localhost:8787/webhook/orders \
     -H "Content-Type: application/json" \
     -d "[]"
```



npx  ts-node templatify.ts matic

yarn orderbook:codegen

yarn orderbook:build

yarn orderbook:deploy-goldsky