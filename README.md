# GSX CHECK V4

GSX CHECK V4 is based on the uploaded Clean V3 source. It preserves the existing Telegram + Netlify structure and adds a configurable Netflix gift-card/subscription-code order flow, clearer MoMo sender-name entry, redemption instructions, and improved payment/delivery handling.

## Important status notes

- **MoMo name is payment evidence, not automatic verification.** After the customer taps “I've Paid”, the bot asks for the sender/account name. The customer should type normal text such as `Kofi Ama` (without `/`). The name is sent to the admin for manual comparison with the actual transaction.
- **Netflix supplier API is not connected yet.** Products are configured with `NETFLIX_PRODUCTS_JSON`. Payment confirmation and code delivery remain manual until you obtain a legitimate supplier API and we implement and test that supplier's documented API. Do not add an API key until there is a real provider and documentation.
- Netflix redemption help is included with delivery and links to Netflix's official redemption page: https://www.netflix.com/redeem
- Device/IMEI, serial and website reports remain `NOT VERIFIED` / `UNKNOWN` unless an authorized data provider is configured. This bot does not invent live results.
- MoMo and crypto payments are manually verified. A customer's “I've Paid” tap is not proof that money arrived.
- Virtual numbers must be used only for lawful purposes and with services that permit them.

## Customer buttons

- **Device Check** — choose Apple/Samsung/universal IMEI or Apple serial workflow.
- **Website Check** — choose a website category and submit a URL; reports remain unverified until a provider is integrated.
- **Digital Store** — Apple gift cards, Netflix gift cards/subscription codes, and virtual numbers.
- **Buy Checks** — purchase 1 check for GH₵5 or 3 checks for GH₵10.
- **My Account** — see check credits and completed-check count.
- **Help** — basic ordering instructions.

## Customer commands and inputs

| Command / input | What it does | When to use it |
|---|---|---|
| `/start` | Opens the main menu. | Anytime. |
| `/help` | Shows the help menu. | Anytime. |
| `/cancel` | Clears the current text-input prompt. | If the bot is waiting for a name, country, URL, IMEI or serial. |
| `/imei 352851114023666` | Submits a 15-digit IMEI using a Luhn check. | When an IMEI prompt is active; a real lookup still requires an authorized API. |
| `/serial FCLD55FVN70G` | Submits a serial number. | When using the Apple serial workflow; a real lookup still requires an authorized API. |
| `Kofi Ama` | Gives the MoMo sender/account name for manual payment review. | Type as **normal text** after tapping “I've Paid” in a MoMo order. Do not include a slash. `/Kofi Ama` is also cleaned if sent, but normal text is recommended. |
| `/USA`, `/Ghana`, `/South Africa` | Provides a country/region for a virtual-number order. | Only when the bot asks for the number's country/region. |

The MoMo sender name is **not** a Telegram username and does not itself confirm payment. Admin must check the actual payment transaction.

## Admin commands

These work only for the Telegram account whose numeric ID is set in `ADMIN_USER_ID`.

| Command | What it does | Example |
|---|---|---|
| `/admin` | Shows the admin panel and command guide. | `/admin` |
| `/pending` | Opens the pending-payment list. | `/pending` |
| `/deliver ORDER_ID` | Opens a prompt to send the purchased item/code after payment is confirmed. | `/deliver GSX-ABC1234` |
| `/deliver ORDER_ID CONTENT` | Delivers the supplied code/content immediately after payment is confirmed. For Netflix orders, redemption instructions are appended. | `/deliver GSX-ABC1234 XXXX-XXXX-XXXX` |
| `/number ORDER_ID +123456789` | Sends a purchased virtual number after payment confirmation. | `/number GSX-ABC1234 +12025550123` |
| `/otp ORDER_ID 123456` | Sends a provider message/verification code for a virtual-number order after payment confirmation. Use only where permitted. | `/otp GSX-ABC1234 482913` |
| `/proof ORDER_ID DETAILS` | Attaches notes/proof details to an order record. | `/proof GSX-ABC1234 Bank reference checked` |

The admin inline buttons provide **Payments**, **Orders**, **Stats**, **Settings**, **Confirm Payment**, **Reject**, **Send Payment Details**, and **Deliver** actions. A payment confirmation is intended to be followed by a delivery action for gift-card, Netflix and number orders.

## Environment variables for Netlify

Set these in **Netlify → Site configuration → Environment variables**. Never publish the Telegram bot token or supplier API keys in the website HTML or GitHub.

```text
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
ADMIN_USER_ID=your_numeric_telegram_user_id
BOT_USERNAME=GSX_chechBot
BOT_NAME=GSX CHECK
SITE_URL=https://your-site-name.netlify.app
WEBHOOK_URL=https://your-site-name.netlify.app/.netlify/functions/gsxcheck
DATA_STORE_NAME=gsx-check-data
MOMO_NAME=Kofi Ama
MOMO_NUMBER=0240000000
MOMO_NETWORK=MTN
GIFT_CARD_PRODUCTS_JSON=[{"id":"us25","title":"Apple Gift Card","usd":25,"region":"US","mtcGhs":422.47}]
VIRTUAL_NUMBER_PRODUCTS_JSON=[{"id":"basic","title":"Virtual Number Basic","price":30,"duration":"30 days"}]
NETFLIX_PRODUCTS_JSON=[{"id":"netflix-us-10","title":"US Netflix Gift Card - $10","price":100,"region":"US","description":"Netflix gift card code"}]
```

**Replace every example value with your real settings.** The Netflix `price` above is only an example value; set the actual price you intend to charge in Ghana cedis. `NETFLIX_PRODUCTS_JSON` may contain multiple products. Each product needs a unique `id`, a customer-facing `title`, and a positive numeric `price`; `region` and `description` are optional.

The bot uses Netlify Blobs through `@netlify/blobs`. Keep the `DATA_STORE_NAME` stable after deployment so the bot continues using the same store.

## Netflix order lifecycle

1. Customer opens **Digital Store → Netflix Cards**.
2. Customer chooses a configured product and sees its region, description and GHS price.
3. Customer chooses MoMo or crypto.
4. Customer taps “I've Paid” / submits payment claim.
5. Admin manually verifies the transaction and taps **Confirm Payment**.
6. Admin obtains a valid code from the supplier and delivers it with `/deliver ORDER_ID CODE` or the Deliver button.
7. The bot sends the code and basic redemption guidance.

A live supplier API is not included because no supplier has been selected and no API documentation/credentials were provided. Once you have the supplier's official API documentation, the fulfillment adapter can be added without pretending that manual delivery is automated.

## Deployment

- Deploy this project folder to Netlify.
- Function endpoint: `/.netlify/functions/gsxcheck`
- Configure the Telegram webhook to the full function URL shown in `WEBHOOK_URL`.
- Set the environment variables above and redeploy after changes.
- Verify `/start`, a test order, MoMo sender-name input, admin confirmation and a test delivery before taking real payments.

## Project files

- `public/index.html` — GSX CHECK landing page.
- `netlify/functions/gsxcheck.mjs` — Telegram webhook, customer workflows, order/payment logic, admin actions and Netflix redemption guidance.
- `netlify.toml` — Netlify build/function configuration.
- `package.json` — package metadata and Netlify Blobs dependency.
