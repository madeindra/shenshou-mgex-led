# Shenshou MGEX LED Controller

A browser-based controller for the **Shenshou MGEX Unicorn Bluetooth LED kit**. It replaces the "炫彩灯光" WeChat mini-program, so you can control the kit from Chrome without WeChat.

Everything runs in a single `index.html` file using the [Web Bluetooth API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API). No install, no server, no account.

## Features

- **Color**: quick modes, a color wheel with 14 preset colors, and 9 effects (Solid, Breathe, Strobe, Comet, Flow, Stack, Marquee, Chase, Curtain) that use the selected color
- **Scenes**: the 14 built-in multicolor modes (rainbow and three-color patterns)
- **Adjust**: brightness, speed and polarity
- **Presets**: set each of the 17 LEDs to its own color, dim individual LEDs, pick an effect, brightness, speed and polarity for the whole preset, and save presets in the browser
- **Backup and share**: export and import presets as JSON
- **Log**: every packet sent and every reply from the light, for debugging

## Requirements

Web Bluetooth only works in some browsers:

| Platform | Works? |
|---|---|
| Chrome or Edge on Android | Yes |
| Chrome or Edge on Windows, macOS, Linux, ChromeOS | Yes |
| iPhone / iPad | Only in a Web Bluetooth browser such as [Bluefy](https://apps.apple.com/app/id1492822055). Chrome and Safari on iOS do not support it. |
| Firefox, Safari | No |

The page must be opened from an `https://` address or from `localhost`.

## Usage

1. Open the hosted page (see below), or serve `index.html` locally.
2. If the kit is connected to any other app, close that app first. The kit accepts only one connection at a time.
3. Press **Connect** and pick your kit from the list. Kits show up with a name starting with `632-`.

### Hosting it yourself

The easiest option is GitHub Pages: fork this repo, then go to **Settings > Pages** and deploy from the `main` branch. Your copy will be at `https://<your-username>.github.io/shenshou-mgex-led/`.

To run it locally instead:

```sh
python3 -m http.server 8000
# then open http://localhost:8000 in Chrome
```

## Compatibility

Tested only with the Shenshou MGEX Unicorn LED kit, third batch (advertised name `632-…`). Other kits and production batches may use different firmware. If the page can't find or control your kit:

1. Check the kit's Bluetooth services with the free [nRF Connect](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-mobile) app.
2. Open **Settings** at the bottom of the page and enter the UUIDs you found.
3. **Copy share link** creates a link with your settings filled in.

If your kit behaves differently, please open an issue with your findings.

## How it works

The protocol was reverse engineered by capturing Bluetooth traffic between the mini-program and the kit (Android's HCI snoop log) and decoding it. The full protocol is documented in [PROTOCOL.md](PROTOCOL.md).

## Contributing

Contributions are welcome. Useful things to work on:

- Confirming the preset effect codes marked as inferred in [PROTOCOL.md](PROTOCOL.md)
- Testing with other kits or production batches
- Better names for the Scenes modes

To capture traffic yourself: enable **Bluetooth HCI snoop log** in Android developer options, use the mini-program, then take a bug report and extract `FS/data/misc/bluetooth/logs/btsnoop_hci.log` from the zip.

## Disclaimer

This is an unofficial project. It is not affiliated with or endorsed by Shenshou, Bandai, or the makers of the WeChat mini-program. Use it at your own risk.

It was tested only with the **Shenshou MGEX Unicorn LED kit, third batch**. It has not been tested with any other kit yet.

Mode code `06` in the Scenes list is not used by the official app and caused very fast flashing in testing, so it is left out.

## License

[MIT](LICENSE)
