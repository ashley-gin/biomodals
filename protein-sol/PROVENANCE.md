# Protein-Sol sequence solubility prediction — vendored third-party code

**This directory is not part of biomodals and is not covered by this repository's
MIT licence.** It is upstream code from the University of Manchester, redistributed
unmodified under the GNU GPL v3. See `README_sequence_prediction.txt` for the authors'
own copyright and licence notice.

## What it is

The sequence-based solubility prediction code behind the Protein-Sol web server. The
web form at <https://protein-sol.manchester.ac.uk/sequence> accepts **one sequence at a
time**; this standalone runs a multi-FASTA in a single pass, which is the only reason
it is vendored here.

## Source

| | |
|---|---|
| Downloaded from | <https://protein-sol.manchester.ac.uk/cgi-bin/utilities/download_sequence_code.php> (the "Download Zip" button on <https://protein-sol.manchester.ac.uk/software>) |
| Date | 2026-10-09 |
| SHA-256 of the zip | `4df32c61fca53adcb2394a528babd1ad85cb5c551bf7bd1c56d134097fb2b1b8` |
| Contents | 5 perl scripts, 1 shell wrapper, 3 data tables, README. No binaries, no network calls, no installer. |
| Modifications | **None.** Files are exactly as shipped, including the authors' own example run artifacts (`run.log`, `seq_prediction.txt`, `seq_composition.txt`, `example.fasta_ORIGINAL`, `blah.txt`). |

## Verification

A fresh run on the shipped `example.fasta` reproduces the authors' own shipped
`seq_prediction.txt` byte-identically:

```bash
cp seq_prediction.txt /tmp/SHIPPED.txt
bash multiple_prediction_wrapper_export.sh example.fasta
diff <(grep '^SEQUENCE PREDICTIONS' /tmp/SHIPPED.txt) \
     <(grep '^SEQUENCE PREDICTIONS' seq_prediction.txt)   # no output
```

Note this run **modifies the working directory** — see below. Do it on a copy.

## How to run it

```bash
bash multiple_prediction_wrapper_export.sh <your.fasta>
```

Results land in `seq_prediction.txt`. The lines you want start with
`SEQUENCE PREDICTIONS,` and carry `percent-sol, scaled-sol, population-sol, pI`.
`scaled-sol` is the headline number; the population average is **0.45**, and above
that is predicted more soluble than an average soluble E. coli protein.

### Two behaviours that will bite you

1. **It overwrites its own input file in place** (`mv reformat_out $FASTA_in`, keeping
   the original as `$FASTA_in_ORIGINAL`).
2. **It reads and writes in the current working directory**, so it must be run with
   `cwd` set to the directory holding these files.

Together these mean **you must run it against a disposable copy of this directory**.
Running it here in place would overwrite the pristine vendored copy, and two runs at
once in the same directory would interleave their outputs.
`03_screen_submit.ipynb` does this via `shutil.copytree` into the run directory.

## Known upstream quirks

- `README_sequence_prediction.txt` refers to an input file `seq_reference_data_NIWA.txt`;
  the zip ships `seq_reference_data.txt`, which is the name the perl actually reads.
  Stale documentation, not a defect.
- The authors describe the release as "available 'as is' ... just a snapshot".

## Citation

Hebditch M, Carballo-Amador M A, Charonis S, Curtis R, Warwicker J.
*Protein-Sol: a web tool for predicting protein solubility from sequence.*
Bioinformatics (2017) 33:3098–3100. <https://doi.org/10.1093/bioinformatics/btx345>
