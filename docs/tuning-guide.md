# Tuning Guide — The Lot Standard

Once a card flashes clean and validates, Renewable Revolt applies a consistent
tune before it ships.  The goal for a donated machine is not the last percentile
of performance — it is low heat, low noise, and long life, applied the same way
across a whole lot so no card needs individual babysitting.

Tuning is done in MSI Afterburner and saved to a profile with **apply-at-startup
enabled** — without that, the undervolt and fan curve evaporate on the first
reboot and the recipient ends up running a bone-stock, loud, full-voltage card.

---

## Undervolt — conservative and universal

The undervolt floor is per-chip (silicon lottery), so find a given card's floor
for curiosity, but **standardize the lot on a conservative value every card
handles** without per-card validation:

- Start at **−50 mV** core voltage offset, stock clocks, and confirm stability
  under load.
- Step deeper only to characterize the batch — **−75 mV, then −100 mV** — watching
  for the black-screen crash-protection trip, which is the brown-out signal.
  Back off one notch from wherever a card trips.
- For the shipped standard, **−50 mV (−62 if the batch proves strong)** is the
  right universal.  The payoff is lower power, lower heat, and longer life — not
  speed.

Leave the **power limit at +50%** so the card never throttles.  Do not touch the
memory straps: on these cards the mining straps already deliver top-of-cohort
bandwidth, and for inference use (memory-bandwidth-bound) that is a feature.

---

## Quiet fan curve — universal

Polaris does not throttle until ~84 °C, so there is abundant thermal headroom to
trade for silence.  This curve runs the card warm in the healthy 60s under load
while keeping the fan quiet through the common range, with a steep safety ramp up
top.  It is a clean lot universal.

| Temp (°C) | Fan (%) |
|-----------|---------|
| ≤40 | 28 |
| 50 | 33 |
| 60 | 45 |
| 68 | 58 |
| 74 | 72 |
| 80 | 90 |
| 85 | 100 |

- **28% floor** — do not go lower; worn bearings on a broken-in card stall, click,
  or buzz below ~28% PWM, and lower sustained RPM extends their remaining life.
- **Temp hysteresis 3 °C** — stops the fan hunting up and down around a threshold,
  which is its own source of noise and wear.
- Update period **2500 ms**, no "force update on each period."

Paired with the undervolt, a recovered reference 580 settles around **61 °C at
~46% fan** under a sustained Kombustor load — and real gaming or Vulkan inference,
which draw less than that power-virus, run cooler and quieter still.

---

## Validate before it ships

- **MSI Kombustor** at stock clocks until the artifact counter holds **0/0** over
  a soak (15–20 minutes is a solid rubber stamp; a nine-minute clean run is
  effectively validated).
- **UserBenchmark** for a cohort percentile sanity check.  A healthy recovered
  reference 580 on this tune lands at **95–99th percentile** among RX 580s.
- Then save the Afterburner profile and enable apply-at-startup.
