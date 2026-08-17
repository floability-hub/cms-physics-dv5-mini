# CMS Physics DV5 Mini

## Overview

This Floability backpack demonstrates a small distributed CMS physics analysis
using Coffea, Dask, and TaskVine. The notebook processes a miniature set of CMS
NanoAOD ROOT files, applies event and jet selections, computes energy
correlation functions with FastJet, and schedules the work through DaskVine.
Floability stages the example input data from the public `floability` S3 bucket
described in `data/data.yml` before the notebook runs.

## Install Floability

Install and activate Floability by following the
[official installation instructions](https://floability.readthedocs.io/en/stable/getting-started/installation/).
Verify the installation before running the backpack:

```bash
floability --version
```

## Run the Backpack

Run these commands from the repository root. Floability will prepare the
software environment and data, launch workers, and start JupyterLab. Open the
URL printed in the terminal and run all notebook cells.

### Local workers

Omit `--batch-type` to run workers directly on the current machine:

```bash
floability run --backpack .
```

### HTCondor workers

```bash
floability run --backpack . --batch-type condor
```

### Slurm workers

```bash
floability run --backpack . --batch-type slurm
```

The selected batch system must be available and configured on the machine
where Floability is launched.

## Common Options

HPC home directories often have limited quotas. Use `--base-dir` to place
Floability instances, prepared software environments, packed environment
archives, logs, and the default data cache on a larger project or scratch
filesystem instead:

```bash
floability run --backpack . \
  --batch-type slurm \
  --base-dir "$SCRATCH/floability" \
  --data-cache-dir "$PROJECT/floability-data-cache" \
  --manager-ports 9123:9150 \
  --worker-transfer-ports 10000:11000
```

Replace `$SCRATCH` and `$PROJECT` with persistent, high-capacity locations
available at your site. Avoid temporary node-local storage if you want
Floability to reuse cached environments and data across runs.

Common options and their implications:

- `--base-dir PATH` changes the root used for instances, environment caches,
  logs, and the default data cache. Without it, Floability uses
  `~/floability-base-dir`.
- `--data-cache-dir PATH` moves only the data cache. Use it when instance files
  and cached datasets need to live on different filesystems.
- `--manager-ports START:END` restricts the TaskVine manager to a permitted
  port range. Set this when worker nodes can connect to the login or frontend
  node only through specific firewall-approved ports.
- `--worker-transfer-ports START:END` restricts the ports used for direct
  worker-to-worker transfers. Set this to a range on which compute nodes are
  allowed to reach one another when peer transfers are enabled.

Configure worker counts, cores, memory, disk, and other resource requirements
in `compute/compute.yml` so the backpack carries its compute specification
across sites.

## Backpack Contents

- `workflow/cms-physics-dv5-mini.ipynb` — analysis and TaskVine manager.
- `software/environment.yml` — portable software specification.
- `data/data.yml` — input sources, checksums, and staging locations.
- `compute/compute.yml` — worker count and resource requirements.

