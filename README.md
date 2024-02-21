## Simple Python Telegram Bot With Redstone API

A step-by-step guide on how to use RedStone API for your Telegram BOT:

>[!WARNING]
>Before create project, make sure you already install [Python](https://www.python.org/)

Step 1: Setting Up the Project.

1. Create project folder. Then first file, name it `main.py`.
2. Import **requests** library. Use this command in terminal `pip install requests`
3. Import **telegram** library for configuration your bot. Use this command in terminal `pip install telegram`

Step 2: Bot settings.

1. Go to [BotFather](https://t.me/BotFather) and press button **Start** to create Bot.
2. At the BotFather, enter the /newbot command, and type name for your bot
   ![image](https://github.com/CryptoFenomen/SimpleTelegramBotWithRedstoneApi/assets/156483400/f6132ddc-9942-4591-84e3-78a4ba9e0c13)
   
4. Next step, choose username for your bot. And add prefix bot to the end
   ![image](https://github.com/CryptoFenomen/SimpleTelegramBotWithRedstoneApi/assets/156483400/a53647b0-9b4e-42c5-af20-51e0a1b1101c)
     
5. Congratulations! You have created your own bot! BotFather now provides your own bot token. Do not share it, keep this token in a safe place.
   ![image](https://github.com/CryptoFenomen/SimpleTelegramBotWithRedstoneApi/assets/156483400/cc6e5dcc-4ade-46ac-9f36-61bf34b98338)

7. Create your own command using **/setcommand**
   ![image](https://github.com/CryptoFenomen/SimpleTelegramBotWithRedstoneApi/assets/156483400/18c6ed18-2f93-486b-8c16-e64841f681e2)

9. For this example, you'll need to create a few commands, which I've noted here:
  ```
     help  - Provides help for RedStone bot
     price - Send actual price of choosen token
     pricelist - Send price from different exchange
     tokenlist - List of tokens supported RedStone API
   ```
Step 3: File setting.

>[!Warning]
>Make sure, you install all requirements from Step 1

1. Go main.py file and paste
```python
import requests
from typing import Final
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes

TOKEN: Final = 'YOUR_TOKEN'
BOT_USERNAME: Final = 'YOUR_BOT_USERNAME'
URL: Final = f'https://api.redstone.finance/prices?symbol='

# Commands
async def start_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text('Hi, Thanks for choosing me!')

async def help_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text('Use /tokenlist to know what RedStone APIs supporting tokens\nUse /pricelist [token] to find out the price on the exchange\nUse /price [token] to find actual price')

async def tokenlist_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    token_list = """
    <code>ETH, BTC, LINK, LTC, UNI, XRP, BCH, DOGE, EOS, TRX, 
    XLM, YFI, DOT, AAVE, COMP, ADA, ETC, MKR, FIL, 
    MATIC, BAT, DASH, SUSHI, USDC
    </code>
    """
    await update.message.reply_text(f'Supported Tokens:\n{token_list}\n'
                                    f'More supported token you can see here:\n'
                                    f'https://github.com/redstone-finance/redstone-api/blob/main/docs/ALL_SUPPORTED_TOKENS.md', parse_mode='HTML')

async def pricelist_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    try:
        token = context.args[0].upper()
        url = f'{URL}{token}&provider=redstone-rapid&limit=1'

        response = requests.get(url)
        if response.status_code == 200:
            data = response.json()

            await update.message.reply_text(f'Price for {token}: \n\n'
                                            f'Binance: {data[0]["source"]["binance"]}\n'
                                            f'Bitmart: {data[0]["source"]["bitmart"]}\n'
                                            f'Bitget: {data[0]["source"]["bitget"]}\n'
                                            f'Okx: {data[0]["source"]["okx"]}\n'
                                            f'Kucoin: {data[0]["source"]["kucoin"]}\n'
                                            f'Mexc: {data[0]["source"]["mexc"]}\n'
                                            f'Bingx: {data[0]["source"]["bingx"]}\n') 
        else:
            await update.message.reply_text(f'Error: {response.status_code} - {response.text}') 

    except IndexError:
        await update.message.reply_text('Please provide a valid token. List of supported tokens: /tokenlist')
    except Exception as e:
        await update.message.reply_text(f'An error occurred: {e}')
async def price_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    try:
        token = context.args[0].upper()
        url = f'{URL}{token}&provider=redstone-rapid&limit=1'

        response = requests.get(url)
        if response.status_code == 200:
            data = response.json()

            await update.message.reply_text(f'Price for {token}: {data[0]["value"]}') 
        else:
            await update.message.reply_text(f'Error: {response.status_code} - {response.text}') 

    except IndexError:
        await update.message.reply_text('Please provide a valid token. List of supported tokens: /tokenlist')
    except Exception as e:
        await update.message.reply_text(f'An error occurred: {e}')

# Responses
async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    message_type: str = update.message.chat.type
    text: str = update.message.text

    print(f'User({update.message.id}) in {message_type}: "{text}"')

    if message_type == 'group':
        if BOT_USERNAME in text:
            new_text: str = text.replace(BOT_USERNAME, '').strip()
        else:
            return

async def error(update: Update, context: ContextTypes.DEFAULT_TYPE):
    print(f'Update {update} caused error {context.error}')

if __name__ == '__main__':
    print('Starting bot..')
    app = Application.builder().token(TOKEN).build()

    # Commands
    app.add_handler(CommandHandler('start', start_command))
    app.add_handler(CommandHandler('help', help_command))
    app.add_handler(CommandHandler('tokenlist', tokenlist_command))
    app.add_handler(CommandHandler('pricelist', pricelist_command))
    app.add_handler(CommandHandler('price', price_command))
    # Messages
    app.add_handler(MessageHandler(filters.TEXT, handle_message))

    # Errors
    app.add_error_handler(error)

    print('Polling..')
    app.run_polling(poll_interval=3)

```

Step 4: Testing.
1. When you have finished setting up find your bot on telegram search bar and enter bot username what you used when create bot.
   ![image](https://github.com/CryptoFenomen/SimpleTelegramBotWithRedstoneApi/assets/156483400/147bddeb-53bd-4989-95cd-6ffc48686abd)

3. Type **/start** command
   ![image](https://github.com/CryptoFenomen/SimpleTelegramBotWithRedstoneApi/assets/156483400/d97cc06b-c88e-44a3-a765-a46d6d7d690c)

4. Enjoy!
  ![image](https://github.com/CryptoFenomen/SimpleTelegramBotWithRedstoneApi/assets/156483400/e70d0674-78c2-4226-8fd0-48028eeb2063)

>[!Warning]
>When you try create your own command, after coding don't forget create this command, in @BotFather.



