# SuperJupiter PulseMetrics

<!-- SUPERJUPITER-CONSOLIDATION-MIGRATION:START -->
## Consolidated library available

For new projects, start with **[SuperJupiter Sensor Toolkit](https://github.com/Super-Jupiter/superjupiter-sensor-toolkit)**. This standalone package and its published releases remain available. Existing installations need no change.

If migrating with PlatformIO, replace the standalone dependency with:

```ini
lib_deps = superjupiter/SuperJupiterSensorToolkit@0.1.0
```

Replace this standalone include:

```cpp
#include <SuperJupiterPulseMetrics.h>
```

with this consolidated include:

```cpp
#include <SuperJupiter/SensorToolkit/PulseMetrics.h>
```

The component namespace and behaviour are preserved from standalone **0.1.1**. If using an earlier version, review its API migration requirements first. Use one implementation of this component per application; do not mix standalone and consolidated copies.

See the [consolidated migration guide](https://github.com/Super-Jupiter/superjupiter-sensor-toolkit/blob/v0.1.0/docs/MIGRATION.md) and consolidated README for Arduino/CMake installation. **Hardware validation pending.**
<!-- SUPERJUPITER-CONSOLIDATION-MIGRATION:END -->

Pulse counts and interval-derived rates with bounce and timeout reporting.

**Version 0.1.1: evolving preview. Hardware validation pending.**

Original MIT-licensed portable C++11 by [SuperJupiter](https://www.superjupiter.net).
No mandatory service, hidden clock reads, network calls or heap allocation in the core.

## What it does

Each accepted call represents one pulse; no interrupt handler is installed.
Pulses closer than minIntervalUs are rejected, retaining the last accepted pulse.
Timestamps are uint32 microseconds with consecutive distances below 2^31; timeoutUs is below that horizon.
The reported rate is based on the last accepted interval only, not a window average. The first pulse has no rate.
poll reports stale rate as zero. Count is uint64 and fails explicitly at overflow. Caller handles interrupt synchronisation.

## Install

PlatformIO exact release (after registry publication):

```ini
lib_deps = superjupiter/SuperJupiterPulseMetrics@0.1.1
```

Arduino: use the versioned [GitHub release ZIP](https://github.com/Super-Jupiter/superjupiter-pulse-metrics/releases/tag/v0.1.1).
Library Manager registration and index propagation are separate steps; check the official index before relying on IDE availability.

## Example

```cpp
#include <SuperJupiterPulseMetrics.h>
using namespace superjupiter::pulse_metrics;
int main() {
PulseMetrics meter(Config(100,1000000,0.5));
meter.pulse(0);
Result result=meter.pulse(100000);
(void)result;
return 0;
}

```

For Arduino, open `examples/Minimal`. It uses synthetic data only.
Results are deliberately local so applications can inspect outcomes before adding I/O.

## CMake

```cmake
add_subdirectory(path/to/superjupiter-pulse-metrics)
target_link_libraries(your_app PRIVATE SuperJupiter::PulseMetrics)
```

For an installed package use `find_package(SuperJupiterPulseMetrics 0.1 CONFIG REQUIRED)`.
Top-level CMake builds tests/examples; a subproject does not enable developer tools by default.

## Documentation and verification

- [API and boundaries](https://github.com/Super-Jupiter/superjupiter-pulse-metrics/blob/v0.1.1/docs/API.md)
- [Tests](https://github.com/Super-Jupiter/superjupiter-pulse-metrics/blob/v0.1.1/tests/test.cpp)
- [Validation status](https://github.com/Super-Jupiter/superjupiter-pulse-metrics/blob/v0.1.1/docs/VALIDATION.md)
- [Shared conventions](https://github.com/Super-Jupiter/superjupiter-pulse-metrics/blob/v0.1.1/docs/CONVENTIONS.md)
- [Migration policy](https://github.com/Super-Jupiter/superjupiter-pulse-metrics/blob/v0.1.1/docs/MIGRATION.md)

```sh
cmake -S . -B build/cmake
cmake --build build/cmake
ctest --test-dir build/cmake --output-on-failure
python3 tools/check_release.py --tag v0.1.1
```

Keep raw observations and their provenance in your application. Derived results are not physical truth.
Do not use fast-math or finite-math-only compiler assumptions. AVR double may have float precision.
Software tests, board compilation and physical validation are reported separately.

## Fits the SuperJupiter measurement portfolio

Complements [SensorQuality](https://github.com/Super-Jupiter/superjupiter-sensor-quality) and
[SampleWindow](https://github.com/Super-Jupiter/superjupiter-sample-window) without a required dependency.
Each component can be used independently. Domain models, customer data and proprietary applications are excluded.

## Support and licence

MIT. Report reproducible bugs through repository issues; private security reports: info@superjupiter.net.
Include version, configuration, inputs, expected and observed behaviour. No credentials or private customer data.


## Compatibility review 0.1.1

This patch includes independent regression tests and a reviewed [interoperability contract](https://github.com/Super-Jupiter/superjupiter-pulse-metrics/blob/v0.1.1/docs/INTEROPERABILITY.md). Hardware validation pending.

- Removed unused duplicated math helpers and unnecessary includes.
