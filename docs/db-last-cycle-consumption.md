# DB last-cycle consumption estimates

For model `38132406`, subtype `19504`, notification type `0x04` with body
type `0x05` exposes `last_cycle_water_consumption` in litres and
`last_cycle_energy_consumption` in kWh. These are estimated values for the
last cycle, not measured usage or cumulative counters. Both start as `None`
and retain their values until another valid consumption notification arrives;
zero is a valid report. Normal status messages do not clear them.

## Evidence and limitations

- The [DB v33 Lua protocol file](https://github.com/wuwentao/midea-lua/blob/main/lua/0xDB/T_0000_DB_33.lua#L627-L685)
  decodes `0405` water and power as little-endian 16-bit integers. Its
  [response assembly](https://github.com/wuwentao/midea-lua/blob/main/lua/0xDB/T_0000_DB_33.lua#L1370-L1402)
  publishes these raw values; it does not establish physical units or scaling.
- One actual end-of-wash observation on the specified model and subtype
  correlated type `04`, body bytes `05 ca 01 0a 00` (raw water 458, energy 10)
  with the app's estimated 45.800 L and 0.0100 kWh. Dividing by 10 and 1000,
  respectively, matches that one observation. The app values are calculated
  estimates, not direct measurements.
- The test frame uses those five correlated body bytes, with a **synthetic**
  header and checksum. It is not a complete recorded frame. Other test values
  are synthetic too. No other cycle or model is validated by this evidence.
  No device access was performed for this implementation.

The existing transport handles framing/checksums. Direct response-parser tests
use synthetic frames and do not establish transport-level checksum validation.
