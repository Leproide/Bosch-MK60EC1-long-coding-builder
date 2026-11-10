# MK60EC1 Coding Builder

Calcolatore di codifica lunga per centraline ABS/ESP **Bosch/ATE MK60EC1** (indirizzo 03) montate su auto del gruppo VAG: Audi A3 8P, VW Golf/Jetta 5–6, Golf Plus, Touran, Caddy, Scirocco, Eos, Beetle, Seat Leon/Altea/Toledo, Skoda Octavia/Superb/Yeti.

La codifica MK60EC1 è legata al VIN: una parte della stringa deriva dai caratteri del VIN, un'altra è un checksum a specchio di bit dei byte di configurazione. Una codifica copiata da un'altra auto viene rifiutata. Questo strumento costruisce la stringa partendo dal VIN e dalla configurazione del veicolo, così una centralina usata o di ricambio si può codificare senza accesso online.

È un singolo file HTML statico: niente server, niente build, niente tracciamento, nessuna richiesta di rete durante l'uso.

## Funzioni

- Codifica generata dal VIN più le opzioni di ogni byte documentato (modello, freni, G608/G85, trazione, coppia motore, Hill Hold, tasto TCS/ASR, ACC, tipo ESP, TPMS, PLA, XDS, sensori ruota, Side Assist, tasto ESC).
- Tutte e tre le lunghezze: 36, 38 e 40 caratteri esadecimali, in base all'indice software della centralina.
- Checksum dei byte 8, 10, 12 e 14 ricalcolati in automatico.
- Codifica colorata per tipo (configurazione, VIN, checksum) e tabella byte per byte con i singoli bit.
- Avvisi per combinazioni non supportate (es. XDS su firmware AE, TPMS+ su codifiche corte) e per caratteri VIN fuori dalle tabelle pubblicate.
- Preset, tra cui uno verificato su un'auto reale.
- Interfaccia in italiano o inglese scelta in base alla lingua del browser (italiano se il browser è in italiano, inglese in tutti gli altri casi), con pulsante per cambiarla.
- Tema chiaro e scuro.

## Uso

1. Apri `index.html` nel browser oppure usa il sito GitHub Pages (vedi sotto).
2. Leggi l'indirizzo 03 con lo strumento di diagnosi e annota il codice software (es. `1K0907379AE`). Scegli la lunghezza corrispondente.
3. Inserisci il VIN **memorizzato nel quadro strumenti** (indirizzo 17).
4. Scegli un preset oppure imposta ogni byte dai codici PR dell'auto (adesivo nel vano ruota di scorta o libretto di manutenzione).
5. Copia la codifica, verificala con un validatore VIN/codifica e scrivila nell'indirizzo 03.
6. Dopo la codifica: accesso di sicurezza `40168`, impostazioni di base `060` (G85), `063` (accelerazione laterale), `066` (G201), `069` (accelerazione longitudinale), poi apprendimento dei fine corsa sterzo nell'indirizzo 44. Cancella gli errori su tutti i moduli e fai una prova su strada.

### Lunghezza della codifica per indice software

| Caratteri | Nome secondo lprot | Indice software |
|---|---|---|
| 36 | «17 byte» | AD/CD/AE/CE/AM/AN/AP/BA, 1JD907379F, 3T0907379C |
| 38 | «18 byte» | AH/AJ/AR/AS/AT/BB–BL, 2K0907379C/D/E, 5C0907379C |
| 40 | «19 byte» | BM/BR/BS/CA/CB/CC |

lprot numera i byte da 0: una codifica «17 byte» ha i byte 0–17, cioè 18 byte reali.

### Byte derivati dal VIN

| Byte | Origine |
|---|---|
| 1 | 7° carattere del VIN (ASCII + 0x0A) |
| 3 | 8° carattere del VIN (ASCII + 0xC2) |
| 5 | 0x22 + 13ª cifra del VIN |
| 7 | 0xFA + 14ª cifra del VIN |
| 9 | 0x0B + 15ª cifra del VIN |
| 11 | 0xE4 + 16ª cifra del VIN |
| 13 | 0x19 + 17ª cifra del VIN |
| 8, 10, 12, 14 | Specchio di bit dei byte 0, 2, 4, 6 |

## Codifica verificata

| Auto | Centralina | Codifica | Esito |
|---|---|---|---|
| Audi A3 8P MY2009, 2.0 TDI CR (CBAB), trazione anteriore, manuale, guida a sinistra, Italia | 1K0907379AE H35 SW 0106 | `11424012092200FC881002EB901C00423900` | Accettata e funzionante |

Quell'auto usa `40` nei bit 6–7 del byte 15, anche se lprot indica `80` per la guida a sinistra. Se hai una codifica confermata su un'altra auto, apri una issue o una pull request con codice ricambio, codifica, codici PR ed esito.

## Limiti noti

- Lo strumento controlla la coerenza interna della stringa, non che la configurazione corrisponda all'auto. Una centralina che accetta la codifica non dimostra che sia corretta.
- Bit del lato guida nel byte 15: le fonti sono in conflitto (vedi sopra). Dopo la codifica controlla il segno del G85 nel blocco di misura 004: sterzando a sinistra il valore deve essere positivo.
- Le formule VIN riproducono tutti i valori pubblicati; le lettere nelle posizioni 13–17 del VIN non sono verificate.
- lprot segnala come problematici i firmware AD e AE e consiglia l'aggiornamento a `1K0907379CD_0120.sgo` / `1K0907379CE_0120.sgo`. Prima salva le adattazioni degli indirizzi 03 e 44.

## Sicurezza

L'ABS/ESP è un sistema di sicurezza. Usa questo strumento solo se sei qualificato per intervenire sugli impianti frenanti, verifica ogni codifica prima di scriverla, completa le impostazioni di base e prova l'auto su strada prima di riconsegnarla. Il software è fornito senza alcuna garanzia (vedi Licenza).

## GitHub Pages

Settings → Pages → Source: *Deploy from a branch*, ramo `main`, cartella `/ (root)`. Lo strumento sarà disponibile su `https://leproide.github.io/mk60ec1-coding-builder/`.

## Riconoscimenti

- Tabelle dei byte: lprot, ["Осознанное кодирование блоков ATE MK60EC1"](https://www.drive2.ru/l/623620456359922967/) su Drive2.
- Decoder usato per i controlli incrociati: [phnahes/vw_mk60ec1_abs_decoder](https://github.com/phnahes/vw_mk60ec1_abs_decoder).

## Licenza

Questo progetto è distribuito con licenza **GNU General Public License v3.0**. Il testo completo è nel file [LICENSE](LICENSE).

## Autore

[https://github.com/Leproide](https://github.com/Leproide)
