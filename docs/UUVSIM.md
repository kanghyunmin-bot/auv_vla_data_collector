# Simulator-only integration (`uuvsim`)

This branch preserves the recorder and U0 wrappers used by the MuJoCo UUV
integration, separately from the real-data PR. It is based on the real-data fixes,
then adds ROS simulation-clock handling, recorder status, optional simulator IMU
motion input, span-400 deployment checks, and the existing experimental training
samplers. It does not contain model weights, recorded demonstrations, the simulator
GUI, physical scenes, or residual PPO training.

Use this collector branch together with the model fork's `uuvsim` branch. Its
`examples/uuvsim/kmu26_auv_vla_policy` is the standalone ROS adapter package; build
it with the collector in a ROS workspace. The copied source provenance is recorded
in `UUVSIM_SNAPSHOT.json`. Existing local simulator files have not been changed.

`config/collector.yaml` retains the conservative recorder defaults.
`config/collector_sim.yaml` is an explicit simulator profile (span 400); launch it
with `use_sim_time:=true`. Complete session/provenance settings before recording a
task demonstration. The separate IMU-motion input may only be enabled for the
simulator's documented `fcu_link` FLU stream.

In the model Python environment, with this collector package importable:

```bash
python tools/finetune_transfer.py /path/to/auv_vla \
  --dataset-path /path/to/reviewed-sim-train --output-dir /path/to/sim-model
python tools/serve_transfer.py /path/to/auv_vla /path/to/sim-model --check_only
```

These wrappers use the external `Kmu26TransferDataConfig` and their own stored
span-400 settings. They are not the `kmu26_auv_real_v2` real-vehicle profile. Do not
mix the settings or checkpoints. Preserve original demonstrations and review any
experimental sampling annotations; this branch makes no task-success claim.
