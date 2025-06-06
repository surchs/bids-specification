# Data summary files

## Participants file

Template:

```Text
participants.tsv
participants.json
```

<!-- This block generates a description.
A guide for using macros can be found at
 https://github.com/bids-standard/bids-specification/blob/master/macros_doc.md
-->
{{ MACROS___render_text("objects.files.participants.description") }}

We RECOMMEND to make use of these columns, and
in case that you do use them, we RECOMMEND to use the following values
for them:

<!-- This block generates a columns table.
The definitions of these fields can be found in
  src/schema/rules/tabular_data/*.yaml
and a guide for using macros can be found at
 https://github.com/bids-standard/bids-specification/blob/master/macros_doc.md
-->
{{ MACROS___make_columns_table("modality_agnostic.Participants") }}

Throughout BIDS you can indicate missing values with `n/a` (for "not
available").

`participants.tsv` example:

```tsv
participant_id	age	sex	handedness	group
sub-01	34	M	right	read
sub-02	12	F	right	write
sub-03	33	F	n/a	read
```

It is RECOMMENDED to accompany each `participants.tsv` file with a sidecar
`participants.json` file to describe the TSV column names and properties of their values (see also
the [section on tabular files](../common-principles.md#tabular-files)).
Such sidecar files are needed to interpret the data, especially so when
optional columns are defined beyond `age`, `sex`, `handedness`, `species`, `strain`,
and `strain_rrid`, such as `group` in this example, or when a different
age unit is needed (for example, gestational weeks).
If no `units` is provided for age, it will be assumed to be in years relative
to date of birth.

`participants.json` example:

```JSON
{
    "age": {
        "Description": "age of the participant",
        "Units": "year"
    },
    "sex": {
        "Description": "sex of the participant as reported by the participant",
        "Levels": {
            "M": "male",
            "F": "female"
        }
    },
    "handedness": {
        "Description": "handedness of the participant as reported by the participant",
        "Levels": {
            "left": "left",
            "right": "right"
        }
    },
    "group": {
        "Description": "experimental group the participant belonged to",
        "Levels": {
            "read": "participants who read an inspirational text before the experiment",
            "write": "participants who wrote an inspirational text before the experiment"
        }
    }
}
```

## Samples file

Template:

```Text
samples.tsv
samples.json
```

<!-- This block generates a description.
A guide for using macros can be found at
 https://github.com/bids-standard/bids-specification/blob/master/macros_doc.md
-->
{{ MACROS___render_text("objects.files.samples.description") }}

<!-- This block generates a columns table.
The definitions of these fields can be found in
  src/schema/rules/tabular_data/*.yaml
and a guide for using macros can be found at
 https://github.com/bids-standard/bids-specification/blob/master/macros_doc.md
-->
{{ MACROS___make_columns_table("modality_agnostic.Samples") }}

`samples.tsv` example:

```tsv
sample_id	participant_id	sample_type	derived_from
sample-01	sub-01	tissue	n/a
sample-02	sub-01	tissue	sample-01
sample-03	sub-01	tissue	sample-01
sample-04	sub-02	tissue	n/a
sample-05	sub-02	tissue	n/a
```

It is RECOMMENDED to accompany each `samples.tsv` file with a sidecar
`samples.json` file to describe the TSV column names and properties of their values
(see also the [section on tabular files](../common-principles.md#tabular-files)).

`samples.json` example:

```JSON
{
    "sample_type": {
        "Description": "type of sample from ENCODE Biosample Type (https://www.encodeproject.org/profiles/biosample_type)",
    },
    "derived_from": {
        "Description": "sample_id from which the sample is derived"
    }
}
```

## Demographics file

Template:

```Text
phenotype/
    demographics.tsv
    demographics.json
```

The demographics file is an OPTIONAL tabular phenotypic file in
the `phenotype/` directory meant to house common subject demographics.
For example demographics like age, gender, race, and household income.
A demographics file is RECOMMENDED to use when any participant has
more than one session of any type.
It does not replace the participants file, which is meant for unchanging data about
each participant in the data set. It is instead a superset of the participants file,
centralizing demographics across as many sessions as are available.

!!! success "Guideline 5"

    For [best tabular phenotypic data](../appendices/phenotype.md):
    Some studies collect demographics into their own
    tabular phenotypic data file already. In these cases, it is RECOMMENDED
    to house this data also in the demographics file.

!!! success "Guideline 6"

    For [best tabular phenotypic data](../appendices/phenotype.md):
    It is RECOMMENDED to use the `age` column to record participant age
    at every session in longitudinal or multi-session data sets.
    This reduces data duplication across tabular data files. The `Units` of `age`
    do not have to be years so long as the units of the age
    are written in `phenotype/demographics.json`.
    Consider participant privacy or study objectives when selecting
    the `Units` of `age` or the accuracy of `age` data.

## Scans file

Template:

```Text
sub-<label>/
    [ses-<label>/]
        sub-<label>[_ses-<label>]_scans.tsv
        sub-<label>[_ses-<label>]_scans.json
```

Optional: Yes

The purpose of this file is to describe timing and other properties of each recording *file* within one session.
In general, each of these files SHOULD be described by exactly one row.

For *file formats* that are based on several files of different extensions,
or a directory of files with different extensions (multi-file file formats),
only that file SHOULD be listed that would also be passed to analysis software for reading the data.
For example for BrainVision data (`.vhdr`, `.vmrk`, `.eeg`),
only the `.vhdr` SHOULD be listed;
for EEGLAB data (`.set`, `.fdt`),
only the `.set` file SHOULD be listed;
and for CTF data (`.ds`),
the whole `.ds` directory SHOULD be listed,
and not the individual files in that directory.

Some neural recordings consist of multiple parts,
that span several files,
but that share the same extension.
For example in [entity-linked file collections](../common-principles.md#entity-linked-file-collections),
commonly used for qMRI,
where recordings may be linked through entities such as `echo` and `part`
(using `.nii` or `.nii.gz` extensions).
For another example consider the case of large files in `.fif` format that are linked through the `split` entity
(see [Split files](../appendices/meg-file-formats.md#split-files)).
Such recordings MUST be documented with one row per file
(unlike the case of multi-file file formats described above).

<!-- This block generates a columns table.
The definitions of these fields can be found in
  src/schema/rules/tabular_data/*.yaml
and a guide for using macros can be found at
 https://github.com/bids-standard/bids-specification/blob/master/macros_doc.md
-->
{{ MACROS___make_columns_table("modality_agnostic.Scans") }}

Additional fields can include external behavioral measures relevant to the
scan.
For example vigilance questionnaire score administered after a resting
state scan.
All such included additional fields SHOULD be documented in an accompanying
`_scans.json` file that describes these fields in detail
(see [Tabular files](../common-principles.md#tabular-files)).

Example `_scans.tsv`:

```tsv
filename	acq_time
func/sub-control01_task-nback_bold.nii.gz	1877-06-15T13:45:30
func/sub-control01_task-motor_bold.nii.gz	1877-06-15T13:55:33
meg/sub-control01_task-rest_split-01_meg.nii.gz	1877-06-15T12:15:27
meg/sub-control01_task-rest_split-02_meg.nii.gz	1877-06-15T12:15:27
```

## Sessions file

Template A (segregated sessions files):

```Text
[sessions.json]
sub-<label>/
    sub-<label>_sessions.tsv
```

Optional: Yes

In case of multiple sessions there is an option of adding additional
`sessions.tsv` files describing each session and variables changing between sessions.
In such case one file per participant SHOULD be added.
These files MUST include a `session_id` column and describe each session by one and only one row.
Column names in `sessions.tsv` files MUST be different from group level participant key column names in the
[`participants.tsv` file](#participants-file).

<!-- This block generates a columns table.
The definitions of these fields can be found in
  src/schema/rules/tabular_data/*.yaml
and a guide for using macros can be found at
 https://github.com/bids-standard/bids-specification/blob/master/macros_doc.md
-->
{{ MACROS___make_columns_table("modality_agnostic.Sessions") }}

`sub-<label>/sub-<label>_sessions.tsv` example:

```tsv
session_id	acq_time	systolic_blood_pressure
ses-predrug	2009-06-15T13:45:30	120
ses-postdrug	2009-06-16T13:45:30	100
ses-followup	2009-06-17T13:45:30	110
```

Template B (aggregated sessions file):

```Text
sessions.tsv
sessions.json
```

Optional: Yes

An aggregated sessions file CAN be provided at the dataset root.
If a root-level sessions file is provided, then it MUST begin with
a `participant_id` column followed immediately after by a `session_id` column.
The intent of this root-level sessions file is to describe the sessions
in a data set and non-demographic variables changing between sessions.
Participant's demographic variables should be added to
a [demographics file](#demographics-file), as described above.

`sessions.tsv` example:

```tsv
participant_id	session_id	acq_time	systolic_blood_pressure
sub-01	ses-predrug	2009-06-15T13:45:30	120
sub-01	ses-postdrug	2009-06-16T13:45:30	100
sub-01	ses-followup	2009-06-17T13:45:30	110
sub-02	ses-predrug	2009-06-22T12:22:05	105
sub-02	ses-postdrug	2009-06-23T12:22:05	95
sub-03	ses-postdrug	2009-06-30T14:06:40	115
sub-03	ses-followup	2009-07-01T14:06:40	120
```

!!! success "Guideline 7"

    For [best tabular phenotypic data](../appendices/phenotype.md):
    If there is more than one session for any one participant, then it is
    REQUIRED to provide a sessions file at the dataset root.
    The sessions file MUST list all sessions for all subjects
    across imaging and tabular phenotypic data.

    When a sessions file is in use, you MUST NOT provide additional sessions
    files at the participant-level which would otherwise use
    the inheritance principle. If a sessions file is provided, then
    it MUST begin with a `participant_id` column followed immediately by
    a `session_id` column. The data dictionary JSON file's `session_id` field
    MUST include `Levels` with the description of each `session_id`.

!!! success "Guideline 8"

    For [best tabular phenotypic data](../appendices/phenotype.md):
    Whenever possible, it is RECOMMENDED to also collect acquisition time
    for tabular phenotypic data and store the time of acquisition of each row
    inside a column named `acq_time` in the sessions file.
    This is consistent with how acquisition time is recorded for MRI data
    and other time-sensitive measurements (for example systolic blood pressure).

    When it is needed to preserve participant privacy, you SHOULD record
    relative acquisition times with respect to the earliest session.
    Relative session acquisition times MAY be listed as durations from
    the earliest session (baseline) in days, months, or years
    using the `acq_time` column.
