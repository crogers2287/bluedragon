# Blue Dragon feature set

Blue Dragon is a personal comma 3X install branch built on BluePilot, SunnyPilot,
and openpilot. It keeps BluePilot's Ford-focused feature work while adding a
small local maintenance layer for safe upstream syncing and install promotion.

**Current upstream base:** BluePilot **bp-7.0** (merged on branch `bd/bp-7.0`).

## Install branch

Use this branch for the comma device:

```text
https://github.com/crogers2287/bluedragon.git
branch: comma3x
```

`bp-dev` is the integration branch. `bd/bp-7.0` holds the BluePilot 7.0 merge
with Blue Dragon patches preserved. `comma3x` is the stable install branch and
should only be promoted after the integration branch has been reviewed and
tested.

## Blue Dragon additions

- Driver monitoring quality-of-life changes cherry-picked from dragonpilot:
  calibrated phone-probability handling, a less aggressive camera-uncertain
  reset window, a low-speed exemption for always-on driver-monitor alerts, a
  one-per-drive offroad camera-uncertain alert, and nonblocking persistence of
  right-hand-drive detection from the realtime monitoring thread.
- Fork maintenance automation:
  `.github/workflows/sync-bluepilot-upstream.yaml` opens or updates a PR that
  merges `BluePilotDev/bluepilot:bp-dev` into this fork's `bp-dev`.
- Install branch promotion automation:
  `.github/workflows/promote-comma3x.yaml` manually fast-forwards `comma3x` to
  `bp-dev` after testing.
- Local graph documentation support through `graphify-out/graph.json` and
  `graphify-out/GRAPH_REPORT.md`, useful for tracing BluePilot modules before
  merging upstream changes.

## Inherited BluePilot features to preserve

These are the main BluePilot systems present in this fork and should be checked
after upstream merges.

### Ford controls and tuning

- Ford-specific parameter set in `bluepilot/params/params.json`.
- BlueCruise cluster UI toggle for supported vehicles.
- Human turn detection controls.
- Ford lane-positioning controls, including in-lane offset and laneful mode.
- Custom lateral tuning profile controls for predicted-curvature blend ratios
  and PID gain UI values.
- High/low curvature tuning parameters for Ford lateral behavior.
- Intelligent Cruise Button Management support paths for PCM cruise behavior.

### Onroad UI

- BluePilot onroad renderers under `selfdrive/ui/bp/onroad/`.
- Enhanced model rendering with wider lane lines, optional lane-line status
  colors, radar lead overlays, and radar/vision lead styling.
- Ford stock ACC radar lead overlay with configurable size.
- Confidence ball, optional onroad border, animated steering wheel, and
  pill-shaped alert rendering.
- Brake-status display that can render the speed setpoint in red while braking.
- Blindspot edge overlay and stop-indicator overlay controls.
- Smoother BluePilot torque bar renderer, including lateral-uncertainty display
  support through `controllerStateBP`.

### Hybrid and EV display

- Hybrid/EV battery status gauge with SOC, voltage, and current.
- Hybrid/EV power-flow gauge for throttle demand and regenerative braking.
- Flat and arched gauge styles.
- Configurable gauge sizing.
- `carStateBP` publishing paths for hybrid drive data.

### Portal, routes, and device UX

- BluePilot web routes portal with route browsing, video playback, route
  processing status, WebSocket updates, and HTTP polling fallback.
- Portal safety behavior that blocks modifying APIs while driving.
- Configurable portal port through `BPPortalPort`.
- Route preprocessor process wired into `system/manager/process_config.py`.
- Favorite WiFi network selection and reconnect behavior.
- Tethering and web-server controls exposed through BluePilot settings.
- Debug panels for controls, lateral, longitudinal, and other vehicle state.

### Messaging and logging

- `controllerStateBP` and `carStateBP` cereal messages published from
  `selfdrive/car/card.py`.
- BluePilot-specific publisher helpers in `bluepilot/selfdrive/car/`.
- Queue-based BluePilot logger with `FordPrefEnableDebugLogs` gating verbose
  output.
- UI debug logging through `BPUIDebugLog`.

## Upstream sync checklist

When BluePilot releases new changes:

1. Run or wait for the `Sync BluePilot upstream` workflow.
2. Review the generated PR for conflicts in:
   `selfdrive/monitoring/`, `selfdrive/ui/bp/`, `bluepilot/`,
   `selfdrive/car/`, `cereal/`, and `system/manager/process_config.py`.
3. Verify the driver-monitoring QoL changes are still present.
4. Smoke-clone with submodules:

   ```bash
   git clone --depth=1 --branch bp-dev --recurse-submodules --shallow-submodules \
     https://github.com/crogers2287/bluedragon.git bluedragon-smoke
   ```

5. Run the local build/test path appropriate for the change.
6. Promote `comma3x` only after review and testing.
