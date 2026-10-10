# openmouse-cobra — Razer Cobra brightness patch

A focused patch for the Razer Cobra in [OpenMouse](https://github.com/OpenMouse-Project/openmouse):
the WebHID driver file `cobra-hid.ts` extended with a **continuous, persistent brightness
control** (0–100%, including off).

## What this adds

- `cobra-hid.ts` — the Razer Cobra WebHID driver (lives at `src/drivers/razer/cobra-hid.ts`
  inside the `@openmouse/protocol` package) with brightness read/write support.
- Brightness writes target the logo LED (`RAZER_LED.logo`) over the extended-matrix
  transaction id (`0x1f`) using `VARSTORE` storage, so the level is stored **on the mouse**
  and survives unplugging / moving to another PC.
- Every whole percent from 0–100 is offered; `0` turns the LEDs off.

## Why brightness is the one lighting setting that sticks

Effects (Spectrum, Breathing, …) are rendered by Synapse in software and don't survive on the
mouse. Brightness is the lighting setting Razer actually stores in the mouse's persistent store
(`VARSTORE`) — which is why it's the piece worth controlling via OpenMouse.

## Where it fits

A single standalone file can't be consumed by the app by itself — this repo is a reference copy
of the patch. The complete change spans two OpenMouse repositories (user forks, branch
`cobra-brightness`):

| File | Repository | Change |
|------|-----------|--------|
| `src/drivers/razer/cobra-hid.ts` | `mouse-protocol` | brightness read/write + 0–100 level list |
| `src/drivers/razer/cobra-hid.test.ts` | `mouse-protocol` | byte-level brightness tests |
| `src/app/cards/LightingCard.tsx` | `openmouse` | brightness slider UI for continuous level sets |

## Using it with the OpenMouse web app

```sh
# check the forks out as siblings
git clone git@github.com:saurabh-nehra/openmouse.git
git clone git@github.com:saurabh-nehra/mouse-protocol.git

cd mouse-protocol
git checkout cobra-brightness
npm install
npm run build
cd ..

cd openmouse
git checkout cobra-brightness
npm install
npm install --no-save --package-lock=false ../mouse-protocol
npm run dev
```

Open the printed localhost URL in **Chrome or Edge** (WebHID — Firefox won't work), connect the
Cobra, then go to **Lighting** and use the **Brightness** slider.

## Verified on hardware

- Tested with a Razer Cobra (USB `1532:00a3`) through the OpenMouse dev server.
- Brightness — including 0% off — persists after replugging and on other PCs, because Razer
  stores it on the device.