# mia_annotation

Issue tracker and annotation progress board for the MIAAI annotation and proofreading work: one issue per crop to annotate, per prediction to proofread, and per label that was ingested.

**Project board**: https://github.com/orgs/AI-HHMI/projects/1
**Data collection**: `/groups/miaai/miaai/lmd-v0.0.1/` (datasets are requested in [mia_pretraining](https://github.com/AI-HHMI/mia_pretraining))

---

## 1. Open an issue: use a form

**New issue** offers forms, so a request carries what the automation needs. Pick one, go through the steps in order (Step 1, Step 2, ...), fill the required ones (a step marked *optional* can stay empty), and submit.

Open the list of forms: **https://github.com/AI-HHMI/mia_annotation/issues/new/choose** (or **Issues** > **New issue**). Or go straight to a form:

| Form | Direct link | Use it to |
|---|---|---|
| **Proofread a prediction** | https://github.com/AI-HHMI/mia_annotation/issues/new?template=proofread.yml | ask for an automatic segmentation to be proofread in one or more boxes. The title names the final label: `<dataset>_<crop>_manual_gt-<class>-final` |
| **Annotate a crop** | https://github.com/AI-HHMI/mia_annotation/issues/new?template=annotation.yml | ask for a crop to be annotated by hand (source, organism, expansion factor, box, who it is for) |

### How to use a form, step by step

1. Open the list of forms at https://github.com/AI-HHMI/mia_annotation/issues/new/choose (or click **Issues**, then **New issue**).
2. Pick the form from the list and click **Get started**. (A blank issue is still offered at the bottom, but the forms are preferred: the automation reads their answers as written.)
3. Read the short "How this form works" note at the top.
4. Fill the steps in order, Step 1, Step 2, and so on:
   - A step marked **(optional)** can be left empty. Every other step must be filled before you can submit.
   - The grey text in a box shows the format to copy. A dropdown is chosen from its list.
   - Keep the formats exactly (boxes as `Z[start:end], Y[start:end], X[start:end]`, links and paths, `@` before a GitHub user), because the automation reads them.
5. Click **Submit new issue**. The issue is written from your answers, under one heading per step.
6. Then use comments as before, and answer the automation when it asks a question on the issue.
7. If you made a mistake, edit the issue (**...** then **Edit**) or add a comment with the correction; do not open a second issue for the same request.

Formats to keep, because the automation reads them as written:
- boxes: `BBox 1: Z[265:532], Y[678:946], X[572:841]` (voxels), with the grid they are in on the first line, for example `Resolution is segmentation layer (9 nm iso)`; or, for an annotation, `Bbox: topLeft X=1000, Y=1000, Z=807 - size 482x482x194 vox`
- images and predictions: Fileglancer links (or cluster paths)
- people: GitHub users with `@`

**The box is part of the request.** Put it in the **Bbox** step of the annotation form, or in the **BBoxes** step of the proofread form: it is written into the issue body, and the automation reads it from there. There is no separate form for a box. If the box has to change later (or is added to an issue that is already open), write it in a comment on that issue:

```
Bbox: topLeft X=1000, Y=1000, Z=807 - size 482x482x194 vox
```

(or `Z[265:532], Y[678:946], X[572:841]` with the grid they are in). The automation answers with a Neuroglancer link of the box; reply there if it needs to change.

A new dataset or crop is requested in [mia_pretraining](https://github.com/AI-HHMI/mia_pretraining) with its own form.

## 2. What happens to an issue

1. **Box.** A request with a box is answered with a Neuroglancer link of the box on the raw image. Reply there if it needs to change. When the box is approved, the issue's `Status` becomes `Crop Approved` and the board gets `Bbox`, `Bbox_size`, `Source_Image_Path`, `Fileglancer_Path` and `Last_Updated`.
2. **Annotation or proofreading.** The work is done with the tool named in `Tool` (Amira, Paintera or WebKnossos). For a proofreading request, ask in a comment ("could you please provide @user with the Paintera formatted labels for the BBoxes?"): after the approver says yes, the Paintera files are made and the people named get a comment with their paths. The annotation team moves the card through `In Annotation` and the screening rounds (`Screening Queue`, `Initial Screen`, `Fix`, `nth Screen`), or, for proofreading, `Proofreading` and its own screening rounds.
3. **Export.** When the work is done, the annotator replies on the issue with `Exported to <folder path>` and says `final` or `snapshot`. A snapshot is a mid-way copy; a final is the finished label.
4. **Ingestion.** After the approver's yes, the label is added to the crop in the data collection, a report is posted on the issue, the approver checks it, and the label's board fields are filled. A snapshot leaves `Status` as it is. A final sets `Status` to `manual_gt_ingested` (or `proofread_ingested`, `public_gt_ingested`, `auto_pred_ingested` for the other kinds of label) and renames the issue to the label's name (`<dataset>_<crop>_<label>`).
5. **Label.** Issues of work done by the annotation team carry the label `mia ann/proof`.

If something is missing or wrong (a source that cannot be read, a box outside the image, a label name that is already taken), the automation asks on the issue and writes nothing until it has an answer. A name that is already taken is answered with `/overwrite` (replace the label) or `/rename` (use another name).

The forms do not set the board fields (`Status`, `Task`, ...): the team sets those on the board.

## 3. Status

| Status | Meaning |
|---|---|
| `Crop Pending Approval` | a box is proposed and waits for approval |
| `Crop Approved` | the box is approved; the work can start |
| `On Hold` | paused |
| `In Annotation` | being annotated by hand |
| `Screening Queue`, `Initial Screen`, `Fix`, `nth Screen` | the screening rounds of an annotation |
| `Done` | the annotation is complete and waits for ingestion |
| `pre_paintera_proofreading` | a prediction waits to be sent to Paintera |
| `Proofreading` | being proofread in Paintera |
| `Proofread Screening Queue`, `Proofread Initial Screen`, `Proofread Fix`, `Proofread nth Screen` | the screening rounds of proofreading |
| `post_paintera_proofreading` | proofreading is finished and waits for ingestion |
| `manual_gt_ingested`, `public_gt_ingested`, `proofread_ingested`, `auto_pred_ingested` | the final label is in the data collection (the word says what kind of label it is) |
| `In Training` | the data is used in a training run |

## 4. Board fields

| Field | Meaning |
|---|---|
| `Dataset` | the project the data belongs to (Betzig Fish, MammalianLICONN, FlyLICONN, MICrONS, CellMap, ...) |
| `Model_Organism` | Zebrafish, Mouse, Drosophila, C. elegans, Human, ... |
| `Task` | `dense annotation`, `sparse annotation`, `dense proofreading`, `sparse proofreading`, `assessment` |
| `Structure_of_Interest_1`, `_2` | neurons, cells, mitochondria, synapses, nuclei |
| `Tool` | Amira, Paintera or WebKnossos |
| `OG Annotator` | the annotator's full name |
| `Timepoint` | for time series, for example `t=5` |
| `Source_Image_Path` | the raw image (cluster path of the crop's `raw`) |
| `Fileglancer_Path` | the link to see the crop |
| `Bbox`, `Bbox_size` | the box in voxels, and its size in voxels and microns |
| `Proofread_Needed_Path` | the prediction to proofread |
| `Proofread_Export_Path`, `GT_Export_Path` | where the proofreader's or annotator's export is |
| `Proofread_Ingested_Path`, `GT_Ingested_Path` | where the ingested label is in the data collection |
| `Label_Class`, `Segmentation_Type`, `Provenance`, `Proofreading_Status`, `Coverage` | what the label is (see the label rules below) |
| `Label_Count` | the number of objects in the label |
| `Completion_Pct` | percent of the work done |
| `Last_Updated` | date of the last change to the label |
| `WK_Link`, `WK_Ann_Link` | WebKnossos dataset and annotation links |
| `Priority` | P1, P2, P3 |

### Label fields
- `Segmentation_Type`: `instance` (every object its own id), `semantic` (each value is a class), `point`
- `Provenance`: `manual_gt` (made by hand), `proofread`, `auto_pred` (automatic prediction), `public_gt` (public ground truth)
- `Proofreading_Status`: `unreviewed`, `partial`, `full`, `expert_reviewed`, ...
- `Coverage`: `dense_volume`, `full_volume`, `sparse_crop`, `sparse_points`

## 5. Issue labels

`mia ann/proof` (annotated or proofread by the annotation team), the tool labels `Amira`, `Paintera`, `WebKnossos`, and the dataset labels `Betzig Fish`, `LICONN Cell Bodies`, `LICONN Organelles`, `MICrONS`.
