# GSX CHECK — Clean V3

Professional Telegram + Netlify structure for device checks, website-check requests, manual payments, digital fulfillment, and lawful virtual-number fulfillment.

## Customer commands

These commands are state-specific; they are **not** Telegram usernames.

- Device IMEI: `/imei 352851114023666`
- Apple serial: `/serial FCLD55FVN70G`
- MoMo sender/account proof (only when the bot asks after “I've Paid”): `/Kofi Mensah`
- Virtual-number country/region (only when the bot asks): `/USA`, `/Ghana`, or `/South Africa`

Country flags are shown when the order is created. Common configured flags include 🇺🇸 🇬🇧 🇨🇦 🇬🇭 🇳🇬 🇰🇪 🇿🇦 🇦🇺 🇩🇪 🇫🇷 🇮🇳 🇯🇵 and more.

## Admin delivery commands

After manual payment confirmation:

- `/deliver ORDER_ID` — opens a delivery prompt
- `/deliver ORDER_ID CONTENT` — delivers directly
- `/number ORDER_ID +123456789` — sends a purchased virtual number
- `/otp ORDER_ID 123456` — sends a received verification code
- `/proof ORDER_ID DETAILS` — attaches proof/details to an order

Example:

```text
/number GSX-ABC1234 +12025550123
/otp GSX-ABC1234 482913
```

The bot also provides buttons for Confirm Payment, Reject, Send Payment Details, and Deliver.

## Important

Virtual-number delivery is intended for lawful use and only with services that permit virtual/VoIP numbers. The bot does not attempt to bypass anti-abuse, CAPTCHA, identity, or account-security controls.

Device and website reports remain `NOT VERIFIED` / `UNKNOWN` until a legitimate authorized provider API is configured. The system never fabricates blacklist, activation-lock, carrier-lock, or fraud results.

## Environment variables

```text
TELEGRAM_BOT_TOKEN
ADMIN_USER_ID
BOT_USERNAME=GSX_chechBot
BOT_NAME=GSX CHECK
SITE_URL=https://gsx-check.netlify.app
WEBHOOK_URL=https://gsx-check.netlify.app/.netlify/functions/gsxcheck
DATA_STORE_NAME=gsx-check-data
MOMO_NAME=...
MOMO_NUMBER=...
MOMO_NETWORK=MTN
GIFT_CARD_PRODUCTS_JSON=[...]
VIRTUAL_NUMBER_PRODUCTS_JSON=[...]
```

No permanent crypto wallet address is required. Crypto instructions are supplied manually per order.

## Deploy

Deploy the folder as a Netlify site. The Telegram webhook should point to:

`https://gsx-check.netlify.app/.netlify/functions/gsxcheck`
