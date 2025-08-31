# term_query.sh

A small Bash script that **probes terminal capabilities** by sending standard VT/DEC/XTerm control sequences and parsing the replies.  
It’s useful for debugging, comparing terminal emulators, or just learning what features your terminal supports.

---

## Features

The script currently queries and decodes:

- **DECRQSS “ q”** — Cursor shape (block / underline / bar, blinking or steady)  
- **DECRQM ?12** — Cursor blink mode  
- **CSI u** — Keyboard enhancement / CSI `u` support flags  
- **DA1 (Primary Device Attributes)** — Terminal “family” (VT100, VT220, VT320, etc.) and features  
- **DA2 (Secondary Device Attributes)** — Terminal type, firmware version, cartridge number  
- **DA3 (Tertiary Device Attributes / DECRPTUI)** — Terminal Unit ID (site + serial; xterm reports zeros)  
- **DECRQM ?2004** — Bracketed paste mode  
- **DECRQM ?1004** — Focus tracking  
- **DECRQM ?1006** — SGR mouse mode  

The raw replies are shown with `hexyl` (if available) or `hexdump -C`, and then parsed into human-readable summaries.

---

## Usage

```bash
./term_query.sh [--passthrough]
```

Options:

- `--passthrough`  
  If running inside **tmux**, wrap the queries using the passthrough mechanism (`DCS Ptmux`) so they reach the underlying terminal.

---

## Example Output

```
Preparing terminal...
Sending DIRECT queries to the current terminal...
→ DECRQSS cursor shape: Ps=2  [steady block]
→ DECRQM ?12 (blink): steady (off)  [state=2]
→ DA1 (primary): ?62;1;2;22c  [VT220 features: 132-columns, printer, ANSI-color]
→ DA2 (secondary): >0;3793;0 c  [type=VT100  version=3793  cartridge=0]
   note: this looks like an xterm-style reply (Pp=0, Pc=0; Pv is patch/version).
→ DA3 (tertiary / DECRPTUI): DCS ! | 0;0 ST
   note: looks like an xterm-style reply (zeros for site/serial).
→ CSI u: ON  [mode 1: disambiguate escape codes]
→ DECRQM ?2004: bracketed-paste: enabled
→ DECRQM ?1004: focus-events: disabled
→ DECRQM ?1006: mouse-sgr: enabled
Test finished.
```

---

## Requirements

- `bash`  
- `stty`, `dd` (standard on Unix-like systems)  
- [`hexyl`](https://github.com/sharkdp/hexyl) (optional, for nicer hex dumps; falls back to `hexdump -C`)  
- (Optional) [`tmux`](https://github.com/tmux/tmux) if using passthrough mode

---

## Reference

The control sequences are based on the [XTerm Control Sequences documentation](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html).  
VT100/VT220 family attributes are decoded according to DEC and xterm conventions.

---

## License

MIT — see [LICENSE](LICENSE).
