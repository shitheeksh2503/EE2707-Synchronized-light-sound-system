# Audio-Reactive LED Driver with Synchronized Class-D Speaker

An analog system that listens to an audio signal, extracts its amplitude
envelope, and uses it to drive an LED's brightness in real time — while
simultaneously amplifying the same audio through a Class-D speaker output, so
light and sound stay perfectly in sync.

## What's in the circuit

**Sources + Audio Input**
`V1` = 5V rail. `R5/R6` divider generates `Vcm` (2.5V mid-supply reference for
biasing single-supply op-amps). `V5` is the test audio source (`Vaudio`,
2kHz/0.5V sine on a 2.5V offset).

**Bandpass + Adder + Peak Detector**
`U10`/`U11` are 1kHz and 3kHz active bandpass filters extracting
`Vout_bpf1`/`Vout_bpf2` from `Vaudio`. `U12` sums them into `Vout_adder`. `U13`
+ `D2` + `C12` peak-detect that signal into `Vref`, a DC voltage tracking the
audio's amplitude envelope — this is the control signal for the LED driver.

**LED Circuitry**
A buck-converter LED driver: `OPA1` generates a ramp (`Vramp`), `LM339`
comparators turn it into PWM (`Vpwm`/`Vctrl`), and `SN74AHC00`/`CD4069` logic
creates non-overlapping gate signals (`Vgate_P`/`Vgate_N`) to prevent
shoot-through. `LTC1693-1` drives the high/low-side MOSFETs switching node
`Vsw`, filtered by `L1`/`C5` into the LED (`D1`). `U9` is the feedback error
amp comparing sensed LED current (`Vfb`) against `Vref`, closing the loop so
LED brightness tracks the audio envelope.

**Speaker Circuitry**
`U14` converts `Vout_adder` into a differential pair (`Vin_a+`/`Vin_a-`).
`U15`/`U16` compare these against `Vramp` to generate differential PWM
(`Vpwm_p`/`Vpwm_n`). Duplicate non-overlap logic shapes drive signals for two
BJT push-pull half-bridges (`Q1-Q4`), which switch the speaker load through
an `L2`/`C17`/`C18` filter to reconstruct the amplified audio.

## How it works together

Both outputs come from the same `Vout_adder` signal — one path peak-detects
it into a brightness reference for the LED feedback loop, the other
differentially PWM-modulates it for the Class-D speaker stage — so the LED
and speaker respond to the same audio in real time.
