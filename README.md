# MK60EC1 Coding Builder

Long coding calculator for **Bosch/ATE MK60EC1** ABS/ESP control units (address 03) used on VAG cars: Audi A3 8P, VW Golf/Jetta 5–6, Golf Plus, Touran, Caddy, Scirocco, Eos, Beetle, Seat Leon/Altea/Toledo, Skoda Octavia/Superb/Yeti.

MK60EC1 codings are bound to the VIN: part of the string is derived from VIN characters, part is a bit-mirror checksum of the configuration bytes. A coding copied from another car is rejected. This tool builds the string from the VIN and the vehicle configuration, so a used or replacement unit can be coded without online access.

It is a single static HTML file: no server, no build step, no tracking, no network requests at runtime.

## Online

- Site: [vagwiki.muninn.ovh/download/Calcolatori/ABS_Bosch_MK60EC1.html](https://vagwiki.muninn.ovh/download/Calcolatori/ABS_Bosch_MK60EC1.html)
- Mirror (GitHub Pages): [leproide.github.io/Bosch-MK60EC1-long-coding-builder](https://leproide.github.io/Bosch-MK60EC1-long-coding-builder/)

## Features

- Coding built from the VIN plus selectable options for every documented byte (model, brakes, G608/G85, drivetrain, engine torque, Hill Hold, TCS/ASR button, ACC, ESP type, TPMS, PLA, XDS, wheel sensors, Side Assist, ESC button).
- All three coding lengths: 36, 38 and 40 hex characters, selected by the unit's software index.
- Checksum bytes 8, 10, 12 and 14 recalculated automatically.
- Colour-coded output (configuration, VIN-derived, checksum) and a byte-by-byte table with bit view.
- Warnings for unsupported combinations (e.g. XDS on AE firmware, TPMS+ on short codings) and for VIN characters outside the published tables.
- Presets, including one verified on a real car.
- Italian or English interface, chosen from the browser language (Italian if the browser is set to Italian, English otherwise), with a manual switch.
- Light and dark theme.

## Usage

1. Open the [online version](#online), or download `index.html` and open it in any browser (it works offline).
2. Read address 03 with your diagnostic tool and note the software part number (e.g. `1K0907379AE`). Pick the matching coding length.
3. Enter the VIN **as stored in the instrument cluster** (address 17).
4. Choose a preset or set each byte from the car's PR codes (sticker in the spare wheel well or service book).
5. Copy the coding, check it with a VIN/coding validator, then write it to address 03.
6. After coding: security access `40168`, basic settings `060` (G85), `063` (lateral acceleration), `066` (G201), `069` (longitudinal acceleration), then steering end-stop learning in address 44. Clear all fault codes and road test.

### Coding length by software index

| Hex characters | lprot naming | Software index |
|---|---|---|
| 36 | "17 bytes" | AD/CD/AE/CE/AM/AN/AP/BA, 1JD907379F, 3T0907379C |
| 38 | "18 bytes" | AH/AJ/AR/AS/AT/BB–BL, 2K0907379C/D/E, 5C0907379C |
| 40 | "19 bytes" | BM/BR/BS/CA/CB/CC |

lprot numbers bytes from 0, so a "17-byte" coding has bytes 0–17, i.e. 18 real bytes.

### VIN-derived bytes

| Byte | Source |
|---|---|
| 1 | VIN character 7 (ASCII + 0x0A) |
| 3 | VIN character 8 (ASCII + 0xC2) |
| 5 | 0x22 + VIN digit 13 |
| 7 | 0xFA + VIN digit 14 |
| 9 | 0x0B + VIN digit 15 |
| 11 | 0xE4 + VIN digit 16 |
| 13 | 0x19 + VIN digit 17 |
| 8, 10, 12, 14 | Bit mirror of bytes 0, 2, 4, 6 |

## Verified coding

| Car | Unit | Coding | Result |
|---|---|---|---|
| Audi A3 8P MY2009, 2.0 TDI CR (CBAB), FWD, manual, LHD, Italy | 1K0907379AE H35 SW 0106 | `11424012092200FC881002EB901C00423900` | Accepted and working |

That car uses `40` in byte 15 bits 6–7 although lprot lists `80` for left-hand drive. If you have a confirmed coding for another car, open an issue or a pull request with the part number, the coding, the PR codes and the result.

## Known limitations

- The tool checks internal consistency, not whether the configuration matches the car. A unit that accepts a coding does not prove the coding is correct.
- Byte 15 steering-side bits: sources conflict (see above). After coding, check the sign of G85 in measuring block 004: steering left should read positive.
- The VIN formulas reproduce every published value; letters in VIN positions 13–17 are not verified.
- Firmware AD and AE are reported as problematic by lprot, who recommends updating to `1K0907379CD_0120.sgo` / `1K0907379CE_0120.sgo`. Save the adaptations of addresses 03 and 44 first.

## Safety

ABS/ESP is a safety system. Use this tool only if you are qualified to work on braking systems, verify every coding before writing it, complete the basic settings and road test the vehicle before returning it to service. The software is provided without any warranty (see License).

## Self-hosting

The tool is the single file `index.html`. Copy it to any static web server, or fork this repository and enable GitHub Pages (Settings → Pages → Source: *Deploy from a branch*, branch `main`, folder `/ (root)`).

## Credits

- Byte tables: lprot, ["Осознанное кодирование блоков ATE MK60EC1"](https://www.drive2.ru/l/623620456359922967/) on Drive2.
- Decoder used for cross-checking: [phnahes/vw_mk60ec1_abs_decoder](https://github.com/phnahes/vw_mk60ec1_abs_decoder).

## License

This project is licensed under the **GNU General Public License v3.0**. See [LICENSE](LICENSE) for the full text.

## Author

[https://github.com/Leproide](https://github.com/Leproide)
