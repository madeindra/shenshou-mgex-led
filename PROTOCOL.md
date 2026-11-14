# Protocol

Bluetooth Low Energy protocol of the Shenshou MGEX Unicorn LED kit, decoded from HCI snoop captures of the "炫彩灯光" WeChat mini-program.

Status labels: **confirmed** (seen in a capture and tested on the kit), **inferred** (follows the pattern but not captured).

## GATT

| Item | UUID | Notes |
|---|---|---|
| Advertised service | `0xAF30` | Used only for discovery |
| Device name | `632-<id>` | The suffix differs per unit |
| Control service | `0xA950` | |
| Write characteristic | `0xA951` | Write without response |
| Notify characteristic | `0xA953` | Replies and status |

Other services on the device (`0x180A` Device Information, `0xAE00`, `0xAE3A`) are not used by the mini-program's controls.

## Packet format

```
55 AA [CMD] [LEN] [DATA x LEN] 00 00
```

- `55 AA`: header for packets sent to the kit
- `LEN`: number of data bytes
- Two trailing `00` bytes
- **Colors are sent in G, R, B order**

## Commands

| CMD | Purpose | Data | Status |
|---|---|---|---|
| `01` | Init, sent once after connecting | `00` | confirmed |
| `02` | Set mode with color (see below) | 11 bytes | confirmed |
| `03` | Brightness | `01`-`64` (1-100) | confirmed |
| `04` | Speed | `01`-`64` (1-100) | confirmed |
| `05` | Polarity | `00` = positive, `01` = negative | confirmed |
| `06` | Unknown, sent when leaving the preset screen | `00` (packet has only one trailing `00`) | seen once |
| `07` | Set one LED (live preview) | `[LED] 01 [G R B]` | confirmed |
| `07` | Select an LED in the editor | `[LED] 00 00 00 00` | seen |
| `08` | Apply a full 17-LED preset | 55 bytes, see below | confirmed |
| `09` | Sent when opening the preset screen | `00` | confirmed |
| `0A` | Preset effect | effect code, see below | confirmed for `01`, `09` |

Sliders send a stream of `03` / `04` writes while dragging.

### CMD 02: modes

```
55 AA 02 0B [GROUP] [CODE] [G R B] [G2 R2 B2] 64 64 00 00 00
```

**Group `01`**, single color. The color bytes set the color.

| Code | App name | Name in this project |
|---|---|---|
| `C9` | Red | Red |
| `CA` | Green | Green |
| `CB` | Two-color | Two-tone |
| `CC` | Blue | Blue |
| `CD` | Yellow | Yellow |
| `CE` | Electronic | Electro |
| `01` | Static | Solid |
| `02` | Breath | Breathe |
| `03` | Strobo | Strobe |
| `04` | Tail | Comet |
| `05` | Flow | Flow |
| `06` | Pile up | Stack |
| `07` | Horse racing | Marquee |
| `08` | Chasing light | Chase |
| `09` | Curtain call | Curtain |

**Group `03`**, multicolor scenes ("Colorful" tab). The app always sends `00 00 FF` and `00 FF 00` as colors.

| Code | App name (truncated) | Name in this project |
|---|---|---|
| `01` | seven color gradi… | Rainbow fade |
| `02` | seven color irides… | Rainbow shimmer |
| `03` | seven color strobe | Rainbow strobe |
| `04` | seven color transi… | Rainbow jump |
| `05` | seven color sp… | Rainbow sparkle |
| `06` | (not in the app) | Not used: fast flashing |
| `07` | three-coloured pur… | Tri-color chase |
| `08` | three-colours of pu… | Purple trio |
| `09` | three-colours of ye… | Yellow trio |
| `0A` | three-colours gradi… | Tri-color fade |
| `0B` | three-colours gradi… | Tri-color fade 2 |
| `0C` | three-colours strob… | Tri-color strobe |
| `0D` | three-colours flashi… | Tri-color flash |
| `0E` | tri color: red, gree… | Red, green, blue |
| `0F` | three colored, ye… | Yellow, cyan, purple |

### CMD 08: preset

```
55 AA 08 37 [17 x (G R B)] [EFFECT] [BRIGHTNESS] [SPEED] 01 00 00
```

Colors are ordered by LED index 1 to 17. Effect, brightness and speed apply to the whole preset, not to single LEDs. The final `01` is unknown and was always `01` in captures. Polarity is not part of this packet: this project sends CMD `05` right after CMD `08` to apply a preset's polarity.

### Preset effects (CMD 0A and the EFFECT byte of CMD 08)

| Code | App name | Name in this project | Status |
|---|---|---|---|
| `01` | static | Solid | confirmed |
| `02` | breathe | Breathe | inferred |
| `03` | stroboscopic | Strobe | inferred |
| `04` | tail | Comet | inferred |
| `05` | flow | Flow | inferred |
| `06` | pile up | Stack | inferred |
| `07` | horse racing | Marquee | inferred |
| `08` | electronic | Electro | inferred |
| `09` | beat | Beat | confirmed |

## LED index map

The kit's LED indices don't follow the app's numbering. Positions as seen from the front:

| Position | LED index |
|---|---|
| Head | 17 |
| Neck | 13 |
| Left shoulder / right shoulder | 15 / 11 |
| Left chest / right chest | 14 / 10 |
| Stomach | 9 |
| Left forearm / right forearm | 16 / 12 |
| Left waist / right waist | 8 / 7 |
| Left thigh / right thigh | 4 / 3 |
| Left knee / right knee | 5 / 2 |
| Left foot / right foot | 6 / 1 |

"Left" means the left side of the screen.

## Notifications (handle 0xA953)

| Packet | Meaning |
|---|---|
| `55 AA [CMD] 01 01 00 00` | Acknowledgement of a command |
| `55 FF 01 0C …` | Status after connecting, includes the current mode and color |
| `55 AA 00 02 [xx] 01 9C 41` | Periodic status, meaning unknown |
