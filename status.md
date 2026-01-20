# Prodikeys Capture Status (Nov 11, 2025)

## Device Enumeration
- Creative Prodikeys connected over USB reports as VID:PID 13ba:0018 (`PCPlay Barcode PCP-BCG4209`).
- Exposes two USB HID interfaces only:
  - Interface 0 → keyboard (`/dev/input/event19`, `/dev/hidraw4`).
  - Interface 1 → mouse/consumer control (`/dev/input/event21-22`, `/dev/hidraw5`).
- No ALSA MIDI ports appear (`aconnect -lo` shows only `Midi Through`).

## Capture Attempts
- `cat /dev/hidraw4|5 | hexdump` with sudo and 4 s timeout: no data captured while piano keys were pressed.
- `usbhid-dump -d 13ba:0018 -e stream` with 4 s timeout: no interrupt reports observed.
- `cat /sys/kernel/debug/usb/usbmon/3u` (bus 3) with 4 s timeout: no USB traffic seen during piano input.

## Interpretation
- The USB bridge appears to expose only standard keyboard/mouse HID endpoints; the piano keybed likely communicates over the legacy PS/2 signals without reaching the USB HID layer.
- Capturing MIDI data via USB alone is not yielding events, even under active keypress testing.

## Next Steps
- Inspect/tap the PS/2 clock/data lines between the Prodikeys controller and the keybed to capture raw scancodes.
- Once PS/2 signalling is understood, evaluate translating the data into ALSA MIDI events (e.g. with a microcontroller or kernel module).
- Optionally revisit Windows driver behaviour for protocol clues if PS/2 capture proves inconclusive.
