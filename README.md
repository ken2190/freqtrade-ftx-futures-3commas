# freqtrade-ftx-future-3commas
Enables FTX Futures trading using freqtrade on 3commas

## Do NOT use unless you understand what you're doing and you enjoy losing money. 

These modified files allow you to use FTX PERP futures pairs from within a freqtrade bot, passing the buy signal to 3commas to enable auto DCA'ing. This has been confirmed working with freqtrade 2021.11. There may be unintended and unantcipated consequences to these modified files. DCA can be risky, this risk is magnified with leveraged futures. If you do use this; only trade what you can afford to lose.

## Acknowledgements

This is based on the work of Al @ https://github.com/AlexBabescu/freqtrade_3commas

### Prerequisites
> Working freqtrade installation
> 
> A FTX account with enough USD/USDT in your wallet to trade futures. 

## Installation

### Enabling FTX Future within freqtrade

Copy `exchange.py` and `ftx.py` to the `./freqtrade/exchange/` directory; overwriting the existing files.

Copy `IPairList.py` to the `./freqtrade/plugins/pairlist/` directory; overwriting the existing file.


Edit your existing `config.json` file. Set your `"stake_currency"` to `USD`. We need to filter to only enable PERP futures pairlists and filter the other USD spot market pairs. To do this add `".*/USD"` to your pairs blacklist and `".*PERP/.*"` to your whitelist.  If you wish to avoid trading non perpetual futures include `".*-([0123456789])+",` in your blacklist (thanks to stash on the freqtrade discord). See the example config. Make sure you've set your exchange to `ftx` (don't need to have keys in the config, we'll be running freqtrade as a dry run.

### Setting up freqtrade to send trades to 3commas

Please follow the excellent documentation at https://github.com/AlexBabescu/freqtrade_3commas. Make sure you've created your bot as a multi-pair/long only bot, configured freqtrade within config.json and downloaded and installed the wrapper in the correct directory. 

Once you have `freqtrade3cw.py` in your `user_data` folder; we need to do some modifications to enable freqtrade to send the right buy command to 3commas due to the way FTX names its pairs

From within `freqtrade3cw.py` we need to change:
```
coin, currency = metadata['pair'].split('/')
```
to:
```
coin = metadata['pair']
```

We then also need to change:
```
"pair": f"{currency}_{coin}",
```
to:
```
"pair": f"USD_{coin}",
```

### Start trading.

Make sure you've imported `Freqtrade3cw` into your strategy and decorated the `populate_buy_trend` method (as described in the wrapper setup). Start freqtrade as you normally would (in a dry run) and keep an eye on 3commas as the signals come in. You can choose which specific futures pairs you want to trade from within freqtrade as you normally (Volume list etc) or can manually set a static pairlist from within `config.json` depending on your preference. 





