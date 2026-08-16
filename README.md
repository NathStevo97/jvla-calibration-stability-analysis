# JVLA_Calibration Stability Analysis

> **Status: archived research software.** This repository is preserved as a record of a 2019 summer research internship and the analysis that contributed to EVLA Memo 208. It is not under active development, and the original calibration products are not included.

Python and CASA analysis scripts used to investigate the long-term stability of calibration solutions from the Karl G. Jansky Very Large Array (JVLA) at L-band.

The work contributed to:

**Christopher A. Hales & Nathan Stephenson (2019), _JVLA calibration stability at L-band over 5.5 years_, EVLA Memo No. 208.**

- Memo index: <https://library.nrao.edu/evla.shtml>
- Memo PDF: <https://library.nrao.edu/public/memos/evla/EVLAM_208.pdf>

## Scientific context

The analysis used observations from the **CHILES Continuum Polarization (CCP)** survey. The final memo describes 210 observations obtained over five JVLA B-configuration semesters between October 2013 and April 2019.

All observations used the same L-band correlator setup with four spectral windows (SPWs), each containing 64 channels:

| SPW | Channel 0 frequency | Channels | Bandwidth |
| --- | ---: | ---: | ---: |
| 0 | 1000 MHz | 64 | 128 MHz |
| 1 | 1384 MHz | 64 | 128 MHz |
| 2 | 1640 MHz | 64 | 128 MHz |
| 3 | 1768 MHz | 64 | 128 MHz |

The observations recorded all four correlation products (RR, RL, LR and LL). The scripts in this repository primarily work with the resulting right (R) and left (L) polarization calibration solutions.

The CCP calibration pipeline was run twice for each observation:

1. an initial calibration without ionospheric Total Electron Content (TEC) information; and
2. a later calibration incorporating GPS-derived TEC corrections, including ionospheric Faraday rotation.

This is the origin of the parallel non-TEC and `_Tec`/`_tec` analysis paths in the repository.

## What this repository analyses

The surviving source code covers two calibration products:

- **Bandpass amplitude** — complex bandpass calibration (`.B`) solutions, converted to amplitudes and normalised independently for each antenna, SPW, polarization and observation.
- **Polarization leakage** — leakage/D-term calibration (`.D`) solutions, converted to amplitudes and compared over time.

Both analyses have non-TEC and TEC-corrected variants.

The `Plots/Flux_Density/` directory also preserves flux-density-related output figures, but the source script that produced those plots is not present in this repository snapshot.

### Bandpass normalisation

For each bandpass spectrum, the original scripts normalise the 64-channel amplitude spectrum to the median of unflagged channels in the central portion of the SPW. The code uses Python slice-equivalent indices `20:42` (22 channels). **This differs from the final memo, which states that the central 42 channels were used.** The discrepancy is retained as historical evidence rather than changed without the original data to validate the effect.

## Terminology

**JVLA** — Karl G. Jansky Very Large Array.

**CCP** — CHILES Continuum Polarization survey.

**SPW / SpW** — spectral window: one of the four 128 MHz frequency ranges used by the observations.

**TEC** — Total Electron Content. In this project, `_tec` denotes results produced from the second calibration run that incorporated GPS-derived ionospheric TEC corrections.

**Bandpass** — the frequency-dependent complex instrumental response. These scripts analyse the amplitude of the CASA bandpass calibration solutions.

**Leakage / D-term** — instrumental polarization leakage: imperfect separation of orthogonal polarization responses. CASA stores the relevant complex leakage solutions in calibration tables conventionally associated here with the `.D` suffix.

**R / L** — right- and left-circular polarization at VLA L-band.

**Flagged data** — channels marked invalid during calibration/quality control. The scripts omit flagged samples when calculating statistics.

## Data and reproducibility

The original input data are **not included** in this archive.

The first-stage scripts expect CASA measurement sets and calibration tables with names such as:

```text
13B-266_<observation>.mms
13B-266_<observation>.B
13B-266_<observation>_tec.B
13B-266_<observation>.D
13B-266_<observation>_tec.D
```

The scripts then write intermediate comma-separated text files beneath the historical `/data/jvla/analysis*` directories.

The final memo states that the observational data were calibrated using a custom scripted pipeline in **CASA 4.7.2**, with additional custom tasks and manual quality assurance. Reproducing the exact inputs to this repository would therefore require more than simply downloading raw telescope observations.

### Important snapshot limitation

Some `masterplot.py` scripts allocate arrays for **181 observations**, whereas the final 2019 memo reports **210 observations**. This repository should therefore be treated as an internship-era analysis snapshot rather than a bit-for-bit archive of the final publication workflow.

The generated plots are retained because they provide useful historical output and context, but they have not been regenerated during the 2026 archival cleanup.

## Running the code

The full historical workflow cannot currently be run from this repository alone because:

- the original measurement sets and exported calibration products are absent;
- the extraction scripts rely on the CASA table tool (`tb`), which was provided by the historical CASA environment rather than as a normal Python dependency;
- the scripts assume a specific `/data/jvla` directory layout; and
- at least some code appears to represent an intermediate analysis state rather than the final 210-observation dataset.

`numpy` and `matplotlib` are recorded in `pyproject.toml` for inspection and maintenance of the standalone numerical/plotting code. Installing those dependencies does **not** reproduce the historical CASA environment.

## Archival cleanup

The repository was revisited in 2026 to make the surviving research code easier to understand without presenting it as newly validated software. Cleanup is deliberately conservative:

- Python 2-era syntax and inconsistent indentation were corrected where necessary for source readability;
- formatting and comments were improved;
- scientific terminology and the historical data flow were documented;
- known discrepancies with the final memo were recorded rather than silently altered; and
- behaviour-changing scientific refactors were avoided because the original inputs are unavailable for validation.

See [`ARCHIVE_NOTES.md`](ARCHIVE_NOTES.md) for a code-level audit and known limitations.

## Acknowledgements

Special thanks to Christopher A. Hales for the internship opportunity, scientific supervision and support during development and analysis.
