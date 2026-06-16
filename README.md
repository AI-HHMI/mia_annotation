# mia_annotation

Issue tracker and annotation progress board for active MIAAI annotation projects.

**Project board**: https://github.com/orgs/AI-HHMI/projects/1
**Datasets**: Betzig Fish mosaic · LICONN DG / Cortex / Hippocampus · Matt LICONN

---

## Workflow

### Step 1 — Crop Proposed *(Admin)*
A new crop issue is created with all available information pre-filled:
- Source image path (raw zarr on cluster)
- Crop volume / tile name
- Proposed bounding box
- Tool to use (Amira / Paintera / WebKnossos)
- Dataset, model organism, data modality, task, structure of interest

Status is set to **Crop Proposal (Pending Approval)**.

---

### Step 2 — Crop Approved *(Admin)*
Jakob reviews the proposed bounding box in WebKnossos or Neuroglancer:
1. Visually inspects the proposed region for tissue quality
2. Adjusts the bbox center / placement if needed
3. Gives approval (via Slack or GitHub comment)

Once approved, the admin:
- Confirms or updates the bbox coordinates in the issue
- Sets a WK annotation task with the bbox pre-configured
- Assigns the crop to an annotator
- Changes Status → **Crop Assigned (Approved)**

---

### Step 3 — In Annotation *(Annotator)*
When you start working on your crop:
1. **Drag your card** from `Crop Assigned (Approved)` → `In Annotation`
2. Optionally update:
   - `Completion_Pct` — your % estimate of progress
   - `Tool` — if different from what was pre-filled
3. Leave a **comment** for any notes (complex structures, questions, blockers)

---

### Step 4 — Done *(Annotator)*
When your annotation is complete:
1. Fill in `Annotation_Export_Path` — exact path where you saved the export file
   - Amira users: `\\prfs\miaai\annotations\amira\...`
   - Paintera users: `\\prfs\miaai\annotations\paintera_exports\...`
   - WebKnossos users: leave blank — WK link is already in `WK_Link` field
2. Set `Completion_Pct` → 100 (or your best estimate)
3. Leave a **comment** with any notes on data quality or anything to be aware of before ingestion
4. **Drag your card** → `Done`

---

### Step 5 — Ingested *(Admin)*
After the annotation is marked Done, the assigned admin will:
1. Inspect the annotation export
2. Run the ingestion pipeline
3. Update `Ingested_Label_Path`, `Label_Count`, `Last_Updated`
4. Change Status → `Ingested`

For LICONN: ingestion happens every Monday. Fields are updated after each export regardless of card status.

---

### Step 6 — Proofreading *(Annotator)*
For crops that require a proofreading pass after initial annotation or automated segmentation:
1. Admin sets Status → `Proofreading` and assigns a proofreader
2. Proofreader reviews and corrects segmentation in WebKnossos or Amira
3. When complete, update `Annotation_Export_Path` and drag card → `Done`

---

## Status Overview

| Status | Who acts | Meaning |
|---|---|---|
| `Crop Proposal (Pending Approval)` | Admin creates, Jakob approves | Bbox proposed — awaiting Jakob sign-off on placement |
| `Crop Assigned (Approved)` | Admin sets after approval | Jakob approved bbox — assigned to annotator, WK task ready |
| `In Annotation` | Annotator sets | Actively being annotated |
| `Done` | Annotator sets | Annotation complete — awaiting ingestion |
| `Ingested` | Admin sets | Ingested into lmd-v0.0.1 |
| `Proofreading` | Admin sets | Undergoing proofreading pass |
| `In Training` | Admin sets | Data actively used in a training run |

---

## Field Reference

| Field | Who fills it | Meaning |
|---|---|---|
| `Status` | Both | Workflow stage (see above) |
| `Dataset` | Admin | Betzig Fish · MammalianLICONN · FlyLICONN · MICrONS |
| `Model_Organism` | Admin | Zebrafish · Mouse · Drosophila · C. elegans · Human |
| `Data_Modality` | Admin | Imaging modality + instrument (see options below) |
| `Task` | Admin | Annotation task type (see options below) |
| `Structure_of_Interest` | Admin | Biological structure being annotated (see options below) |
| `Tool` | Annotator | Amira / Paintera / WebKnossos |
| `Annotator` | Admin | Annotator full name |
| `Timepoint` | Admin | e.g. t=0, t=9; N/A for LICONN |
| `Source_Image_Path` | Admin | Raw image zarr on cluster |
| `Annotation_Export_Path` | Annotator | Export file path (fill in at Step 4) |
| `Ingested_Label_Path` | Admin | Final label path in lmd-v0.0.1 |
| `Label_Count` | Admin | Verified label count after ingestion |
| `Last_Updated` | Admin | Date of last ingestion |
| `Completion_Pct` | Annotator | % estimate of annotation completeness |
| `WK_Link` | Admin | WebKnossos dataset view URL (LICONN only) |
| `WK_Ann_Link` | Admin | WebKnossos annotation URL with bbox overlay |
| `Bbox` | Admin | Crop bbox range e.g. "X:931-1645, Y:900-1614, Z:171-431" |
| `Bbox_size` | Admin | Crop bbox voxel + physical size e.g. "714×714×260 vox (77×77×78 μm)" |
| `Labels` | Both | Multi-select tags — see label guide below |

---

## Field Options

### Data_Modality
| Option | Description |
|---|---|
| `Light-sheet (LLSM)` | Lattice Light Sheet Microscopy — Betzig Fish data |
| `Light-sheet (diSPIM)` | Dual-view inverted SPIM — Hari Shroff lab data |
| `Expansion Microscopy (Spinning Disk)` | ExM sample imaged on spinning disk confocal — LICONN data |
| `Expansion Microscopy (Mirror)` | ExM sample imaged on mirror microscope |
| `Expansion Microscopy (Widefield)` | ExM sample imaged on widefield microscope |
| `ATUM-mSEM` | Automated Tape-collecting Ultramicrotome + multibeam SEM |
| `FIB-SEM` | Focused Ion Beam Scanning Electron Microscopy |
| `ssTEM (Multibeam SEM)` | Serial Section TEM with multibeam SEM — MICrONS data |
| `IBEAM-mSEM` | Ion Beam multibeam SEM |
| `Confocal (CLSM)` | Confocal Laser Scanning Microscopy |

### Task
| Option | Description |
|---|---|
| `dense annotation` | Annotating every structure in the volume |
| `sparse annotation` | Annotating a selected subset of structures |
| `dense proofreading` | Proofreading every structure in the volume |
| `sparse proofreading` | Proofreading a selected subset of structures |

### Structure_of_Interest
`neurons` · `cells` · `mitochondria` · `synapses` · `nuclei`

---

## Labels (multi-select)

The **Labels** column is the only native multi-select field in GitHub Projects v2.
Use it to apply multiple tags per issue. Search labels by typing in the label picker.

### Task labels *(purple)*
`task: dense annotation` · `task: sparse annotation` · `task: dense proofreading` · `task: sparse proofreading`

### Structure labels *(green)*
`structure: neurons` · `structure: cells` · `structure: mitochondria` · `structure: synapses` · `structure: nuclei`

### Modality labels *(orange)*
`modality: Light-sheet (LLSM)` · `modality: Light-sheet (diSPIM)` · `modality: Expansion Microscopy (Spinning Disk)` · `modality: Expansion Microscopy (Mirror)` · `modality: Expansion Microscopy (Widefield)` · `modality: ATUM-mSEM` · `modality: FIB-SEM` · `modality: ssTEM (Multibeam SEM)` · `modality: IBEAM-mSEM` · `modality: Confocal (CLSM)`

---

## Adding a New Crop

See `Jakob/mia_annotation.md` in the MIAAI repo for field IDs, item IDs, and update scripts.
