# anonymous-virtual-credit-card

Anonymous crypto funded virtual credit cards (VCC) and PayPal gift balance.

Open-source, drop-in front-ends for the public [PayGate.to](https://paygate.to) card
APIs. No sign up, no KYC, no API keys, no build step, no dependencies — every file
here is a single HTML page you can open in a browser or paste into your own site.

## Files

| File | What it does |
|---|---|
| [`create-card.html`](create-card.html) | **One-time cards.** Order a Visa, Mastercard, PayPal balance or Binance gift card for a fixed amount, pay the assigned wallet in crypto, and get a `redeem_id`. |
| [`check-status.html`](check-status.html) | **Redeem a one-time card.** Enter a `redeem_id` to check payment status and get the redeem link. |
| [`reloadable-card.html`](reloadable-card.html) | **Reloadable cards.** A complete white-label account widget: sign up, top up in crypto, mint cards, load and unload them, reveal the number behind 2FA, and add to Apple Pay or Google Pay. |

## Reloadable cards

`reloadable-card.html` is a self-contained widget for the reloadable card
programme. Unlike a one-time card, the customer keeps a balance and can mint
several cards against it, then top those same cards up again for as long as they
hold them.

There is no email, no password and no personal data: an account is a **session
key** the customer holds, plus a **recovery token** shown once at sign up.

**What it covers**

* Sign up and sign in, with the slow-endpoint handling both need — a blocking
  wait panel and exactly one retry, never a loop.
* Crypto top-ups in BTC, XMR, LTC, ETH, USDC and USDT, with a QR code, the
  minimum deposit shown prominently, and an expiry countdown anchored to the
  API's own timestamps.
* Minting Aurora, Shadow and Eclipse cards — prices, per-tier caps and
  availability all read live from the API, never hardcoded.
* Loading and unloading, both with the $15.00 minimum enforced client side.
* Revealing the PAN, CVV, expiry and PIN behind a TOTP step-up, including
  first-time authenticator enrolment.
* Adding a card to Apple Pay or Google Pay, with the verification code relayed
  back to the page.
* Card and account statements, including foreign-currency charges and a flag
  when a transaction hit a closed merchant category.

### Using it

Open the file in a browser and it works as-is. To embed it in your own site,
copy the three blocks — the `<style>`, the `<div id="rlApp">` and the
`<script>` — into your page. Everything is scoped to `#rlApp`: every CSS rule is
prefixed with it, and the script defines no globals, so it cannot collide with
whatever your page already loads.

### Branding it

The design tokens sit at the top of the `<style>` block, on `#rlApp`. Change
them and the whole widget follows:

```css
#rlApp{
  --primary:#635bff;        /* buttons, links, accents          */
  --text:#0a2540;           /* headings and strong text         */
  --text-soft:#425466;      /* body copy                        */
  --card:#ffffff;           /* panel background                 */
  --bg-soft:#f6f9fc;        /* inset areas                      */
  --border:#e6e9ef;         /* hairlines                        */
  --radius:14px;            /* corner rounding                  */
  --font:"Inter",-apple-system,"Segoe UI",Roboto,sans-serif;
}
```

### Earning commission (optional)

Out of the box the widget calls the plain deposit endpoint and takes nothing.
Fill in `CONFIG.affiliate` near the top of the `<script>` and every top-up made
through your copy pays you an instant percentage.

```js
affiliate: {
  btc: "1FTpXfNvv1obYpLLFHHh8eEK2vaLwFTHQ2",
  xmr: "4...",
  ltc: "L...",
  eth: "0xYourEvmWallet"        // also covers erc20/usdc and erc20/usdt
},
affiliate_fee: "0.075"           // 7.5%
```

A payout wallet has to be on the same chain as the coin the customer picks,
which is why wallets are keyed by ticker. **Any coin you leave blank falls back
to the plain endpoint** rather than sending a commission to an address that
cannot receive it. Set `sub_affiliate` and `sub_affiliate_fee` the same way to
run a second tier on top.

## API documentation

* [Reloadable Virtual Cards API](https://paygate.to/docs/virtual-credit-cards-api/reloadable/)
* [One-Time Virtual Cards API](https://paygate.to/docs/virtual-credit-cards-api/)
* [All API docs](https://paygate.to/docs/)

## Notes

These pages talk only to `https://api.paygate.to`. There is no telemetry and no
third-party requests.

The reloadable widget stores the session key in a cookie in the customer's own
browser and nowhere else. Because the account has no email attached, **a lost
recovery token cannot be recovered by anyone** — the widget makes the customer
acknowledge that before it will continue past sign up, and you should keep that
behaviour if you modify it.
