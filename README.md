# SureCart Country Code Phone Input

Add a country code selector, phone masks, fixed DDI, and international phone number normalization to the SureCart checkout phone field in WordPress.

This snippet works directly with SureCart's native phone field, including its nested Shadow DOM structure.

## Features

- Country code selector inside the SureCart phone field
- Country flag display
- Fixed and non-removable DDI
- Visual `+` prefix
- Country-specific phone masks
- Exact phone number length validation
- Automatic international number normalization
- Supports national trunk prefixes such as the initial `0`
- Keeps the original SureCart phone field working
- Compatible with dynamically loaded SureCart checkout forms
- No external JavaScript libraries required
- Works with Code Snippets or a WordPress child theme

## Example

Japan:

```text
Displayed:

🇯🇵 +81 | 090-7788-7788
```

Saved internally:

```text
819077887788
```

The customer can also enter:

```text
9077887788
```

and the saved value will still be:

```text
819077887788
```

The `+` symbol is only visual and is not stored.

The DDI is outside the editable phone input, so the customer cannot accidentally remove it.

## Why This Is Necessary

SureCart uses nested Shadow DOM components for its checkout phone field.

The structure is similar to:

```text
sc-customer-phone
  #shadow-root
    sc-phone-input
      #shadow-root
        input[type="tel"]
```

Because of this, traditional phone mask plugins and scripts using only:

```javascript
document.querySelector()
```

usually cannot access the actual phone input.

This solution recursively traverses Shadow DOM roots and integrates directly with the native `sc-phone-input` component.

## Supported Countries

Current configuration includes:

| Country | Code | DDI |
| --- | --- | --- |
| Japan | JP | +81 |
| Brazil | BR | +55 |
| Argentina | AR | +54 |
| Bolivia | BO | +591 |
| Chile | CL | +56 |
| Colombia | CO | +57 |
| Ecuador | EC | +593 |
| Guyana | GY | +592 |
| Paraguay | PY | +595 |
| Peru | PE | +51 |
| Suriname | SR | +597 |
| Uruguay | UY | +598 |
| Venezuela | VE | +58 |
| Italy | IT | +39 |
| Portugal | PT | +351 |
| United States / Canada | US | +1 |

Additional countries can easily be added to the configuration.

## Installation

### Option 1: Code Snippets

Install the WordPress plugin:

```text
Code Snippets
```

Then:

1. Go to `Snippets`
2. Click `Add New`
3. Select PHP snippet
4. Paste the SureCart Country Code Phone Input code
5. Configure it to run on the frontend or across the entire site
6. Save and activate the snippet

### Option 2: Child Theme

Add the PHP code to:

```text
functions.php
```

inside your WordPress child theme.

Using a child theme is recommended instead of editing the parent theme directly.

## Country Configuration

Countries are configured inside the `PAISES` array.

Example:

```javascript
{
  cc: 'BR',
  nome: 'Brasil',
  ddi: '55',
  digitos: 11,
  zeroInicial: false,
  mascara: '(##) #####-####',
  exemplo: '(31) 98888-7777'
}
```

### Properties

#### `cc`

ISO country code.

```javascript
cc: 'BR'
```

#### `nome`

Country name displayed in the selector.

```javascript
nome: 'Brasil'
```

#### `ddi`

International dialing code without the `+` symbol.

```javascript
ddi: '55'
```

#### `digitos`

Exact number of digits expected after normalization.

```javascript
digitos: 11
```

#### `zeroInicial`

Allows the user to enter a national trunk prefix such as `0`.

```javascript
zeroInicial: true
```

When enabled, the initial zero is accepted visually but removed from the normalized international number.

#### `mascara`

Phone mask when the national number does not contain a trunk prefix.

```javascript
mascara: '##-####-####'
```

#### `mascara0`

Optional mask used when the customer enters an initial zero.

Example for Japan:

```javascript
mascara0: '###-####-####'
```

#### `exemplo`

Placeholder displayed inside the phone field.

```javascript
exemplo: '090-7788-7788'
```

## Japan Example

Japanese customers commonly write mobile numbers like:

```text
090-7788-7788
```

However, the international version is:

```text
+81 90 7788 7788
```

This script accepts both:

```text
09077887788
```

and:

```text
9077887788
```

Both are normalized internally as:

```text
819077887788
```

## Brazil Example

Displayed:

```text
🇧🇷 +55 | (31) 98888-7777
```

Saved:

```text
5531988887777
```

## Ecuador Example

Displayed:

```text
🇪🇨 +593 | 099 123 4567
```

Saved:

```text
593991234567
```

The national `0` is accepted but removed before saving.

## Paraguay Example

Displayed:

```text
🇵🇾 +595 | 0981 123 456
```

Saved:

```text
595981123456
```

## Uruguay Example

Displayed:

```text
🇺🇾 +598 | 099 123 456
```

Saved:

```text
59899123456
```

## Venezuela Example

Displayed:

```text
🇻🇪 +58 | 0414-123-4567
```

Saved:

```text
584141234567
```

## Argentina Special Handling

Argentina requires special handling for mobile numbers.

A customer may enter:

```text
11 1234-5678
```

The international mobile format may require:

```text
54 9 11 1234 5678
```

A country configuration can therefore define an additional international prefix:

```javascript
prefixoInternacional: '9'
```

Normalization can then use:

```javascript
return pais.ddi + (pais.prefixoInternacional || '') + nacional;
```

Result:

```text
5491112345678
```

## Internal Storage

The recommended storage format contains digits only.

Example:

```text
819077887788
```

instead of:

```text
+819077887788
```

The `+` remains visible in the checkout interface but does not need to be stored.

This makes the phone number easier to use with:

- WhatsApp APIs
- n8n
- CRM platforms
- Brevo
- Google Sheets
- Webhooks
- Marketing automation tools
- Custom APIs

## Phone Validation

Each country can define an exact national phone number length.

For example:

```javascript
digitos: 10
```

The script limits the amount of digits entered and can prevent the checkout from accepting incomplete phone numbers.

## Dynamic Checkout Support

SureCart checkout components may be rendered after the main page loads.

The script therefore uses:

```javascript
MutationObserver
```

plus a temporary scan interval to detect newly created SureCart phone fields.

This allows it to work on dynamically rendered checkout forms.

## No External Dependencies

This implementation does not require:

- jQuery
- intl-tel-input
- Inputmask
- Cleave.js
- third-party WordPress phone plugins

Everything runs with native JavaScript.

## Important

Always test the snippet after major SureCart updates.

SureCart internally uses Web Components and Shadow DOM. Changes to component names or internal structure may require adjustments to the selector logic.

## Compatibility

Designed for:

```text
WordPress
SureCart
Code Snippets
Modern browsers with Shadow DOM support
```

## License

MIT License

You are free to use, modify, distribute, and adapt this project.

## Contributions

Pull requests, bug reports, improvements, and additional country phone masks are welcome.

## Disclaimer

This project is not officially affiliated with SureCart.

SureCart is a trademark of its respective owners.
