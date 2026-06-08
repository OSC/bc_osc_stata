# Batch Connect - OSC Stata 

![GitHub Release](https://img.shields.io/github/release/osc/bc_osc_stata.svg)
[![GitHub License](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/licenses/MIT)

## Overview

An [Open OnDemand](https://openondemand.org/) Batch Connect app that launches
a [Stata](https://www.stata.com/) GUI (`xstata-mp`) in an XFCE desktop session
on the OSC Cardinal cluster. Stata is a statistical software suite for data
management, analysis, and graphics.

This app uses the Batch Connect `vnc` template with Slurm.

- **Upstream project:** [Stata](https://www.stata.com/)
- **Batch Connect template:** `vnc`

## Screenshots

![Stata running in browser](docs/bc_osc_stata.png)

## Features

- Launches Stata multiprocessor GUI (`xstata-mp`) in an XFCE VNC desktop
  session
- Configurable cores, wall time, and node type (any, hugemem) via the launch
  form
- Slurm license reservation (`stata@osc`) handled automatically
- Hugemem node support with parallel partition for multi-node jobs
- Wall time limited to 1--8 hours via form constraints
- Configurable VNC resolution

## Requirements

### Compute Node Software


This Batch Connect app requires the following software be installed on the
**compute nodes** that the batch job is intended to run on (**NOT** the
OnDemand node):

- [Stata](https://www.stata.com/) 15+
- [Xfce Desktop](https://xfce.org/) 4+

For VNC server support:

- [TurboVNC](http://www.turbovnc.org/) 2.1+
- [websockify](https://github.com/novnc/websockify) 0.8.0+

### Open OnDemand

- Tested to work with the latest version of Open OnDemand
- Scheduler: Slurm

### Optional

- [Lmod](https://tacc.uctexas.edu/research-development/tacc-projects/lmod) 6.0.1+ or any other `module purge` and `module load <modules>` based CLI used to load appropriate environments within the batch job


## App Installation

### 1. Clone the repository
```sh
cd /var/www/ood/apps/sys
git clone https://github.com/OSC/bc_osc_stata.git
cd bc_osc_stata

# Pin to a release (recommended)
git checkout v0.9.1
```

No restart is needed -- Batch Connect apps are not Passenger apps and are
detected automatically.

### 2. Configure for your site

Edit `form.yml` and update these values for your cluster:

| Attribute         | OSC Default        | Change to                        |
|-------------------|--------------------|----------------------------------|
| `cluster`         | `cardinal`         | Your cluster name(s)             |
| `stata_version`   | `stata/18`         | Stata module available on your system |
| `node_type`       | `any`, `hugemem`   | Node types available on your cluster |
| `num_cores.max`   | `96`               | Max cores on your compute nodes  |
| `bc_num_hours.max`| `8`                | Max wall time for your site      |

In `script.sh.erb`, the app loads modules with:
```
module load stata/18
```
Ensure an equivalent `stata/` module is available on your system.

In `submit.yml.erb`, the app requests a Slurm license with
`--licenses stata@osc`. Update this to match your license server.

### To Update the App

```sh
cd /var/www/ood/apps/sys/bc_osc_stata
git fetch
git checkout <tag>
```

No restart is needed.

## Configuration

### form.yml attributes

| Attribute         | Widget       | Description                                              | Default |
|-------------------|--------------|----------------------------------------------------------|---------|
| `cluster`         | select       | Target cluster ID(s)                                     | `cardinal` |
| `stata_version`   | (hidden)     | Stata module to load                                     | `stata/18` |
| `bc_num_hours`    | number       | Maximum wall time (hours)                                | 1--8 |
| `bc_num_slots`    | (hidden)     | Number of nodes                                          | `1` |
| `num_cores`       | number_field | Number of CPU cores (1--96)                              | `1` |
| `node_type`       | select       | Compute node type (any, hugemem)                         | `any` |
| `bc_vnc_resolution` | text       | VNC session resolution                                   | (required) |

### Environment Variables

| Variable | Required | Description |
|---------|---------|----------|
| STATA_HOME | Yes | Path to Stata installation root |

## Troubleshooting

### Job starts but app doesn't appear (Batch Connect)

1. Check the job's `output.log` in `~/ondemand/data/sys/YOUR-APP/`
2. Verify the module loads correctly: `module load software/1.0`
3. Verify the window manager is installed: `which xfwm4`

### Connection timeout

The app may need more time to start. Increase the connection timeout or check that the compute node can open the required port.

## Testing

| Site                      | OOD Version    | Scheduler | Status     |
|---------------------------|----------------|-----------|------------|
| Ohio Supercomputer Center | 4.2.2 | Slurm     | Production |

To verify your installation:

1. Launch the app from the OOD dashboard with default settings
2. Confirm the application loads in the browser

## Known Limitations

- No GPU support

## Contributing

Contributions are welcome. To contribute:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/my-improvement`)
3. Submit a pull request with a description of your changes

For bugs or feature requests, [open an issue](https://github.com/OSC/bc_osc_stata/issues).

This app is part of the [OOD Appverse](https://ondemand.connectci.org/affinity-groups/ood-appverse). Join the [Appverse Affinity Group](https://ondemand.connectci.org/affinity-groups/ood-appverse) to connect with other contributors.

## References

- [Stata](https://www.stata.com) - The application launched by this OOD app
- [Open OnDemand](https://openondemand.org/) — the HPC portal framework

## License

* Documentation, website content, and logo is licensed under
  [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)
* Code is licensed under MIT (see LICENSE.txt)o
* The pictures, icons, and logos encoded in gif files and other formats are either copyrights and/or trademarks of StataCorp LLC.

## Acknowledgments

This app is built on [Open OnDemand](https://openondemand.org/), developed and
maintained by the [Ohio Supercomputer Center (OSC)](https://www.osc.edu/).

Open OnDemand is supported by the National Science Foundation under awards
[NSF SI2-SSE-1534949](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1534949)
and [NSF CSSI-Frameworks-1835725](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1835725).
