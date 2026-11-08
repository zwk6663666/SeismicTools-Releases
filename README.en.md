# Seismic Tools User Guide

[简体中文](README.md) | **English**

> Version: 0.1.0　｜　Updated: 2026-10-28

Seismic Tools is a desktop toolkit for seismic structural research. It generates design acceleration response spectra from seismic codes, computes elastic response spectra and ground motion parameters of earthquake records, scales ground motions to target amplitudes, and processes quasi-static test data (hysteresis analysis, skeleton curves, hysteresis correction and test curve processing), as well as pile foundation calculation (capacity, pile forces and spring stiffness for finite element models). This guide follows the layout of the program and describes the operation, calculation methods and output of each function.

---

## Contents

1. [Overview](#1-overview)
2. [User Interface](#2-user-interface)
3. [Data Import](#3-data-import)
4. [Response Spectrum](#4-response-spectrum)
5. [Wave Response Spectrum](#5-wave-response-spectrum)
6. [Amplitude Seismic Wave](#6-amplitude-seismic-wave)
7. [Ground Motion Params](#7-ground-motion-params)
8. [Hysteresis Analysis](#8-hysteresis-analysis)
9. [Skeleton Curve](#9-skeleton-curve)
10. [Hysteresis Correction](#10-hysteresis-correction)
11. [Test Curve Processing](#11-test-curve-processing)
12. [Pile Foundation](#12-pile-foundation)
13. [Export](#13-export)
14. [Settings](#14-settings)
15. [FAQ and Notes](#15-faq-and-notes)
16. [References](#16-references)
- [Appendix A　Units](#appendix-aunits)
- [Appendix B　Context Menus](#appendix-bcontext-menus)

---

## 1. Overview

### 1.1 Modules

The navigation sidebar on the left is divided into four groups. Pages that pass data on to each other are merged into one sidebar entry, and you switch between the steps in the step bar at the top of the page: amplitude scaling, response spectrum and ground motion parameters in dynamics, hysteresis analysis and skeleton curve in the quasi-static group, and the four pile foundation pages. There are twelve function pages in all, plus three more entries (Settings, User Guide and About).

| Group | Sidebar entry (short name) | Page (top step bar) | Main function |
| --- | --- | --- | --- |
| DYNAMICS | Response Spectrum (Code RS) | Response Spectrum | Design acceleration response spectra per GB 50111-2006, JTG/T 2231-01-2020 and GB 50011-2010 (2016 edition) |
| | Ground Motion Analysis (Waves) | ① Amplitude Seismic Wave | Scale ground motions to target peak accelerations, optionally scaling the time axis |
| | | ② Wave Response Spectrum | SA, SV, SD, PSA and PSV spectra and the tripartite spectrum of ground motions, with multiple damping ratios |
| | | ③ Ground Motion Params | 35 ground motion parameters (peak, intensity, duration, spectral intensity and frequency parameters) |
| QUASI-STATIC | Hysteresis Analysis (Hysteresis) | ① Hysteresis Analysis | Cycle splitting, characteristic values (peak load, secant stiffness, residual displacement, energy dissipation, equivalent viscous damping ratio) and loop decomposition |
| | | ② Skeleton Curve | Skeleton curve extraction; equivalent yield point (five methods), peak point, ultimate point and ductility factor |
| | Curve Processing (Processing) | Curve Processing (experimental) | Detect and remove waiting periods (flat segments) of the displacement history and compare the hysteresis loops and time histories before and after |
| | Hysteresis Correction (Correction) | Hysteresis Correction (experimental) | Correct the P-Δ effect of the vertical loading device on hysteresis loops for three loading setups and compare the characteristic values before and after |
| PILE FOUNDATION | Pile Foundation Calculation (Piles) | ① Pile Data | Enter the pile section, soil layers, pile layout, cap and loads; elevation and plan sketches; JSON project files |
| | | ② Bearing Capacity | Allowable axial capacity of single piles (eight methods), group efficiency and solid foundation check |
| | | ③ Pile Forces | m method: cap displacement, pile-top forces and the distribution of moment, shear and displacement along the pile, with the influence functions from a table or by direct calculation |
| | | ④ Spring Stiffness | Six-DOF stiffness matrix of pile groups and single piles, distributed shaft, side and tip spring stiffness |
| GENERAL | Settings (Settings) | | Global defaults of import/export folders and export formats |
| | User Guide (Guide) | | Read this guide in the built-in viewer |
| | About (About) | | Version and author information |

Pages marked with a circled number pass data on to the following ones and are listed in that order. Pages under one entry are switched with the step bar at the top of the page, and clicking the sidebar entry again returns to the step you left last (see [section 2.5](#25-workflow-bar)).

The language button at the bottom of the sidebar switches the interface between English and Chinese immediately; imported data and results are kept. The titles, axis labels and legends of figures follow the interface language and are updated immediately when the language is switched; the headers and row names of data tables follow the interface language (common abbreviations such as the ground motion parameters excepted), while exported files always use English headers.

### 1.2 Shared Data

- **Wave Response Spectrum** and **Ground Motion Params** share one list of ground motions: records imported or deleted on either page are updated on the other.
- The waves scaled on **Amplitude Seismic Wave** can be added to that shared list with **Send to Wave Response Spectrum** at the top of the page, so there is no need to export them and import them again.
- **Hysteresis Analysis** and **Skeleton Curve** share one list of hysteresis data.
- **Test Curve Processing** and **Hysteresis Correction** import one file at a time and keep their own data. Use **Send to** at the top of the page to pass the processed or corrected data on: processed data can go to Correction (replacing its data) or to Hysteresis Analysis (added to the shared list), and corrected data can go to Hysteresis Analysis.
- **Response Spectrum** manages its own data.
- **Pile Data**, **Bearing Capacity**, **Pile Forces** and **Spring Stiffness** share the pile model entered on the Pile Data page. When an input changes, the results on the other pages are cleared and must be recalculated.

### 1.3 Common Features

- Every figure can be **exported as an image by right-clicking** (PNG/JPEG/TIFF/SVG/PDF/EPS) with configurable size, resolution, title, axis labels, font size, tick direction, legend and grid.
- Every data table can be **exported by right-clicking** (CSV/TXT/custom suffix); format options are linked to the global settings.
- Pages that handle several curves provide an **Export all** button to export the tables and figures of all curves at once.
- Figures use Times New Roman; axis titles and legends are larger than tick labels.
- Formulas and symbols in the interface and in figures (such as $`\sigma_{\max}`$, $`k_h`$ and $`\bar{h}`$) are typeset in LaTeX, so subscripts, Greek letters and fractions stay readable. Units in table headers and axis titles (such as $`\mathrm{kN\cdot m/rad}`$) are typeset in upright LaTeX as well. Table headers and text that you copy or export use the plain-text form, for example σmax.

---

## 2. User Interface

### 2.1 Main Window

![Main window (Response Spectrum page)](docs/images/01_response_spectrum.png)

The main window consists of:

1. **Sidebar** (dark area on the left): logo, collapse button, navigation, Settings, User Guide and About entries, language button and version.
2. **Top bar** (white card at the top): the page name or the step bar on the left (see [section 2.5](#25-workflow-bar); hover over a step to see the page description), with "(Experimental)" after the current step on experimental pages, and the buttons of the page on the right. On the Response Spectrum page the information card on the right shows the number and name of the selected code, and clicking it opens the code parameter window.
3. **Left panel** (fixed width): imported data list, calculation options, characteristic values, etc. All pages use the same left panel width.
4. **Result area** on the right: figures and tables. Most result groups have **Figure / Table** tabs showing the figure and the corresponding data.

### 2.2 Collapsing the Sidebar

The double-arrow button (») at the top right of the sidebar collapses it. The sidebar is expanded at startup.

- **Collapsed**, the sidebar becomes a narrow rail showing an icon and a short name for each entry (see [Section 1.1](#11-modules)); group titles become dividers, and hovering over a button shows its full name.
- Click the double-arrow button (») again to expand the sidebar.
- Collapsing gives more space to the result area on small screens.

![Collapsed sidebar](docs/images/10_sidebar_collapsed.png)

### 2.3 Button Colors

Buttons with the same kind of action share the same style:

| Style | Action | Examples |
| --- | --- | --- |
| Solid blue | Calculate, execute, import | Calculate, Import, Decompose loops, Extract skeleton, Calculate yield, Confirm |
| Blue outline | Page-level export | Export all, Export selected |
| Gray outline | Local tool | Export loops (loop decomposition) |
| Red outline | Clear data | Clear |

### 2.4 Common Operations

- **Select data**: click a row in the list on the left; figures and tables on the right switch to its results.
- **Delete data**: select one or more rows in the list and choose the delete command from the context menu.
- **Clear data**: click **Clear** below the list.
- **Tooltips**: most inputs, table headers and buttons show tooltips explaining the parameter or its source.
- **Export figures/tables**: right-click a figure or a table, see [Chapter 13](#13-export).

### 2.5 Workflow Bar

Data flows from one page to the next in the dynamics pages (amplitude scaling, response spectrum, ground motion parameters), the quasi-static pages (hysteresis analysis, skeleton curve) and the four pile foundation pages. Curve processing and hysteresis correction are independent preprocessing pages with their own sidebar entries; use **Send to** at the top of those pages to pass their data on. Each of the grouped pages has a step bar at the very top (it also serves as the page title) that lists the pages of the group in order. In the sidebar each group is merged into one entry instead of listing every page. Click the entry to open the group, and click it again to return to the step you left last. The first time, a group opens at its first step:


- The current page is highlighted. Click another step to jump to it without going back to the sidebar.
- When you go to another step by clicking it or by clicking **Next**, the calculation of that page runs automatically (for example capacity, pile forces and spring stiffness, ground motion spectra, hysteresis analysis and skeleton curve). If the data are incomplete (for example nothing is imported yet) nothing is calculated and no message is shown.
- **Next** on the right goes to the following step. The pile foundation pages and the Hysteresis Analysis page share their data, so only the page changes.
- When the result of this page has to be handed over, the button reads **Send to ...**. It copies the data first and then opens the target page. If there are several destinations (the Curve Processing page), they are merged into one **Send to** menu.
- If there is nothing to send (for example no ground motion has been imported yet), a message is shown and the page does not change.
- The last step has no button on the right.
- All four pile foundation pages have an **Export calculation report...** button on the right of the top bar for exporting the report at any time (see [section 12.8](#128-calculation-report)).

---

## 3. Data Import

### 3.1 Ground Motion Files

Pages: Wave Response Spectrum, Ground Motion Params, Amplitude Seismic Wave.

Click **Import** below the list to open the import settings, then click **OK** and select one or more files.

![Ground motion import settings](docs/images/11_wave_import.png)

#### File Type

| Type | Description | Settings used |
| --- | --- | --- |
| **Time and accel** | Text with a time column and an acceleration column (default) | First line, Delimiter, Time column, Accel column, Scaling factor, Unit |
| **Singal accel** | One acceleration column (the first column if there are several); time is generated from the time step | First line, Delimiter, Time step, Scaling factor, Unit |
| **PEER** | PEER NGA database `.AT2` format | Scaling factor, Unit |

#### Settings

| Setting | Description | Default |
| --- | --- | --- |
| First line | First data line (starting from 1); earlier lines (file header) are skipped | 1 |
| Delimiter | Column delimiter: Comma (,), Tab, Whitespace (any run of spaces or tabs, for space-aligned text), Semicolon (;), Custom (enter a single character in the box on the right) | Comma |
| Scaling factor | Acceleration scale factor applied after reading | 1.00 |
| Time column | Column of time (starting from 1) | 1 |
| Accel column | Column of acceleration (starting from 1) | 2 |
| Time step | Time step $`\Delta t`$ (s) of single-column acceleration | 0.02 |
| Unit | Acceleration unit in the file: g, m/s^2, cm/s^2, mm/s^2 | g |

Accelerations are converted to g after reading ($`g = 9.80665\ \mathrm{m/s^2}`$):

```math
a = k \cdot c_{\mathrm{unit}} \cdot a_{\mathrm{file}}
```

where $`a_{\mathrm{file}}`$ is the value in the file, $`k`$ is the scaling factor and $`c_{\mathrm{unit}}`$ is the unit factor: $`1`$ for g, $`1/9.80665`$ for m/s^2, $`1/980.665`$ for cm/s^2 and $`1/9806.65`$ for mm/s^2.

#### File Examples

Time and accel (comma separated, in g):

```text
0.00,0.000000
0.01,0.000388
0.02,-0.000209
...
```

Singal accel (one value per line; set Time step to the time interval of the record):

```text
0.000000
0.000388
-0.000209
...
```

PEER (`.AT2`): the program looks for the `NPTS` and `DT` keywords in the first 4 lines and reads all values after that line as accelerations:

```text
PEER NGA STRONG MOTION DATABASE RECORD
...
ACCELERATION TIME SERIES IN UNITS OF G
NPTS=  5000, DT=   .0100 SEC
  .1234E-03  .2345E-03 ...
```

#### Notes

- Records should be uniformly sampled. Otherwise Wave Response Spectrum and Ground Motion Params resample them linearly at the median time step before calculation.
- **Points** in the list is the number of samples and **Δt (s)** is the (median) time step.
- Importing a file with the same name again replaces the existing record.
- Files that fail to load are listed with the reason after import; the other files are imported normally.

### 3.2 Hysteresis Files

Pages: Hysteresis Analysis, Skeleton Curve (shared data), and Hysteresis Correction and Test Curve Processing (see the note at the end of this section).

Click **Import** to open the **Load hysteresis file** window, confirm the settings and select files (`.csv`, `.txt` or any text file; multiple selection allowed).

![Hysteresis import settings](docs/images/12_hysteresis_import.png)

| Setting | Description | Default |
| --- | --- | --- |
| First line | First data line; leading non-numeric lines (headers) after it are skipped automatically | 1 |
| Delimiter | Comma (,), Tab, Whitespace (any whitespace), Semicolon (;), Custom | Comma |
| Displacement column | Displacement column (starting from 1) | 1 |
| Force column | Force column (starting from 1); must differ from the displacement column | 2 |
| Displacement unit | Displacement unit in the file: mm, cm, m, in | mm |
| Force unit | Force unit in the file: N, kN, kip | kN |
| Displacement factor / Force factor | Scale factors of displacement and force (negative values reverse the direction) | 1.0 |

Data requirements:

- Displacement–force data are listed in **loading order**, one point per line.
- The data should start near zero displacement with cyclic loading in both directions. Half cycles are split at zero crossings of displacement (see [Section 8.4](#84-calculation-methods)).

Data are stored and computed in mm and kN; the display units can be changed at any time on the pages (see [Section 8.2](#82-analysis-options)), and figures, tables and characteristic values are converted accordingly. After import, the display units of the page are set to the units of the imported file.

Hysteresis Correction and Test Curve Processing use the same import window, but select only one file at a time and a new file replaces the old one. A vertical force time history file for the correction has a single force column, so its import window hides the displacement column, displacement unit and displacement factor.

### 3.3 Sample Data

The installer copies the sample data to `Documents\SeismicTools\Import Data\samples`, below the default import folder, so open the `samples` folder in the import dialog to try them; they are kept when the program is uninstalled.

| File | Used for | Description |
| --- | --- | --- |
| `hysteresis_sample.csv` | Hysteresis Analysis, Skeleton Curve, Hysteresis Correction (horizontal force) | Hysteresis curve, comma separated with the header `Displacement (mm),Force (kN)`, 17 201 points; displacement-controlled cyclic loading with amplitudes of 2, 4, 6, 8, 10, 14, 18, 22, 26, 30, 35 and 40 mm, 2 cycles per level, 24 cycles in total |
| `hysteresis_waiting_sample.csv` | Test Curve Processing | The curve above with waiting segments added: a rest before the test, a stop at zero displacement at the end of every cycle, and random load holds at positive or negative peaks of some cycles; the waits last about 180–800 points and all differ, with exponential force relaxation; 36 559 points and 42 waiting segments |
| `vertical_force_displacement_sample.csv` | Hysteresis Correction (Source: Force-displacement) | Vertical force–displacement curve with the header `Displacement (mm),Vertical force (kN)`, 5 734 points; every third point of the hysteresis curve above with displacement rounded to one decimal, so the number of points and the displacement precision differ from the horizontal curve; vertical force about 500 kN |
| `vertical_force_history_sample.csv` | Hysteresis Correction (Source: Time history) | Vertical force time history, one column with the header `Vertical force (kN)`, 4 000 points, about 500 kN |
| `earthquake_wave_sample.csv` | Ground motion pages | Synthetic ground motion, Time and accel type (comma separated, no header, unit g), 4 000 points, $`\Delta t = 0.01`$ s, duration 40 s, PGA 0.30 g, broadband (a medium-stiff site) |
| `earthquake_wave_soft_site.AT2` | Ground motion pages | Synthetic ground motion, PEER type (unit g), 2 500 points, $`\Delta t = 0.02`$ s, duration 50 s, PGA 0.18 g, rich in long-period content (a soft site) |

Hysteresis files with a header are fine because leading non-numeric lines are skipped automatically; ground motion files need First line to skip a header, so the two ground motion samples have no header and can be imported with the default settings.

The ground motion samples are synthesized from Kanai–Tajimi filtered white noise multiplied by an intensity envelope (rise, plateau, decay) and scaled to the given peak acceleration. They are **not real records** and are meant only for trying the response spectrum, ground motion parameter and amplitude scaling functions.

---

## 4. Response Spectrum

Generates design acceleration response spectra from seismic codes; several spectra can be generated for comparison.

![Response Spectrum page](docs/images/01_response_spectrum.png)

### 4.1 Supported Codes

| Code | Name |
| --- | --- |
| GB50111-2006 | Code for Seismic Design of Railway Engineering |
| JTG/T 2231-01-2020 | Specifications for Seismic Design of Highway Bridges |
| GB50011-2010 | Code for Seismic Design of Buildings (2016 edition) |

### 4.2 Procedure

1. Select the code in the drop-down list of **Reference parameters**. The information card in the top bar shows the number and name of the selected code.
2. Set the code parameters and sampling parameters in **Control parameters** (see Section 4.3). The values in **Intermediate Parameter** are updated automatically according to the code.
3. To use other intermediate values (e.g. site-specific values), edit them in Intermediate Parameter; the calculation uses the values in the input boxes.
4. Click **Calculate**. The spectrum is added to the **Response spectrum** list and shown in **Preview**.
5. Click a spectrum in the list to highlight it with a solid line in Preview (other spectra are dashed); the **Table** tab lists its period–acceleration data. The legend format is "type:peak value".

The **Types** column of the list is "intensity_characteristic period region_site class"; for GB 50011 it is "intensity_earthquake level_design group_site class", with the suffix `_Vertical` for vertical spectra. **Codes** is the code number and **Points** the number of points.

### 4.3 Parameters

#### Code Parameters

| Parameter | Options | Codes |
| --- | --- | --- |
| Fortification intensity | VI, VII, VII½, VIII, VIII½, IX. In GB 50011, VII½ and VIII½ mean 7 degrees (0.15g) and 8 degrees (0.30g) | All |
| Frequency category (earthquake level) | Design, Frequent, Rare | GB 50111, GB 50011 |
| Site category | GB 50111: Class I–IV; JTG and GB 50011: Class I0, I1, II, III, IV | All |
| Characteristic period | GB 50111: Region I/II/III; JTG: 0.35, 0.40, 0.45 s | GB 50111, JTG |
| Design group | Group 1, Group 2, Group 3. Shown instead of Characteristic period for GB 50011 | GB 50011 |
| Bridge category | Class A, B, C, D | JTG |
| Earthquake action | E1, E2 | JTG |
| Damping ratio | Default 0.05 | JTG, GB 50011 |
| Direction | horizontal, vertical | JTG, GB 50011 |
| Special | When checked, Ci of Class B bridges takes the values in parentheses of the code table (large and extra-large bridges on expressways and first-class highways) | JTG |

Defaults for GB 50011: intensity VI, frequent earthquake, site class II, group 1, damping ratio 0.05, horizontal.

#### Sampling Parameters

| Parameter | Description | Default |
| --- | --- | --- |
| Control type | Period: fixed period interval; Points: fixed number of points | Period |
| Period interval | Period interval (s), used when Control type is Period | 0.01 |
| Data points | Number of points, used when Control type is Points | 400 |
| Factors | Overall amplification factor applied to the spectrum | 1.00 |
| Time limit | Maximum period (s); the period range is 0 to Time limit | 6.00 |

#### Intermediate Parameters

The five input boxes of Intermediate Parameter show different symbols depending on the code:

| Position | GB 50111 | JTG/T 2231-01 | GB 50011 |
| --- | --- | --- | --- |
| Left column, row 1 | — (not used) | $`C_i`$ seismic importance factor | $`\gamma`$ decay exponent of the curve |
| Left column, row 2 | — (not used) | $`C_d`$ damping adjustment factor | $`\eta_1`$ slope adjustment factor of the linear descending segment |
| Left column, row 3 | — (not used) | $`C_s`$ site factor | $`\eta_2`$ damping adjustment factor |
| Right column, row 1 | $`\alpha`$ basic horizontal seismic acceleration (g) | $`A`$ design peak ground acceleration (g) | $`\alpha_{\max}`$ maximum seismic influence coefficient ($`\alpha_{v\max}`$ for vertical) |
| Right column, row 2 | $`T_g`$ characteristic period (s) | $`T_g`$ characteristic period (s) | $`T_g`$ characteristic period (s) |

### 4.4 Code Parameter Window

Click the code information card in the top bar to open the code parameter window. It has one tab per code and opens at the selected code; the left side holds the parameters and intermediate parameters, and the right side shows the corresponding clauses, formulas and tables for reference. The GB 50011 tab shows the seismic influence coefficient curve redrawn per clauses 5.1.4 and 5.1.5, the shape parameter formulas and the tables. Clicking **Confirm** switches the main page to the code of the tab, fills in the parameters and generates a spectrum immediately.

![Code parameter window](docs/images/02_code_parameters.png)

### 4.5 Formulas

#### GB 50111-2006 (Code for Seismic Design of Railway Engineering)

```math
S(T) = \lambda \cdot \alpha \cdot \beta(T)
```

```math
\beta(T) =
\begin{cases}
1.0 + 12.5T, & 0 \le T < 0.1\ \mathrm{s}
\\ 2.25, & 0.1\ \mathrm{s} \le T < T_g
\\ 2.25 \dfrac{T_g}{T}, & T_g \le T < 5T_g
\\ 0.45, & T \ge 5T_g
\end{cases}
```

where $`\lambda`$ is Factors, $`\alpha`$ is the basic horizontal seismic acceleration, $`T_g`$ is the characteristic period and $`\beta`$ is the dynamic amplification factor (code figure 7.2.3). The code specifies that the $`\beta`$ curve applies to structures with natural periods below 2 s and a damping ratio of 0.05.

#### JTG/T 2231-01-2020 (Specifications for Seismic Design of Highway Bridges)

```math
S_{\max} = 2.5 C_i C_s C_d A
```

```math
S(T) =
\begin{cases}
\lambda S_{\max} \left( 0.4 + 0.6 \dfrac{T}{T_0} \right), & 0 \le T \le T_0
\\ \lambda S_{\max}, & T_0 < T \le T_g
\\ \lambda S_{\max} \dfrac{T_g}{T}, & T > T_g
\end{cases}
\qquad T_0 = 0.1\ \mathrm{s}
```

```math
C_d = 1 + \frac{0.05 - \xi}{0.08 + 1.65\xi} \ge 0.55
```

where $`C_i`$ is the seismic importance factor, $`C_s`$ the site factor, $`C_d`$ the damping adjustment factor, $`A`$ the design peak ground acceleration and $`\xi`$ the damping ratio. For the vertical direction, $`C_s`$ and $`T_g`$ take the vertical site factor and the vertical characteristic period.

#### GB 50011-2010 (Code for Seismic Design of Buildings, 2016 edition)

Seismic influence coefficient curve (clause 5.1.5, figure 5.1.5):

```math
\alpha(T) =
\begin{cases}
\lambda \left[ 0.45 + 10 (\eta_2 - 0.45) T \right] \alpha_{\max}, & 0 \le T < 0.1\ \mathrm{s}
\\ \lambda \eta_2 \alpha_{\max}, & 0.1\ \mathrm{s} \le T \le T_g
\\ \lambda \left( \dfrac{T_g}{T} \right)^{\gamma} \eta_2 \alpha_{\max}, & T_g < T \le 5T_g
\\ \lambda \left[ \eta_2 0.2^{\gamma} - \eta_1 (T - 5T_g) \right] \alpha_{\max}, & 5T_g < T \le 6.0\ \mathrm{s}
\end{cases}
```

The shape parameters depend on the damping ratio $`\zeta`$ (equations 5.1.5-1 to 5.1.5-3); for a damping ratio of 0.05, $`\gamma = 0.9`$, $`\eta_1 = 0.02`$ and $`\eta_2 = 1.0`$:

```math
\gamma = 0.9 + \frac{0.05 - \zeta}{0.3 + 6\zeta}, \qquad
\eta_1 = 0.02 + \frac{0.05 - \zeta}{4 + 32\zeta} \ge 0, \qquad
\eta_2 = 1 + \frac{0.05 - \zeta}{0.08 + 1.6\zeta} \ge 0.55
```

Notes:

- $`\alpha_{\max}`$ is taken from the tables in Section 4.6: frequent and rare earthquakes per code table 5.1.4-1, the design earthquake per clause 3.10.3.
- $`T_g`$ depends on the design group and the site class (code table 5.1.4-2), plus 0.05 s for rare earthquakes.
- For the vertical direction the vertical coefficient is 65% of the horizontal one, $`\alpha_{v\max} = 0.65 \alpha_{\max}`$, and the characteristic period uses design group 1 (clauses 5.3.1 and 5.3.4).
- The code requires special study for buildings with periods above 6.0 s. When Time limit exceeds 6.0 s, the program extends the linear descending segment and keeps the value non-negative.

### 4.6 Built-in Parameter Tables

The program contains the following tables and fills in the intermediate parameters from them.

#### GB 50111: Basic Horizontal Seismic Acceleration α (g)

| Earthquake level | VI | VII | VII½ | VIII | VIII½ | IX |
| --- | --- | --- | --- | --- | --- | --- |
| Design | 0.05 | 0.10 | 0.15 | 0.20 | 0.30 | 0.40 |
| Frequent | 0.02 | 0.04 | 0.05 | 0.07 | 0.10 | 0.14 |
| Rare | 0.11 | 0.21 | 0.32 | 0.38 | 0.57 | 0.64 |

#### GB 50111: Characteristic Period Tg (s)

| Region | Site I | Site II | Site III | Site IV |
| --- | --- | --- | --- | --- |
| Region I | 0.25 | 0.35 | 0.45 | 0.65 |
| Region II | 0.30 | 0.40 | 0.55 | 0.75 |
| Region III | 0.35 | 0.45 | 0.65 | 0.90 |

#### JTG/T 2231-01: Design Peak Ground Acceleration A (g)

| Intensity | VI | VII | VII½ | VIII | VIII½ | IX |
| --- | --- | --- | --- | --- | --- | --- |
| A | 0.05 | 0.10 | 0.15 | 0.20 | 0.30 | 0.40 |

#### JTG/T 2231-01: Seismic Importance Factor Ci

| Bridge category | E1 | E2 |
| --- | --- | --- |
| Class A | 1.0 | 1.7 |
| Class B | 0.43 | 1.3 |
| Class B (Special checked) | 0.5 | 1.7 |
| Class C | 0.34 | 1.0 |
| Class D | 0.23 | — (no E2 check required) |

#### JTG/T 2231-01: Characteristic Period Tg (s)

| Zone characteristic period | Direction | I0 | I1 | II | III | IV |
| --- | --- | --- | --- | --- | --- | --- |
| 0.35 | Horizontal | 0.20 | 0.25 | 0.35 | 0.45 | 0.65 |
| 0.40 | Horizontal | 0.25 | 0.30 | 0.40 | 0.55 | 0.75 |
| 0.45 | Horizontal | 0.30 | 0.35 | 0.45 | 0.65 | 0.90 |
| 0.35 | Vertical | 0.15 | 0.20 | 0.25 | 0.30 | 0.55 |
| 0.40 | Vertical | 0.20 | 0.25 | 0.30 | 0.35 | 0.60 |
| 0.45 | Vertical | 0.25 | 0.30 | 0.40 | 0.50 | 0.75 |

#### JTG/T 2231-01: Site Factor Cs

| Site class | Direction | VI | VII | VII½ | VIII | VIII½ | IX |
| --- | --- | --- | --- | --- | --- | --- | --- |
| I0 | Horizontal | 0.72 | 0.74 | 0.75 | 0.76 | 0.85 | 0.90 |
| I1 | Horizontal | 0.80 | 0.82 | 0.83 | 0.85 | 0.95 | 1.00 |
| II | Horizontal | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| III | Horizontal | 1.30 | 1.25 | 1.15 | 1.00 | 1.00 | 1.00 |
| IV | Horizontal | 1.25 | 1.20 | 1.10 | 1.00 | 0.95 | 0.90 |
| I0 | Vertical | 0.6 | 0.6 | 0.6 | 0.6 | 0.6 | 0.6 |
| I1 | Vertical | 0.6 | 0.6 | 0.6 | 0.6 | 0.7 | 0.7 |
| II | Vertical | 0.6 | 0.6 | 0.6 | 0.6 | 0.7 | 0.8 |
| III | Vertical | 0.7 | 0.7 | 0.7 | 0.8 | 0.8 | 0.8 |
| IV | Vertical | 0.8 | 0.8 | 0.8 | 0.9 | 0.9 | 0.8 |

#### GB 50011: Maximum Horizontal Seismic Influence Coefficient $`\alpha_{\max}`$

| Earthquake level | VI (6 degrees) | VII (7 degrees 0.10g) | VII½ (7 degrees 0.15g) | VIII (8 degrees 0.20g) | VIII½ (8 degrees 0.30g) | IX (9 degrees) |
| --- | --- | --- | --- | --- | --- | --- |
| Frequent | 0.04 | 0.08 | 0.12 | 0.16 | 0.24 | 0.32 |
| Design | 0.12 | 0.23 | 0.34 | 0.45 | 0.68 | 0.90 |
| Rare | 0.28 | 0.50 | 0.72 | 0.90 | 1.20 | 1.40 |

Frequent and rare values follow code table 5.1.4-1, design values clause 3.10.3. Vertical values are 65% of the table values.

#### GB 50011: Characteristic Period $`T_g`$ (s)

| Design group | I0 | I1 | II | III | IV |
| --- | --- | --- | --- | --- | --- |
| Group 1 | 0.20 | 0.25 | 0.35 | 0.45 | 0.65 |
| Group 2 | 0.25 | 0.30 | 0.40 | 0.55 | 0.75 |
| Group 3 | 0.30 | 0.35 | 0.45 | 0.65 | 0.90 |

For rare earthquakes add 0.05 s; vertical spectra always use group 1.

If a combination has no value in the tables (e.g. E2 for Class D bridges), the program shows "Failed to find … value, please enter it manually" and `--` in the input box; enter the value manually before calculating.

### 4.7 List Operations and Export

| Operation | How |
| --- | --- |
| Delete spectra | Select in the list, right-click → Delete selected response spectrum |
| Export selected spectra | Right-click → Export selected response spectrum (see [Section 13.4](#134-exporting-selected-waves-and-spectra)) |
| Clear all spectra | Click **Clear** |
| Batch export | Click **Export all**: Response spectrum data (period, acceleration) and Response spectrum figure |
| Export the current figure/table | Right-click the figure or table in Preview |

---

## 5. Wave Response Spectrum

Computes linear elastic single-degree-of-freedom response spectra of ground motion records: five spectrum types for several damping ratios, plus the tripartite spectrum.

![Wave Response Spectrum page](docs/images/03_wave_spectrum.png)

### 5.1 Procedure

1. Click **Import** to import ground motions (see [Section 3.1](#31-ground-motion-files)); the records are listed in **Imported waves**.
2. Set damping ratios and the period range in **Spectrum options**.
3. Click **Calculate** to compute the spectra of **all** records (a progress dialog is shown for many records and can be cancelled).
4. Click a record in the list to view its results:
   - **Response spectrum**: switch the spectrum type with the **Spectrum** drop-down list; Figure shows the curves for each damping ratio and Table the period and spectral values.
   - **Tripartite spectrum**: Figure shows the tripartite spectrum and Table the complete data of all spectrum types and damping ratios.

### 5.2 Parameters

| Parameter | Description | Default |
| --- | --- | --- |
| Damping ratios | List of damping ratios separated by commas, spaces or semicolons; each value in [0, 1); duplicates are removed and values sorted | 0.02, 0.05, 0.10 |
| Max period | Maximum period (s), 0.1–20 | 6.00 s |
| Period interval | Period interval (s), 0.001–1 | 0.020 s |

The periods are $`0, \Delta T, 2\Delta T, \ldots, T_{\max}`$, where $`\Delta T`$ is Period interval and $`T_{\max}`$ is Max period. After changing parameters the current record is recalculated automatically if it already has results; other records need **Calculate** again (batch export recalculates automatically with the current parameters).

### 5.3 Spectrum Types

| Key | Name | Unit | Definition |
| --- | --- | --- | --- |
| SA | Spectral acceleration (absolute) | g | $`\max \lvert \ddot u(t) + \ddot u_g(t) \rvert`$ |
| SV | Spectral velocity (relative) | cm/s | $`\max \lvert \dot u(t) \rvert`$ |
| SD | Spectral displacement (relative) | cm | $`\max \lvert u(t) \rvert`$ |
| PSA | Pseudo-spectral acceleration | g | $`\omega^2 \cdot \mathrm{SD}`$ |
| PSV | Pseudo-spectral velocity | cm/s | $`\omega \cdot \mathrm{SD}`$ |

$`u(t)`$ is the displacement of the oscillator relative to the ground, $`\ddot u_g(t)`$ the ground acceleration and $`\omega = 2\pi/T`$. At $`T = 0`$ the oscillator is rigid: SA = PSA = PGA and the others are 0.

### 5.4 Tripartite Spectrum

The tripartite spectrum plots PSV against the period $`T`$ on log–log axes. Since

```math
\mathrm{PSV} = \frac{2\pi}{T} \mathrm{SD} = \frac{T}{2\pi} g \cdot \mathrm{PSA}
```

(SD in cm, PSA in g, $`g = 980.665\ \mathrm{cm/s^2}`$), two families of oblique lines are drawn: at constant SD, PSV is inversely proportional to $`T`$ (lines rising to the left); at constant PSA, PSV is proportional to $`T`$ (lines rising to the right).

Reading the chart:

- The horizontal and vertical axes are labeled in a 1-2-5 sequence with major and minor grid lines.
- Two bold oblique lines are the SD and PSA scales, labeled at each decade (e.g. 0.01, 0.1, 1, 10); the labels `SD (cm)` and `PSA (g)` next to them identify the scales.
- The SD value of a point is read along the constant-SD line (the thin oblique lines perpendicular to the bold SD scale) on the SD scale; PSA likewise.
- The legend is placed outside on the right so it does not cover the curves.

### 5.5 Calculation Method

The equation of motion of the oscillator under ground motion is

```math
\ddot u + 2\xi\omega \dot u + \omega^2 u = -\ddot u_g(t)
```

- The response is computed step by step with the exact solution for piecewise-linear excitation (Nigam & Jennings, 1969; Chopra, Dynamics of Structures, Section 5.2) for all periods and damping ratios at once. The method is exact for input varying linearly between samples and has no stability or period-elongation problems of numerical integration, so short periods are also accurate.
- After the record ends, zeros are appended for the length of Max period so that the maximum response during free vibration is included (important for long periods).

### 5.6 Export

| Method | Content |
| --- | --- |
| Export all | Spectrum data (all types): one file per record with all spectrum types and damping ratios; Response spectrum (selected type): figure of the selected type; Tripartite spectrum: figure |
| Right-click the Response spectrum figure/table | Current record, current spectrum type |
| Right-click the Tripartite spectrum figure/table | Tripartite spectrum / all spectrum data of the current record |

Header example: `Period (s), SA ξ=0.05 (g), SV ξ=0.05 (cm/s), …`.

---

## 6. Amplitude Seismic Wave

Scales ground motions to a set of target peak ground accelerations (PGA), optionally scaling the time axis.

![Amplitude Seismic Wave page](docs/images/05_amplitude.png)

### 6.1 Procedure

1. Click **Import** to import ground motions (see [Section 3.1](#31-ground-motion-files)); the records are listed in **Imported wave** (File names, Data points).
2. Click a record to show the original time history (Figure) and data (Table) in **Original seismic wave**; the red dashed line marks the time of PGA.
3. Set the target accelerations and the time factor in **Control parameters**.
4. Click **Calculate** to generate scaled waves for all imported records. The **Target Accel** list on the right of **Amplitude modulated seismic wave** lists the target accelerations; click one to show the scaled wave.
5. To go on with response spectra or ground motion parameters, click **Send to Wave Response Spectrum** at the top of the page. All scaled waves (named like `0.1g_original file name`) are added to the shared list of ground motions and the response spectrum page opens.

### 6.2 Parameters

| Parameter | Description | Default |
| --- | --- | --- |
| Start / Step / End | Target PGA sequence from Start to End with step Step (g) | 0.05 / 0.05 / 0.65 |
| Use target accels | When checked, use the list entered in Target accels instead | Unchecked |
| Target accels | Target PGA list (g) separated by commas or spaces, e.g. `0.05,0.1 0.2` | — |
| Time factor | Time scale factor applied to the time axis | 1.0000 |

### 6.3 Calculation Method

```math
k = \frac{\mathrm{PGA_{target}}}{\max \lvert a(t) \rvert}, \qquad a'(t') = k \cdot a(t), \qquad t' = \lambda_t t
```

where $`\mathrm{PGA_{target}}`$ is the target peak acceleration, $`k`$ the scale factor and $`\lambda_t`$ the time factor.

Records with a PGA of 0 cannot be scaled; the program shows a warning.

### 6.4 Export

| Method | Description |
| --- | --- |
| Export all | Batch export: Amplitude-scaled waves (scaled data, checked by default), Original waves, Amplitude-scaled wave figures, Original wave figures. Scaled waves are recalculated with the current targets before export |
| Export selected | Export all scaled waves of the current record (wave export window, see [Section 13.4](#134-exporting-selected-waves-and-spectra)) |
| Right-click the Imported wave list | Delete selected wave / Export selected wave (selected original waves) |
| Right-click the Target Accel list | Delete selected wave / Export selected wave (selected scaled waves) |
| Right-click a figure or table | Export the figure or data shown |

Scaled wave files are named "target PGA_original file name", e.g. `0.1_SYN-01.csv`.

---

## 7. Ground Motion Params

Computes 35 ground motion parameters of the records and compares them in a chart.

![Ground Motion Params page](docs/images/04_ground_motion.png)

### 7.1 Procedure

1. Import ground motions (the list is shared with Wave Response Spectrum; import on either page).
2. Set the baseline correction, the EPA factor β, the C and c of the damage index and the period range of Sa,avg in **Parameter options**.
3. Click **Calculate** to compute the parameters of all records (with a cancellable progress dialog).
4. In the **Ground motion parameters** table below, each row is a record and each column a parameter. **Click any parameter column (header or cell)** to show it in the **Parameter chart** above, or select it in the **Parameter** drop-down list. The description of the parameter is shown next to the list and in the header tooltip.
5. The chart shows the record number (Record No., same as the table row) on the horizontal axis and the parameter on the vertical axis. The record selected in the list on the left is marked with a red circle.

### 7.2 Parameter Options

| Option | Description | Default |
| --- | --- | --- |
| Baseline correction | Least-squares polynomial baseline correction of acceleration before integrating velocity and displacement: None, Mean (order 0), Linear (order 1), Quadratic (order 2) | Linear |
| EPA factor β | Spectral amplification factor for EPA, EPV and EPD, 1.0–5.0. 2.25 corresponds to $`\beta_{\max}`$ of GB 50011, 2.5 to ATC-3-06 | 2.25 |
| C multiplier | Linear scale factor $`C`$ of the damage index, 0.01–1000 | 1.00 |
| c exponent | Exponent $`c`$ of the damage index and of the number of effective cycles, 0.1–10 | 2.00 |
| Min. period (s) | Shortest period of the Sa,avg calculation, from 0.01 s, not above the longest period | 0.02 |
| Max. period (s) | Longest period of the Sa,avg calculation, not below the shortest period, up to 20 s | 2.47 |
| Period step (s) | Period step of the Sa,avg calculation, 0.01–1 s | 0.05 |

#### Output Units

The units of the parameters follow the acceleration, velocity and displacement units chosen on the Units tab of the **Settings** window. In the unit columns of the tables in Section 7.3, [A], [V] and [D] stand for these three units (defaults: g, m/s, m) and s for seconds. The program calculates in g, cm/s and cm and converts the results to the chosen units; the units in the table headers, on the chart axis and in the exported files change accordingly, and units with exponents such as that of the damage index are converted too. Values in the table are shown with 5 significant digits.

After changing the settings, click **Calculate** to apply them on this page; if you only change the settings and do not click Calculate, the page keeps the previous units.

#### Conditions of the Spectral Parameters

Spectral parameters (HSI, ASI, VSI, DSI, EPA, EPV, EPD, Tp, Sa,avg) use 5% damping. Except for Sa,avg they use periods from 0.01 to 5.00 s at 0.01 s and the trapezoidal rule for the spectral integrals; the periods of Sa,avg are given by the shortest period, longest period and step above. A number box takes effect when the input is finished. After changing the options, computed results are recalculated automatically.

### 7.3 Parameter Definitions

Notation: $`a(t)`$ acceleration, $`v(t)`$ and $`d(t)`$ velocity and displacement integrated from acceleration, $`T_{\mathrm{tot}}`$ total duration of the record, $`g = 9.80665\ \mathrm{m/s^2} = 980.665\ \mathrm{cm/s^2}`$; integrals cover the whole record.

#### Peak Parameters

| Parameter | Name | Unit | Definition |
| --- | --- | --- | --- |
| PGA | Peak ground acceleration | [A] | $`\max \lvert a(t) \rvert`$ |
| PGV | Peak ground velocity | [V] | $`\max \lvert v(t) \rvert`$ |
| PGD | Peak ground displacement | [D] | $`\max \lvert d(t) \rvert`$ |
| PGV/PGA | Velocity–acceleration ratio | s | $`\mathrm{PGV} / (g \cdot \mathrm{PGA})`$ |
| PGD/PGV | Displacement–velocity ratio | s | $`\mathrm{PGD} / \mathrm{PGV}`$ |
| SMA | Sustained maximum acceleration | [A] | 3rd highest sustained peak of $`a(t)`$ (Nuttli, 1979) |
| SMV | Sustained maximum velocity | [V] | 3rd highest sustained peak of $`v(t)`$ |
| SMD | Sustained maximum displacement | [D] | 3rd highest sustained peak of $`d(t)`$ |
| EDA | Effective design acceleration | [A] | Maximum absolute value of the acceleration after 9 Hz low-pass filtering (Benjamin & Associates, 1988) |
| EPA | Effective peak acceleration | [A] | $`\dfrac{1}{0.4\beta} \displaystyle\int_{0.1}^{0.5} \mathrm{SA}(T) \mathrm{d}T`$ |
| EPV | Effective peak velocity | [V] | $`\dfrac{1}{1.2\beta} \displaystyle\int_{0.8}^{2.0} \mathrm{PSV}(T) \mathrm{d}T`$ |
| EPD | Effective peak displacement | [D] | $`\dfrac{1}{2.5\beta} \displaystyle\int_{2.0}^{4.5} \mathrm{SD}(T) \mathrm{d}T`$ |
| A95 | A95 acceleration | [A] | Acceleration level containing 95% of the Arias intensity (Sarma & Yang, 1987) |

EPA, EPV and EPD are the mean spectral values over the period ranges divided by $`\beta`$ (5% damping). A "sustained peak" is a local peak whose absolute value exceeds all absolute values within 20 time steps before and after it. A95: sort $`\lvert a \rvert`$ in ascending order and accumulate $`a^2`$; A95 is the $`\lvert a \rvert`$ at which the sum reaches 95% of $`\sum a^2`$. The low-pass filter of EDA works in the frequency domain with the gain $`1 / [1 + (f / 9)^{8}]`$, i.e. a 4th-order Butterworth filter applied forward and backward, so there is no phase lag.

#### RMS and Intensity Parameters

| Parameter | Name | Unit | Definition |
| --- | --- | --- | --- |
| Arms | Root-mean-square acceleration | [A] | $`\sqrt{\dfrac{1}{T_{\mathrm{tot}}} \displaystyle\int a^2(t) \mathrm{d}t}`$ |
| Vrms | Root-mean-square velocity | [V] | $`\sqrt{\dfrac{1}{T_{\mathrm{tot}}} \displaystyle\int v^2(t) \mathrm{d}t}`$ |
| Drms | Root-mean-square displacement | [D] | $`\sqrt{\dfrac{1}{T_{\mathrm{tot}}} \displaystyle\int d^2(t) \mathrm{d}t}`$ |
| Ic | Characteristic intensity | [A]^1.5·s^0.5 | $`A_{\mathrm{rms}}^{1.5} \sqrt{T_{\mathrm{tot}}}`$ (Park et al., 1985) |
| Ia | Arias intensity | [V] | $`\dfrac{\pi}{2g} \displaystyle\int a^2(t) \mathrm{d}t`$, $`a`$ in $`\mathrm{m/s^2}`$ (Arias, 1970) |
| SED | Specific energy density | [V]²·s | $`\displaystyle\int v^2(t) \mathrm{d}t`$ |
| CAV | Cumulative absolute velocity | [V] | $`\displaystyle\int \lvert a(t) \rvert \mathrm{d}t`$ |
| CAV_STD | Standardized cumulative absolute velocity | [V] | $`\displaystyle\sum_{i=1}^{N} H(\mathrm{PGA}_i - 0.025\,g)\int_{i-1}^{i} \lvert a(t) \rvert \mathrm{d}t`$ (Campbell & Bozorgnia, 2011) |
| CAD | Cumulative absolute displacement | [D] | $`\displaystyle\int \lvert v(t) \rvert \mathrm{d}t`$ |
| CAI | Cumulative absolute impetus | [D]·s | $`\displaystyle\int \lvert d(t) \rvert \mathrm{d}t`$ |

CAV_STD divides the record into $`N`$ windows of 1 s (a final part shorter than 1 s counts as a window); $`\mathrm{PGA}_i`$ is the peak acceleration in window $`i`$ and $`H`$ is the Heaviside step function. A window adds its $`\int \lvert a \rvert \mathrm{d}t`$ only if its peak acceleration exceeds 0.025 g, so weak shaking is not accumulated.

#### Incremental Velocity, Cycles and Impulsivity

| Parameter | Name | Unit | Definition |
| --- | --- | --- | --- |
| MIV | Maximum incremental velocity | [V] | Maximum of the area $`\left\lvert \int a(t) \mathrm{d}t \right\rvert`$ under the acceleration curve between two consecutive zero crossings (Anderson & Bertero, 1987) |
| DI | Damage index | [A]^c | $`C \displaystyle\sum_i n_i A_i^{c}`$ (Malhotra, 2002) |
| Ne | Number of effective cycles | — | $`\displaystyle\sum_i n_i \left(\dfrac{A_i}{A_{\max}}\right)^{c}`$ (Malhotra, 2002) |
| IP | Impulsivity index | — | $`\dfrac{\sum \lvert \Delta v \rvert}{\mathrm{PGV}}`$ (Panella et al., 2017) |

MIV locates the zero crossings by linear interpolation; the parameter is discussed by Guaman (2010). DI and Ne first apply rainflow counting (ASTM E1049-85, three-point method) to the acceleration history; $`A_i`$ is the amplitude of the $`i`$-th kind of cycle (half the range, in g) and $`n_i`$ its number, 1 for a full cycle and 0.5 for a remaining half cycle, $`A_{\max}`$ is the largest cycle amplitude, and $`C`$ and $`c`$ are the C multiplier and c exponent of the parameter options; the procedure is summarized by Hancock & Bommer (2005). IP is the developed length of the velocity history divided by PGV, where the developed length is the sum of the absolute velocity differences between consecutive samples; a single pulse gives a small IP and more oscillations give a larger IP.
#### Duration

| Parameter | Name | Unit | Definition |
| --- | --- | --- | --- |
| Td | Significant duration | s | $`t_{95} - t_{5}`$ (Trifunac & Brady, 1975) |

$`t_{5}`$ and $`t_{95}`$ are the times at which the cumulative Arias intensity reaches 5% and 95% of the total.

#### Spectral Intensity (5% Damping)

| Parameter | Name | Unit | Definition |
| --- | --- | --- | --- |
| HSI | Housner spectrum intensity | [D] | $`\displaystyle\int_{0.1}^{2.5} \mathrm{PSV}(T) \mathrm{d}T`$ (Housner, 1952) |
| ASI | Acceleration spectrum intensity | [A]·s | $`\displaystyle\int_{0.1}^{0.5} \mathrm{SA}(T) \mathrm{d}T`$ (Von Thun et al., 1988) |
| VSI | Velocity spectrum intensity | [D] | $`\displaystyle\int_{0.1}^{2.5} \mathrm{SV}(T) \mathrm{d}T`$ (Von Thun et al., 1988) |
| DSI | Displacement spectrum intensity | [D]·s | $`\displaystyle\int_{2.0}^{5.0} \mathrm{SD}(T) \mathrm{d}T`$ (Bradley, 2011) |
| Sa,avg | Average spectral acceleration | [A] | $`\left[ \displaystyle\prod_{i=1}^{n} \mathrm{PSA}(T_i) \right]^{1/n}`$ (Bianchini et al., 2009) |

The integration limits are periods in seconds. Sa,avg uses the pseudo-acceleration spectrum PSA at 5% damping at the periods $`T_i = T_{\min} + k \Delta T`$ (not above $`T_{\max}`$); the three quantities are the shortest period, longest period and period step of the parameter options, and the geometric mean is taken. The defaults, 0.02–2.47 s with a step of 0.05 s, give 50 periods.

#### Frequency Parameters

| Parameter | Name | Unit | Definition |
| --- | --- | --- | --- |
| Tp | Predominant period | s | $`\arg\max\limits_{T} \mathrm{SA}(T)`$, the period of maximum SA at 5% damping |
| Tm | Mean period | s | $`\dfrac{\sum_i C_i^2 / f_i}{\sum_i C_i^2}`$, $`0.25\ \mathrm{Hz} \le f_i \le 20\ \mathrm{Hz}`$ (Rathje et al., 1998) |

$`C_i`$ is the Fourier amplitude of acceleration at frequency $`f_i`$.

### 7.4 Integration and Baseline Correction

- Velocity and displacement are integrated from acceleration with the trapezoidal rule. Before integration, a polynomial trend fitted according to Baseline correction is subtracted from the acceleration to reduce drift in PGV, PGD and related parameters.
- Baseline correction is **only used for integrating** velocity and displacement; PGA, Arms, Ia, CAV, CAV_STD, A95, EDA, MIV, DI, Ne and all spectral parameters use the original acceleration; IP uses the integrated velocity.

### 7.5 Export

Click **Export all** and choose:

| Content | Description | Default |
| --- | --- | --- |
| Parameter table (all records) | Parameters of all records, file name `ground_motion_parameters` | Checked |
| Acceleration, velocity and displacement histories | One file per record: time, corrected acceleration, velocity and displacement (all in the chosen units) | Unchecked |
| Parameter charts (one per parameter) | 35 comparison charts, file names `GM_parameter` | Checked |

All records are calculated with the current settings before export. The chart and the table can also be exported by right-clicking.

---

## 8. Hysteresis Analysis

Processes force–displacement hysteresis curves of quasi-static (low-cycle reversed) tests: automatic cycle splitting, characteristic values per cycle and for the whole curve, and decomposition into individual loops.

![Hysteresis Analysis page](docs/images/06_hysteresis.png)

### 8.1 Procedure

1. Click **Import** to import hysteresis data (see [Section 3.2](#32-hysteresis-files)); the data are listed in **Imported data** (File names, Points, Units).
2. Click a data set; **Hysteresis loops → Full curve** shows the complete curve (Figure) and the raw data (Table).
3. Set the display units, peak point definition and zero tolerance in **Analysis options**.
4. Click **Calculate** to analyze the current data:
   - **Characteristic values** on the left shows the summary;
   - in **Per-cycle results** at the bottom right, select a metric with **Metric**; Figure shows the metric versus cycle number and Table all per-cycle results;
   - the positive and negative peak load points are marked with triangles on the full curve.
5. Click a row in the Per-cycle results table to highlight that cycle in the hysteresis curve and the metric chart.
6. Click **Decompose loops** to decompose the loops (see Section 8.5).

### 8.2 Analysis Options

| Option | Description | Default |
| --- | --- | --- |
| Displacement unit | Display unit of displacement: mm, cm, m, in | Unit of the imported file |
| Force unit | Display unit of force: N, kN, kip | Unit of the imported file |
| Peak point | Peak point of each half cycle: Peak load or Peak displacement (load reversal point) | Peak load |
| Zero tolerance | Zero-crossing tolerance as a percentage of the maximum displacement amplitude; fluctuations within this band around zero are not treated as crossings | 1.0 % |

Stiffness is in "force unit/displacement unit" and loop area (energy) in "force unit·displacement unit". After changing the options, existing results of the current data are recalculated automatically.

### 8.3 Results

#### Per-cycle Results

| Column | Symbol | Meaning |
| --- | --- | --- |
| Cycle | — | Cycle number |
| Δ+, F+ | $`\Delta^{+}`$, $`F^{+}`$ | Displacement and force of the positive peak point |
| Δ−, F− | $`\Delta^{-}`$, $`F^{-}`$ | Displacement and force of the negative peak point |
| K | $`K`$ | Secant stiffness |
| Res.+, Res.− | $`\Delta_r^{+}`$, $`\Delta_r^{-}`$ | Residual displacement after unloading to zero force from the positive and negative sides |
| Res. avg | $`\bar\Delta_r`$ | Mean of the absolute positive and negative residual displacements |
| Area | $`S_{\mathrm{loop}}`$ | Loop area (dissipated energy) |
| ξeq | $`\xi_{\mathrm{eq}}`$ | Equivalent viscous damping ratio |

Metric charts: Peak load (positive and absolute negative), Residual displacement (positive, absolute negative and mean), Secant stiffness, Equivalent viscous damping ratio and Loop area.

#### Characteristic Values

| Name | Meaning |
| --- | --- |
| Number of cycles | Number of complete loading cycles |
| Peak load + / Disp. at peak load + | Maximum positive load of the whole curve and its displacement |
| Peak load − / Disp. at peak load − | Maximum negative load of the whole curve and its displacement |
| Total / Mean / Max loop area | Sum, mean and maximum of the loop areas |
| Mean / Max residual disp. | Mean and maximum of the per-cycle mean residual displacement (Res. avg) |
| Mean / Max / Min secant stiffness | Mean, maximum and minimum of the secant stiffness |
| Mean / Max equivalent damping ratio | Mean and maximum of the equivalent viscous damping ratio |

### 8.4 Calculation Methods

**Cycle splitting**: the curve is divided into positive and negative half cycles at zero crossings of displacement (with Zero tolerance); one positive and one adjacent negative half cycle form a cycle. Only complete cycles returning to zero displacement are counted; an unfinished last half cycle is ignored.

**Peak point**: the point with the largest absolute force (Peak load) or the largest absolute displacement (Peak displacement) in each half cycle. When peaks are equal, the point with the larger displacement is used, so that a load plateau does not pick a point near zero displacement.

**Secant stiffness** (JGJ/T 101-2015):

```math
K = \frac{\lvert F^{+} \rvert + \lvert F^{-} \rvert}{\lvert \Delta^{+} \rvert + \lvert \Delta^{-} \rvert}
```

where $`(\Delta^{+}, F^{+})`$ and $`(\Delta^{-}, F^{-})`$ are the positive and negative peak points of the cycle.

**Residual displacement**: unloading from the load reversal point (maximum/minimum displacement), when the force changes sign from $`F_{i-1}`$ to $`F_i`$, the displacement is interpolated linearly between the two points:

```math
\Delta_r = \Delta_{i-1} + \left( \Delta_i - \Delta_{i-1} \right) \frac{F_{i-1}}{F_{i-1} - F_i}
```

**Loop area**: the $`n`$ points of a cycle are treated as a closed polygon (shoelace formula):

```math
S_{\mathrm{loop}} = \frac{1}{2} \left\lvert \sum_{i=1}^{n} \left( \Delta_i F_{i+1} - \Delta_{i+1} F_i \right) \right\rvert, \qquad (\Delta_{n+1}, F_{n+1}) = (\Delta_1, F_1)
```

**Equivalent viscous damping ratio**:

```math
\xi_{\mathrm{eq}} = \frac{S_{\mathrm{loop}}}{2\pi \left( S_{OBE} + S_{ODF} \right)}, \qquad
S_{OBE} = \frac{1}{2} \lvert F^{+} \Delta^{+} \rvert, \qquad
S_{ODF} = \frac{1}{2} \lvert F^{-} \Delta^{-} \rvert
```

where $`S_{\mathrm{loop}}`$ is the loop area and $`S_{OBE}`$ and $`S_{ODF}`$ are the triangle areas formed by the positive and negative peak points with the origin and the horizontal axis (elastic strain energy).

### 8.5 Loop Decomposition

Click **Decompose loops** to switch to the **Loop decomposition** tab:

![Loop decomposition](docs/images/07_loops.png)

| Control | Description |
| --- | --- |
| Cycle | All cycles (subplot grid) or a single cycle |
| Shared axes | Use the same axis limits for all subplots to compare loop sizes |
| Export loops | Batch export of decomposition data and figures of all loaded curves (gray button, decomposition only) |
| Figure / Table | Decomposition figure / data of each cycle (Cycle, Point within the cycle, displacement, force) |

Right-click the decomposition figure → Save figure as image…; the **Export** option of the export window offers **Combined figure** (all cycles in one figure) or **Separate files** (one file per cycle). Right-click the decomposition table to export the selected decomposition data.

### 8.6 Export

**Export all** (blue outline, page level):

| Content | File name | Default |
| --- | --- | --- |
| Per-cycle results | `{name}_cycles` | Checked |
| Characteristic values | `{name}_summary` | Checked |
| Loop decomposition data | `{name}_loops` | Unchecked |
| Hysteresis data | `{name}_hysteresis_data` | Unchecked |
| Hysteresis curve | `{name}_hysteresis` | Checked |
| Per-cycle charts | `{name}_cycle_metrics` (five metrics in 2×3 subplots) | Checked |
| Loop decomposition | `{name}_loops` or `{name}_cycle_n` | Unchecked |

The **Loop figures** option of the batch export window selects All loops in one figure (one decomposition figure per curve) or One file per loop.

---

## 9. Skeleton Curve

Extracts the skeleton curve from the hysteresis curve and computes the peak point, ultimate point, equivalent yield points and ductility factors.

![Skeleton Curve page](docs/images/08_skeleton.png)

### 9.1 Procedure

1. Import hysteresis data (shared with Hysteresis Analysis).
2. Set the peak point definition and tolerances in **Skeleton options** and click **Extract skeleton**. The hysteresis curve is shown in a light color and the skeleton curve as a red polyline; the Table tab lists the skeleton points (Point, Branch, Displacement, Force).
3. Set the ultimate point and Park method parameters in **Yield point options** and click **Calculate yield** (the skeleton curve is extracted first if necessary). The figure marks the peak points (▲), ultimate points (✕) and the yield points of the five methods; the **Characteristic points** table lists their coordinates and ductility factors.
4. Use the **Focus** buttons to view one method: its construction lines are highlighted and other elements dimmed; **All** shows all methods. Clicking a yield point row in the table also focuses the corresponding method.

![Focus on the equivalent energy method (area filling)](docs/images/09_skeleton_focus.png)

### 9.2 Parameters

#### Skeleton Options

| Parameter | Description | Default |
| --- | --- | --- |
| Displacement unit / Force unit | Display units | Unit of the imported file |
| Peak point | Peak point of the first half cycle of each loading level: Peak load or Peak displacement | Peak load |
| Level tolerance | A half cycle starts a new loading level when its amplitude exceeds (1 + tolerance) times the previous level | 5.0 % |
| Zero tolerance | Zero-crossing tolerance (as in [Section 8.2](#82-analysis-options)) | 1.0 % |

#### Yield Point Options

| Parameter | Description | Default |
| --- | --- | --- |
| Ultimate load | Ultimate point: the point after the peak where the load drops to this ratio of the peak load Fp, 50%–100% | 85.0 % Fp |
| Graphical II load | Load ratio of point A in Graphical II (Park method), 10%–99% | 75.0 % Fp |

After changing the parameters, existing results of the current data are recalculated automatically.

### 9.3 Skeleton Extraction

1. Split the curve into half cycles at zero crossings of displacement (as in Hysteresis Analysis).
2. The amplitude $`\delta`$ of a half cycle is its maximum absolute displacement. If $`\delta \le (1 + \varepsilon_L) \delta_{\mathrm{prev}}`$, the half cycle repeats the same level and is skipped; otherwise it starts a new loading level. $`\varepsilon_L`$ is Level tolerance and $`\delta_{\mathrm{prev}}`$ the amplitude of the previous level in the same direction.
3. Take the peak point (per Peak point) of the **first half cycle** of each loading level.
4. Connect the negative points (outside in), the origin and the positive points (inside out) to form the skeleton curve.

### 9.4 Characteristic Points and Yield Point Methods

The positive and negative directions are computed separately (the negative branch is mirrored and treated as positive; the table shows negative values). Below, $`D`$ and $`F`$ are displacement and load of the skeleton curve and $`F(D)`$ is the load at displacement $`D`$ (linear interpolation between skeleton points).

**Peak point** $`(D_p, F_p)`$: the point of maximum load; for equal loads, the larger displacement.

**Ultimate point** $`(D_u, F_u)`$: the point after the peak where the load first drops to $`F_u = \eta_u F_p`$ (linear interpolation), $`\eta_u`$ being Ultimate load (default 0.85). If the load does not drop to this ratio in the test, the last skeleton point is used and marked with `*` in the table.

**Initial stiffness** $`K_0`$: slope of the first segment from the origin, $`K_0 = F_1 / D_1`$, where $`(D_1, F_1)`$ is the first skeleton point after the origin.

The yield point methods follow Feng et al. (2017):

| Method | Name in table | Focus button |
| --- | --- | --- |
| Graphical method I (general yield moment method) | Graphical I (GYMM) | Graphical I |
| Graphical method II (Park method) | Graphical II (Park) | Graphical II |
| Graphical method III (tangent method) | Graphical III (tangent) | Graphical III |
| Equivalent energy method | Equivalent energy | Equal energy |
| Farthest point method | Farthest point | Farthest point |

#### Graphical Method I (General Yield Moment Method)

The initial tangent $`F = K_0 D`$ meets the peak load line $`F = F_p`$ at A; a vertical line through A meets the curve at B; OB extended meets $`F = F_p`$ at C, whose displacement is the yield displacement:

```math
D_A = \frac{F_p}{K_0}, \qquad F_B = F(D_A), \qquad D_y = D_C = \frac{F_p}{F_B} D_A
```

#### Graphical Method II (Park Method)

Take point A on the curve at load $`\eta_P F_p`$ ($`\eta_P`$ is Graphical II load, default 0.75); OA extended meets $`F = F_p`$ at B, whose displacement is the yield displacement:

```math
F(D_A) = \eta_P F_p, \qquad D_y = D_B = \frac{D_A}{\eta_P}
```

#### Graphical Method III (Tangent Method)

The initial tangent and the tangent of the last segment before the peak meet at P, where $`(D', F')`$ is the skeleton point before the peak:

```math
K_1 = \frac{F_p - F'}{D_p - D'}, \qquad D_P = \frac{F_p - K_1 D_p}{K_0 - K_1}, \qquad F_P = K_0 D_P
```

The foot of the perpendicular from P to the curve is the yield point. The distance is measured in normalized coordinates $`(D/D_p, F/F_p)`$, so the result does not depend on units. The method requires $`K_0 > K_1`$.

#### Equivalent Energy Method

The curve is replaced by an ideal elastic–plastic bilinear curve $`O \to (D_y, F_p) \to (D_p, F_p)`$ with the same area under it (from the origin to the peak):

```math
F_p D_p - \frac{1}{2} F_p D_y = A = \int_0^{D_p} F(D) \mathrm{d}D
\qquad \Rightarrow \qquad
D_y = \frac{2 \left( F_p D_p - A \right)}{F_p}
```

In the figure, $`A_1`$ (curve above the bilinear line) is filled in cyan and $`A_2`$ (bilinear line above the curve) is hatched in orange; the two areas are equal.

#### Farthest Point Method

The point of the curve farthest from the line joining the origin and the peak is the yield point:

```math
d(D) = \frac{\lvert F_p D - D_p F(D) \rvert}{\sqrt{F_p^2 + D_p^2}}, \qquad D_y = \arg\max_{0 \le D \le D_p} d(D)
```

If the curve is nearly straight, i.e. the normalized maximum distance $`\max \lvert D/D_p - F/F_p \rvert / \sqrt{2} < 0.01`$, there is no distinct yield point.

#### Yield Load and Ductility Factor

The yield displacement of each method is limited to $`D_y \le D_p`$ and the yield load is the load on the skeleton curve $`F_y = F(D_y)`$ (the yield point lies on the curve). The ductility factor is:

```math
\mu = \frac{D_u}{D_y}
```

Columns of the Characteristic points table: Point, Δ+, F+, Δ−, F−, μ+, μ−. The rows are Peak point, Ultimate point (ratio Fp) and the yield points of the five methods (Yield – method name).

### 9.5 Export

**Export all** (all checked by default):

| Content | File name |
| --- | --- |
| Skeleton curve data | `{name}_skeleton` (point, branch, displacement, force) |
| Characteristic points | `{name}_yield_points` |
| Skeleton curve and yield points | `{name}_skeleton` (figure with all methods) |

Figures and tables can also be exported by right-clicking; figure export keeps the current focus.

---

## 10. Hysteresis Correction

> **Experimental**: the algorithm and interface of this page are still being refined; the current step in the top bar is marked "(Experimental)". Please cross-check the results by other means.

In quasi-static tests the vertical load is applied by a jack. As the column top sways, the vertical load produces a P–Δ effect: either an additional moment at the column base, or a horizontal component caused by the tilting vertical jack, so the horizontal force measured by the actuator is not the base shear of the pier. This page corrects the horizontal force point by point with the vertical force according to the loading setup used, and compares the hysteresis loops and characteristic values before and after the correction.

![Hysteresis Correction page](docs/images/17_correction.png)

### 10.1 Procedure

1. In **Input data → Horizontal force-displacement data** click **Import** to import the horizontal force–displacement hysteresis curve. Only one file is imported at a time and a new file replaces the old one; see [Section 3.2](#32-hysteresis-files) for the settings.
2. In **Vertical force data** choose the vertical force **Source**:
   - **Force-displacement**: a vertical force–displacement curve; the file has a displacement column and a vertical force column;
   - **Time history**: a vertical force time history; the file has a single vertical force column;
   - **Constant**: enter a constant vertical force (compression positive).
   - For the first two sources click **Import** on the right; each source keeps its own file, shown when the source is selected. **Use** selects how the vertical force is taken.
3. In **Parameters** set the display units, the geometry parameters $`L`$, $`L_{\mathrm{top}}`$, $`L_{\mathrm{bot}}`$ and the zero tolerance. Parameters not used by the current setup are disabled.
4. In **Loading setup** choose the loading setup: click Case 1 to Case 3 on the left; the diagram is shown on the right and the correction formula below (see [Section 10.3](#103-the-three-loading-setups-and-formulas)).
5. Click **Correct**. The upper plot overlays the Original and Corrected hysteresis loops, and **Characteristic values** below compares the values before and after. Afterwards, changing the data, parameters or setup recalculates the result automatically; with invalid parameters or missing data the result is cleared—complete the input and click **Correct** again.
6. Click **Export results** to export the data (see [Section 10.6](#106-export)), or click **Send to Hysteresis Analysis** at the top of the page to add the corrected data to the hysteresis data and continue with the hysteresis analysis and the skeleton curve.

### 10.2 Options

#### Vertical Force

| Option | Description | Default |
| --- | --- | --- |
| Source | Origin of the vertical force: Force-displacement, Time history or Constant | Force-displacement |
| Use | How the vertical force is taken: Point by point (matched to every point of the horizontal curve), Mean value (the mean as a constant), Maximum value (the value of maximum amplitude, sign kept) | Point by point |
| Constant box | Shown when Source is Constant; in the selected force unit | 0 |

A vertical force time history file has a single force column, so the **Load vertical force time history file** window hides the displacement column, displacement unit and displacement factor (other settings as in [Section 3.2](#32-hysteresis-files)).

#### Parameters

| Option | Description | Default |
| --- | --- | --- |
| Displacement unit / Force unit | Display units. Geometry parameters and the constant vertical force are entered in the display units; switching units converts the values and the physical quantities stay unchanged | Unit of the imported file |
| Loading height $`L`$ | Distance from the top of the base to the horizontal loading point | 1000 mm |
| Top length $`L_{\mathrm{top}}`$ | Distance from the horizontal loading point up to the top of the vertical jack | 300 mm |
| Bottom length $`L_{\mathrm{bot}}`$ | Distance from the top of the base down to the bottom end of the vertical jack | 300 mm |
| Zero tolerance | Zero-crossing tolerance used to split cycles when computing characteristic values (as in [Section 8.2](#82-analysis-options)) | 1.0 % |

The geometry parameters used by each setup are listed below; unused ones are disabled and ignored. Used parameters must be greater than zero, otherwise clicking **Correct** shows "Parameter … must be greater than zero!".

### 10.3 The Three Loading Setups and Formulas

![The three loading setups](docs/images/18_correction_cases.png)

In the table $`\Delta`$ is the horizontal displacement, $`P`$ the vertical force and $`F`$ the corrected base shear. $`\Delta`$, $`F_{\mathrm{eff}}`$ and $`F_{\mathrm{Rep}}`$ are signed, so both directions are treated symmetrically; $`P`$ is positive in compression. If compression is recorded as negative in your data, set **Force factor** to −1 when importing the vertical force file.

| Case | Loading setup | Measured force | Parameters used | Formula |
| --- | --- | --- | --- | --- |
| Case 1 | The vertical force acts on the column top and tilts with the sway; the horizontal force is the effective force of the device | $`F_{\mathrm{eff}}`$ | $`L`$ | $`F = F_{\mathrm{eff}} - \dfrac{P \Delta}{L}`$ |
| Case 2 | Horizontal jack; the vertical jack is fixed at a point $`L_{\mathrm{top}}`$ above the loading point and tilts with the column top | $`F_{\mathrm{Rep}}`$ | $`L_{\mathrm{top}}`$ | $`F = F_{\mathrm{Rep}} + \dfrac{P \Delta}{L_{\mathrm{top}}}`$ |
| Case 3 | Horizontal jack; the vertical force always points to the same position at the bottom of the base | $`F_{\mathrm{Rep}}`$ | $`L`$, $`L_{\mathrm{top}}`$, $`L_{\mathrm{bot}}`$ | $`F = F_{\mathrm{Rep}} - P \sin\alpha`$, $`\tan\alpha = \dfrac{\Delta (L + L_{\mathrm{top}}) / L}{L + L_{\mathrm{bot}} + L_{\mathrm{top}}}`$ |

The layout of the diagrams follows setups (a), (c) and (d) of Fig. 3.5 of Reference 19. A setup whose measured force is already the base shear (setup (b) in the figure) needs no correction and is not handled on this page. The correction of Case 2 is the horizontal component $`P \Delta / L_{\mathrm{top}}`$ produced by the tilted vertical jack (the reference prints $`P L_{\mathrm{top}} / \Delta`$, which grows without bound as the displacement approaches zero and does not agree with the geometry). In Case 3, $`L_{\mathrm{top}}`$ is the distance from the upper reference line of the vertical force line to the horizontal loading point, which the figure of the reference does not mark.

### 10.4 Fusing the Vertical Force with the Horizontal Curve

The horizontal force–displacement curve and the vertical force data may have different numbers of points and different displacement precision, so the program first aligns the vertical force to every point of the horizontal curve:

- **Force-displacement**: both curves describe the same loading process. Each is normalized to 0–1 by its cumulative displacement path $`s_i = \sum_{k \le i} \lvert \Delta_k - \Delta_{k-1} \rvert`$ divided by the total path, and the vertical force is linearly interpolated at the normalized path of every point of the horizontal curve. Points with the same normalized path are averaged; if the total path is zero, the point index is used instead.
- **Time history**: there is no common independent variable, so the records are aligned by relative index $`i/(N-1)`$, assuming that the vertical force record covers the same complete test as the horizontal curve.
- **Constant**: the same constant at every point.

**Use** is then applied: Point by point keeps the matched vertical force; Mean value and Maximum value take the mean and the value of maximum amplitude of all points as a constant.

### 10.5 Results

**Characteristic values** is a transposed table with one column per value and three rows: before (Original), after (Corrected) and the relative change (Change):

| Column | Meaning |
| --- | --- |
| Peak load + / Peak load − | Maximum positive and negative load of the whole curve |
| Mean residual / Max residual | Mean and maximum of the per-cycle mean residual displacement |
| Mean ξeq / Max ξeq | Mean and maximum of the equivalent viscous damping ratio |
| Energy dissipation | Sum of the loop areas (total dissipated energy) |
| Mean stiffness | Mean secant stiffness |

The change is $`(X_{\mathrm{corrected}} - X_{\mathrm{original}}) / \lvert X_{\mathrm{original}} \rvert \times 100\%`$. The values are computed as in [Section 8.4](#84-calculation-methods), with the Peak load definition of the peak point. **Matched vertical force** below the table gives the mean and maximum amplitude of the vertical force aligned to the horizontal curve.

### 10.6 Export

- **Export results**: opens the table export window to export, for every data point, Point (index), Displacement, Original force, Vertical force and Corrected force (in the display units). If the data have not been corrected yet, they are corrected first.
- Right-click the figure → Save figure as image… to export the hysteresis loops before and after.
- Right-click the characteristic values table → Export table data…; the exported table is identical to the one shown (one column per value).

This page has no Export all.

---

## 11. Test Curve Processing

> **Experimental**: the algorithm and interface of this page are still being refined; the current step in the top bar is marked "(Experimental)". Please confirm the detected segments one by one in the time histories.

When a quasi-static test is paused, equipment is adjusted or the load is held, the displacement stays constant and appears as flat segments in the displacement history; the force may relax meanwhile and leave vertical lines in the hysteresis loops that have nothing to do with loading. This page detects such waiting segments, lets you choose segment by segment whether to remove them, and compares the hysteresis loops and time histories before and after.

![Test Curve Processing page](docs/images/19_curve_processing.png)

### 11.1 Procedure

1. In **Input data → Hysteresis data** click **Import** to import the hysteresis curve (displacement–force; one file at a time, a new file replaces the old one; see [Section 3.2](#32-hysteresis-files) for the settings).
2. In **Waiting segment detection** set the display units, **Flatness tolerance** and **Minimum length (points)**. Detection runs again after importing data or changing these two parameters, and all detected segments are removed by default.
3. The **Waiting segments** list shows the detected segments row by row. Check **Remove** to remove a segment and uncheck it to keep it; **Remove all** and **Keep all** select all or none at once. Below the list is a summary of the detected segments, the number to be removed and the number of points.
4. Click a row of the list; that segment is marked with a darker shade in the time histories for checking.
5. The upper plot **Hysteresis loops before and after processing** overlays the loops before (Original, thicker light blue) and after (Processed, red); the vertical lines left by removed segments appear only in Original.
6. The lower plot **Time histories before and after processing** has 2×2 subplots: displacement history in the upper row and force history in the lower row; the left column Before shows the original data with the waiting segments shaded (red: to be removed, gray: kept, darker for the selected row), the right column After the processed data. The horizontal axis is the point number, renumbered from 1 after processing; both columns share the vertical range.
7. Click **Export results** to export the processed data (see [Section 11.3](#113-export-and-notes)), or use the **Send to** menu at the top of the page to pass the processed data on to Hysteresis Correction or Hysteresis Analysis and continue with the next steps.

### 11.2 Detection and Removal

Let the displacement series be $`d_1, d_2, \dots, d_N`$, the maximum displacement amplitude $`d_{\max} = \max_i \lvert d_i \rvert`$, **Flatness tolerance** $`\varepsilon`$ (a percentage of $`d_{\max}`$, default 0.2 %) and **Minimum length** $`n_{\min}`$ (default 50 points). For every window $`W`$ of $`n_{\min}`$ consecutive points, if

```math
\max_{k \in W} d_k - \min_{k \in W} d_k \le \varepsilon \, d_{\max}
```

then $`W`$ is a still window. Overlapping still windows are merged into one **waiting segment**, whose length is at least $`n_{\min}`$. The detection does not depend on the displacement level, so stops at zero displacement and load holds at peaks are both detected.

All points inside the selected waiting segments are deleted and the remaining points keep their order. The displacements at the two ends of a segment are practically equal, so the processed loops have no displacement jumps; the force relaxation during the wait (the vertical lines in the loops) is removed together with it, and there may be a jump between the two force values on either side of a removed segment.

Tuning suggestions:

- If displacement measurement noise is large and flat segments are not detected, increase Flatness tolerance;
- Under very slow loading, a slow one-directional ramp may also satisfy the tolerance and be detected by mistake; decrease Flatness tolerance or increase Minimum length, and confirm in the time histories;
- To remove only long waits and keep short pauses, increase Minimum length.

### 11.3 Export and Notes

- **Export results**: opens the table export window to export the processed data: the first column Point is the index of the point in the original data, followed by Displacement and Force (in the display units).
- Right-click the figures → Save figure as image…; right-click the waiting segment list → Export table data… to export No., Start, End, Points, Displacement (mean level) and Removed of every segment.
- The data of this page are managed independently and are not shared with the other quasi-static pages; the exported file can be imported on pages such as Hysteresis Analysis.
- The data have no time column, so the horizontal axis of the time histories is the point number.
- Sample data with waiting segments are described in [Section 3.3](#33-sample-data): with the default parameters `hysteresis_waiting_sample.csv` yields 42 waiting segments with 19 400 points in total (53.1 %).

---

## 12. Pile Foundation

The **PILE FOUNDATION** group in the sidebar supports hand checks and finite element preparation for pile foundations. It has four pages: axial capacity of single piles and pile groups, bending moment and shear along laterally loaded piles (m method), six-DOF spring stiffness for finite element pile models, and the distributed spring stiffness of the shaft, the pile sides and the pile tip. The methods follow chapter 5 (m method) and appendices A and B of the book *Bridge Piers, Abutments and Foundations* (《桥梁墩台与基础工程》). Symbols follow the book and units are kN, m and kPa.

> **Note**: The coefficient tables for capacity come from an older railway bridge foundation code (TB 10002.5-2005, 《铁路桥涵地基和基础设计规范》), so use the current code for formal design. This group is better suited to scheme comparison, teaching and preparing parameters for finite element models.

![Pile Data page](docs/images/21_pile_data.png)

### 12.1 Pages and Data Sharing

| Page | Short name | Main function |
| --- | --- | --- |
| Pile Data | Pile Data | Enter the pile section, soil layers, pile layout, cap and loads, view the elevation and plan sketches, save or open a project file, export a PDF calculation report |
| Bearing Capacity | Capacity | Eight methods for the allowable axial capacity of a single pile, group efficiency and solid foundation check, check of the maximum pile-top axial force |
| Pile Forces | Forces | m method: cap displacement, pile-top forces of every pile, and the depth distribution of moment, shear, displacement and soil resistance |
| Spring Stiffness | Springs | 6×6 stiffness matrix of a pile group or a single pile, and the distributed lateral and vertical springs and the tip springs along the pile |

All four pages share the pile model entered on the Pile Data page. The step bar at the top of each page lists the four steps in order from 1 to 4. Click **Next** after entering the data to open the following page without going back to the sidebar. When any input there changes, the results already shown on the other pages are cleared and **Calculate** must be clicked again. After the Pile Forces page has computed the pile-top axial forces, the Bearing Capacity page compares the largest one, $`N_{\max}`$, with the allowable capacity $`[P]`$ of a single pile.

### 12.2 Pile Data

The left side of the page holds **Project**, **Soil layers** and **Loads**. On the right, the sketch (elevation and plan side by side) is at the top, with **Pile** and **Piles and cap** below it.

**Project**

- **Load an example**: loads one of the two examples (example 1: a single row of two piles, example 2: high cap with 2×2 piles) to learn the input format and check results (see [section 12.7](#127-checking-the-examples)).
- **Open… / Save…**: reads and writes a JSON project file holding all inputs.
- **Reference tables**: click the card at the top right of the Project group to see the reference tables: $`m`$ and $`m_0`$ values, the shaft friction of bored and driven piles, the bearing capacity of the soil at the pile tip and the correction coefficients of bored piles. The Reference code box at the top of the window switches between the built-in TB 10093-2017 (railway bridges and culverts, the default) and JTG 3363-2019 (highway bridges and culverts); a note below the tables gives the source and the conditions of use. More codes can be added by placing `.json` files in the `Reference Codes` folder of the working directory (the file format is described at the top of `src/utils/pile_reference.py`); they are read when the window opens.

**Pile**

| Input | Description |
| --- | --- |
| Section | Circular or rectangular. The Y width of a rectangular pile is entered separately |
| Diameter / width X | Diameter of a circular pile, or width along X of a rectangular pile. Use the hole diameter for bored piles |
| Concrete grade / Elastic modulus Ec | Choose C20 to C50 to get $`E_c`$ automatically, or choose Custom to enter it |
| EI factor | Flexural rigidity $`EI = \text{factor} \times E_c I`$, 0.8 in TB 10002.5-2005 |
| Free length l0 | Height $`l_0`$ of the pile top (bottom of the cap) above the ground or local scour line, 0 for a low cap |
| Embedded length h | Embedded length $`h`$ below the ground or local scour line |
| Construction | Bored / dug cast-in-place, or driven / vibro-sunk. It affects the coefficient $`\xi`$ of the axial stiffness |
| Bearing type | Friction pile or end-bearing pile |
| Pile tip | In soil, on the rock surface, or embedded in rock. It sets the boundary condition at the pile tip |
| m0 of tip soil | Proportional coefficient $`m_0`$ of the vertical subgrade coefficient below the tip, 0 uses $`m`$ of the last layer |
| Rock strength R | Saturated uniaxial compressive strength of the rock at the tip, used to look up the vertical subgrade coefficient $`C_0`$ |

**Soil layers**: enter, from the ground downward, the name, thickness, $`m`$, unit weight $`\gamma`$, friction angle $`\varphi`$, ultimate shaft friction $`f`$ and the factor $`\alpha_i`$ for driven piles. The last layer extends to the pile tip. $`m`$ is used for pile forces and spring stiffness, $`\gamma`$ and $`\varphi`$ for the group check, $`f`$ and $`\alpha_i`$ for capacity.

**Piles and cap**

- The pile table lists the X and Y coordinates of each pile (origin at the center of the cap bottom), **Batter** (angle from the vertical) and **Azimuth** (direction of the lean, measured from +X toward +Y). **Grid…** generates a rectangular array.
- **Cap type**: high cap (pile tops above the ground) or low cap. A low cap needs the embedment depth $`h_n`$ and the cap widths in both directions, which are used for the soil resistance on the cap sides.

**Loads**: $`\Sigma N`$, $`\Sigma P`$ and $`\Sigma M`$ at the center of the cap bottom, and the **Loading direction** (along X or along Y). The elevation (in the plane of the horizontal force) and plan views in the sketch update as you type, with the name of each view below it. The $`m`$ labels in the elevation mark the proportional coefficient of each layer.

#### Sign Conventions

- Planar analysis (Pile Forces page): x to the right, y downward. $`\Sigma N`$ is positive downward, $`\Sigma P`$ is positive along the loading direction, and $`\Sigma M`$ is positive in the same sense as $`\Sigma P`$ (clockwise when the force points right).
- Cap displacements: $`a`$ (horizontal), $`b`$ (vertical, positive downward) and $`\beta`$ (rotation, positive clockwise).
- Pile inclination $`\varphi`$ is positive when the pile bottom leans toward the loading direction. The projected inclination of a batter pile in the loading plane is derived from Batter and Azimuth.
- Global axes of the Spring Stiffness page: X and Y horizontal, Z upward, right-handed, rotations by the right-hand rule, DOF order UX, UY, UZ, RX, RY, RZ.

### 12.3 Bearing Capacity

![Bearing Capacity page](docs/images/22_pile_capacity.png)

**Single pile axial capacity**: choose the calculation method in Method and the page lists the inputs it needs. The pile perimeter $`U`$ and the base area $`A`$ come from the pile section (for bored piles $`U`$ uses the hole diameter increased by Hole enlargement). The layer values $`f_i`$ and lengths $`l_i`$ come from the soil layers and the embedded length on the Pile Data page.

| Method | Formula |
| --- | --- |
| Bored / dug friction pile (Eq. 5-57) | $`[P] = \tfrac12 U \sum f_i l_i + m_0 A [\sigma]`$ |
| Driven / vibro-sunk friction pile (Eq. 5-58) | $`[P] = \tfrac12 \left( U \sum \alpha_i f_i l_i + \lambda A R \alpha \right)`$ |
| End-bearing pile socketed in rock (Eq. 5-59) | $`[P] = R \,(C_1 A + C_2 U h)`$, with $`C_1`$ multiplied by 0.7 and $`C_2 = 0`$ when $`h \le 0.5\ \mathrm{m}`$ |
| Driven end-bearing pile on rock (Eq. 5-60) | $`[P] = C R A`$, $`C = 0.45`$ (uniform rock) or 0.3 (cracked or weathered rock) |
| Friction pile in tension (Eq. 5-61) | $`[P'] = 0.3\, U \sum \alpha_i l_i f_i`$ |
| Static load test (Eq. 5-62) | $`[P] = P_j / K`$ |
| Static cone penetration (Eq. 5-63 to 5-66) | $`[P] = \tfrac12 (U \sum \alpha_i f_i l_i + \lambda A R \alpha)`$, $`f_i = \beta_i \bar f_{si}`$, $`R = \beta \bar q_c`$ |
| Pile material and buckling (Eq. 5-67) | $`[P] = \varphi\,[\sigma_c]\,(A_c + m A_s')`$ |

The allowable bearing capacity $`[\sigma]`$ below a bored pile has three ranges of the tip depth: for $`h \le 4d`$, $`[\sigma] = \sigma_0 + k_2 \gamma_2 (h-3)`$; for $`4d < h \le 10d`$, $`[\sigma] = \sigma_0 + k_2\gamma_2(4d-3) + k_2'\gamma_2 (h-4d)`$; for $`h > 10d`$, $`h-4d`$ is replaced by $`6d`$. In the cone penetration method, $`\beta_i`$ and $`\beta`$ come from two groups of formulas in Eqs. (5-65) and (5-66): the first group is used when the mean tip resistance exceeds 2000 kPa and the friction ratio does not exceed 0.014, the second otherwise. For the pile material check, the buckling factor $`\varphi`$ is interpolated from table 5-15 using the slenderness ratio, which is based on the calculated length of the pile (table 5-16).

The result table lists the intermediate values and the final $`[P]`$. The chart at the lower right shows $`[P]`$ and its shaft and tip parts against the embedded length, with a red circle at the current embedded length.

**Pile group**

- **Allowable base stress [σ]**: allowable bearing capacity of the soil at the base plane of the group, used for the solid foundation check. When **Estimate the weight of the soil block** is checked, the weight of the cap, soil and piles above the base plane is estimated from the layer unit weights. Uncheck it to enter $`G`$ directly.
- Minimum center distance $`s`$ and $`s/d`$. Group efficiency $`\eta = 1 - \dfrac{\arctan(d/s)}{90^\circ}\cdot\dfrac{(n-1)m + (m-1)n}{mn}`$ (Eq. 5-68), where $`m`$ and $`n`$ are the number of columns and the piles per column, counted from the distinct X and Y coordinates.
- When friction piles are closer than $`6d`$, the base stress is checked with the group as a solid foundation. The base plane extends $`h \tan(\bar\varphi/4)`$ beyond the outer piles, $`\sigma_{\max,\min} = N/A \pm M/W`$ (Eq. 5-69), $`\bar\varphi`$ is the thickness-weighted mean friction angle of the layers (Eq. 5-70), and the base moment is $`M = \Sigma M + \Sigma P\,(l_0 + h)`$.
- After the calculation on the Pile Forces page, the page shows the check $`N_{\max} \le [P]`$.

### 12.4 Pile Forces (m Method)

![Pile Forces page](docs/images/23_pile_forces.png)

#### Procedure

1. Enter or load the model on the Pile Data page.
2. Choose how the influence functions are obtained in **Influence functions** (see [section 12.6](#126-the-two-ways-to-get-the-influence-functions)).
3. Click **Calculate**. The **Parameters** table on the left lists all intermediate parameters. The chart at the upper right shows $`M`$, $`Q`$, $`x`$ and $`\sigma_x`$ against depth for the selected pile. The three tabs below hold the pile-top forces of each pile, the distribution table and the influence function values at each depth.
4. Use **Pile** to switch the pile, **Depth step** to set the spacing of the distribution table, and **Show the part above the ground** to include the free length in the chart and the table.

#### Calculation Method

**1. Calculation width and deformation factor.** The lateral soil resistance is $`\sigma_x = C_y x`$ with $`C_y = m y`$. The calculation width of a pile is

```math
b_0 = K_f K_0 d,\qquad K_0 = \begin{cases} 1 + 1/d, & d \ge 1 \\ 1.5 + 0.5/d, & d < 1 \end{cases}
```

with $`K_f = 0.9`$ for circular piles and 1 for rectangular piles. When $`n`$ piles stand side by side perpendicular to the force, the smaller of $`K_f K_0 d`$ and $`(D'+1)/n`$ is used, where $`D'`$ is the distance between the outer edges of the row. The deformation factor is

```math
\alpha = \left( \frac{m\, b_0}{EI} \right)^{1/5},\qquad EI = 0.8\, E_c I
```

Layered soil is converted over the depth $`h_m`$ by equal areas under the subgrade coefficient curve: $`\bar m = \sum m_i (z_{b,i}^2 - z_{a,i}^2) / h_m^2`$. First assume $`\alpha h > 2.5`$ and take $`h_m = 2(d+1)`$, and if that is not satisfied take $`h_m = h`$. The interaction of piles in front of and behind each other enters through a factor $`k`$ that multiplies $`m`$. With $`n`$ piles in the force direction and a clear spacing $`L_0`$, $`k = C + \dfrac{1-C}{0.6}\dfrac{L_0}{h_0}`$ when $`L_0 < 0.6 h_0`$, otherwise $`k = 1`$, with $`h_0 = 3(d+1)`$ and $`C = 1,\ 0.6,\ 0.5,\ 0.45`$ for $`n = 1,\ 2,\ 3,\ \ge 4`$.

**2. Initial parameter solution.** With $`t = \alpha y`$ the deflection equation becomes $`x'''' = -t\,x`$. At depth $`y`$ the displacement, rotation, moment and shear are

```math
\begin{aligned}
x_y &= x_0 A_1 + \tfrac{\varphi_0}{\alpha} B_1 + \tfrac{M_0}{\alpha^2 EI} C_1 + \tfrac{Q_0}{\alpha^3 EI} D_1
\\ \tfrac{\varphi_y}{\alpha} &= x_0 A_2 + \tfrac{\varphi_0}{\alpha} B_2 + \tfrac{M_0}{\alpha^2 EI} C_2 + \tfrac{Q_0}{\alpha^3 EI} D_2
\\ \tfrac{M_y}{\alpha^2 EI} &= x_0 A_3 + \tfrac{\varphi_0}{\alpha} B_3 + \tfrac{M_0}{\alpha^2 EI} C_3 + \tfrac{Q_0}{\alpha^3 EI} D_3
\\ \tfrac{Q_y}{\alpha^3 EI} &= x_0 A_4 + \tfrac{\varphi_0}{\alpha} B_4 + \tfrac{M_0}{\alpha^2 EI} C_4 + \tfrac{Q_0}{\alpha^3 EI} D_4
\end{aligned}
```

$`x_0`$ and $`\varphi_0`$ at the ground ($`y = 0`$) follow from the boundary condition at the pile tip. For a tip in soil or on the rock surface, the shear at the tip is zero and the moment equals the resisting moment of the soil at the tip, $`-C_0 \varphi_h I_0`$ (written with $`K_h = C_0 I_0 / (\alpha EI)`$). For a tip embedded in rock, the displacement and rotation at the tip are zero. $`K_h`$ is taken as 0 when $`\alpha h \ge 2.5`$ (3.5 on the rock surface), and $`\bar h = \alpha h`$ is limited to 4. This gives the flexibilities at the ground, $`\delta_{QQ}`$, $`\delta_{MQ} = \delta_{QM}`$ and $`\delta_{MM}`$, with

```math
x_0 = Q_0 \delta_{QQ} + M_0 \delta_{QM},\qquad \varphi_0 = -(Q_0 \delta_{MQ} + M_0 \delta_{MM})
```

**3. Pile-top stiffness coefficients.** With the pile top $`l_0`$ above the ground, the pile-top flexibilities are

```math
\delta_1 = \frac{l_0^3}{3EI} + \delta_{MM} l_0^2 + 2\delta_{MQ} l_0 + \delta_{QQ},\quad
\delta_2 = \frac{l_0}{EI} + \delta_{MM},\quad
\delta_3 = \frac{l_0^2}{2EI} + \delta_{MM} l_0 + \delta_{MQ}
```

With $`\Delta = \delta_1 \delta_2 - \delta_3^2`$, $`\rho_2 = \delta_2/\Delta`$, $`\rho_3 = \delta_3/\Delta`$ and $`\rho_4 = \delta_1/\Delta`$. The axial stiffness coefficient is

```math
\rho_1 = \frac{1}{\dfrac{l_0 + \xi h}{EA} + \dfrac{1}{C_0 A_0}}
```

with $`\xi = 1`$ for end-bearing piles, 1/2 for bored friction piles and 2/3 for driven friction piles. The vertical subgrade coefficient $`C_0`$ at the tip comes from table 5-2 by interpolation on the saturated uniaxial strength $`R`$ for rock ($`3 \times 10^5`$ kPa/m for $`R \le 1000`$ kPa and $`1.5 \times 10^7`$ kPa/m for $`R \ge 25000`$ kPa), and is $`10 m_0`$ for soil with $`h \le 10`$ m and $`m_0 h`$ for $`h > 10`$ m. The base area $`A_0`$ is the section area for end-bearing piles. For friction piles the load spreads from the pile sides downward at $`\bar\varphi/4`$ to the tip, limited by the area from the pile spacing.

**4. Displacement method for pile groups.** The cap is rigid. The cap displacements $`(a, b, \beta)`$ and the loads $`(\Sigma P, \Sigma N, \Sigma M)`$ satisfy $`\mathbf{K}\,(a, b, \beta)^{\mathrm T} = (\Sigma P, \Sigma N, \Sigma M)^{\mathrm T}`$. The stiffness matrix of one pile in its own axis frame is

```math
\mathbf{K}'_i = \begin{bmatrix} \rho_2 & 0 & -\rho_3 \\ 0 & \rho_1 & 0 \\ -\rho_3 & 0 & \rho_4 \end{bmatrix}
```

Transform each pile stiffness matrix to the global coordinate system, then sum the results.

```math
\mathbf{T}_i = \begin{bmatrix}
\cos\varphi & -\sin\varphi & -x_i\sin\varphi
\\ \sin\varphi & \cos\varphi & x_i\cos\varphi
\\ 0 & 0 & 1
\end{bmatrix}
```

```math
\mathbf{K} = \sum \mathbf{T}_i^{\mathrm T} \mathbf{K}'_i \mathbf{T}_i
```

For a high cap, the result agrees with the closed form of Eq. 5-35. A low cap adds the soil resistance on the cap sides through $`b_0 A_c`$, $`b_0 S_c`$ and $`b_0 I_c`$, with $`A_c = C_n h_n/2`$, $`S_c = C_n h_n^2/6`$, $`I_c = C_n h_n^3/12`$ and $`C_n = m h_n`$, where $`b_0`$ is the cap width perpendicular to the loading direction.

**5. Pile-top forces and distribution along the pile.** After solving for $`(a, b, \beta)`$, the pile-top displacements of pile $`i`$ in the pile axis frame are $`a' = a\cos\varphi - (b + x\beta)\sin\varphi`$ and $`b' = a\sin\varphi + (b + x\beta)\cos\varphi`$, and the forces are

```math
Q' = \rho_2 a' - \rho_3 \beta,\qquad N' = \rho_1 b',\qquad M' = -\rho_3 a' + \rho_4 \beta
```

which are transformed back to $`N`$ and $`Q`$ in the structural frame. At the ground, $`Q_0 = Q'`$ and $`M_0 = M' + Q'\, l_0/\cos\varphi`$. Putting them into the initial parameter solution gives $`x`$, $`\varphi`$, $`M`$ and $`Q`$ along the pile. The soil resistance is $`\sigma_x = m k\, y\, x`$. The distribution is evaluated down to $`\min(h,\ 4/\alpha)`$, below which the forces can be taken as zero.

#### Output

- **Parameters**: $`b_0`$, $`n`$, $`L_0`$, $`k`$, $`\bar m`$, $`h_m`$, $`EI`$, $`EA`$, $`\alpha`$, $`\alpha h`$, $`C_0`$, $`A_0`$, $`K_h`$, the flexibilities $`\delta`$, the stiffness coefficients $`\rho_1 \sim \rho_4`$, the group stiffness terms $`\gamma_{aa}`$ and so on, and the cap displacements $`a`$, $`b`$ and $`\beta`$.
- **Pile-top forces**: coordinates, inclination, $`N`$, $`Q`$, $`M`$, pile-top displacement and rotation, and $`Q_0`$ and $`M_0`$ at the ground for every pile.
- **Distribution**: depth, $`\alpha y`$, $`x`$, $`\varphi`$, $`M`$, $`Q`$ and $`\sigma_x`$. The part above the ground is listed at negative depth and only $`M`$ and $`Q`$ have values there.
- **Influence functions**: the sixteen values $`A_1 \sim D_4`$ at each depth, for comparison with hand calculations.

### 12.5 Spring Stiffness

![Spring Stiffness page](docs/images/24_pile_springs.png)

This page prepares two kinds of data for a finite element model: the six-DOF stiffness matrix at the pile top, and the distributed springs along the pile.

#### Six-DOF Stiffness Matrix

**Object** is either **Pile group** (the matrix acts at the cap center at the level of the pile tops, with the origin at the center of the cap bottom on the Pile Data page) or **Single pile** (the pile top of a vertical pile at the origin, without group effects). After **Calculate**, the matrix appears in the **6×6 matrix** tab, and **Diagonal terms** at the lower left lists the diagonal terms with units: the first three are translational stiffnesses (kN/m) and the last three rotational stiffnesses (kN·m/rad).

Method:

1. For each pile, the m-method parameters are taken once with the X direction and once with the Y direction as the loading direction, giving $`\rho_1 \sim \rho_4`$ for both. In the pile-head matrix of a vertical pile, $`k_{UZ} = \rho_1`$, $`k_{UX} = k_{UY} = \rho_2`$, $`k_{RX} = k_{RY} = \rho_4`$, and the coupling terms are $`k_{UX,RY} = -\rho_3`$ and $`k_{UY,RX} = +\rho_3`$. A batter pile is first built in its axis frame and then transformed to the global axes with Batter and Azimuth.
2. The torsional stiffness about the pile axis is $`k_{RZ} = G J / (l_0 + L_t)`$, with $`G = E_c/[2(1+\nu)]`$, the torsion constant $`J`$ of the section ($`\pi d^4/32`$ for a circle) and the effective embedded length $`L_t = \min(h,\ 2/\alpha)`$. **The code and the sources used give no torsional stiffness, so this term is an assumption of the program** and serves only as an estimate.
3. The pile-head matrices are moved to the cap center with a rigid link matrix and summed. For a low cap, the terms for the soil resistance on the cap sides are added when **Include the soil resistance on the cap sides** is checked.

#### Shaft, Side and Tip Springs

The embedded length is divided into elements by **Element length** (an element never crosses a layer boundary). Each node gets:

| Spring | Value |
| --- | --- |
| Lateral side spring $`k_h`$ (X and Y) | $`k_h = m\, z\, k\, b_0\, \Delta l`$, with node depth $`z`$, tributary length $`\Delta l`$, $`m`$ of the layer the node belongs to, and the group factor $`k`$ (applied only when **Apply the group factor k to the lateral springs** is checked, never for a single pile) |
| Vertical side spring $`k_v`$ | $`k_v = f\, U\, \Delta l / s`$, with the ultimate shaft friction $`f`$ of the layer, the pile perimeter $`U`$, and $`s`$ from **Friction mobilization displacement**, the relative displacement at which the ultimate friction is fully mobilized |
| Axial spring of the shaft | $`EA/\Delta l`$ |
| Vertical tip spring | $`C_0 A_0`$ |
| Rotational tip spring | $`C_0 I_0`$, with $`I_0`$ the moment of inertia of the pile section |

**The friction mobilization displacement $`s`$ is an assumption.** The default is 6 mm and the usual range is 4 to 10 mm. The code does not give it, so adjust it to the ground conditions and pile test results. The springs are secant stiffnesses to be used as linear. In the chart, the left plot shows the X and Y lateral springs against depth and the right plot shows the vertical springs. The **Side springs** tab lists the depth, layer, $`m`$, $`C_y`$, $`k_h`$, $`k_v`$, shaft stiffness and ultimate friction of each node, and the **Shaft and tip** tab lists $`EA`$, $`EI`$, $`GJ`$, the tip springs and totals.

> When the six-DOF matrix is used in a finite element model, make sure the axes of the modeling software agree with those of this page (Z upward). Under vertical compression the pile top moves down, which is a negative UZ. Software with other axis and rotation definitions needs a transformation first.

### 12.6 The Two Ways to Get the Influence Functions

The Pile Forces and Spring Stiffness pages both provide the **Influence functions** option:

- **Direct calculation (power series)**: solves $`x'''' = -t\,x`$ directly as a power series $`x = \sum a_n t^n`$ with the recurrence $`a_{n+4} = -a_{n-1} / [(n+1)(n+2)(n+3)(n+4)]`$ and $`a_4 = 0`$. The four sets of initial values give $`A_1 \sim D_1`$, and differentiation gives $`A_2 \sim D_4`$. The series converges in double precision and has no interpolation error from the table spacing. Both methods limit $`\bar h`$ to 4, as the book specifies.
- **Table lookup (linear interpolation)**: looks up the table in appendix B of the book ($`\bar h = 0 \sim 4.0`$, 28 rows) and interpolates linearly. $`\bar h > 4`$ is treated as 4. This suits comparison with hand calculations and the examples.

The two methods differ by no more than 0.1 % in the examples. Direct calculation is the default. Switch to table lookup when you need to compare with appendix B or a hand calculation.

### 12.7 Checking the Examples

The two examples can be loaded with **Load an example** on the Pile Data page. The table compares the program with the hand calculation.

| Item | Hand calculation | Program |
| --- | --- | --- |
| Example 1: $`\alpha`$ | 0.4232 | 0.4232 |
| $`\delta_{QQ}`$, $`\delta_{MQ}`$, $`\delta_{MM}`$ | $`3.039\times10^{-5}`$, $`0.8566\times10^{-5}`$, $`0.3907\times10^{-5}`$ | $`3.036\times10^{-5}`$, $`0.8535\times10^{-5}`$, $`0.3901\times10^{-5}`$ |
| $`x_0`$ (m), $`\varphi_0`$ (rad) | 0.004603, -0.001835 | 0.004590, -0.001831 |
| Maximum moment (kN·m) | 407.6 (near 1.4 m) | 407.5 (near 1.4 m) |
| Example 2: $`\alpha`$, $`k`$ | 0.4036 ($`\alpha h = 7.67`$), 0.789 | 0.4036, 0.789 |
| $`\rho_1`$, $`\rho_2`$, $`\rho_3`$, $`\rho_4`$ | $`8.66\times10^{5}`$, $`0.357\times10^{5}`$, $`1.162\times10^{5}`$, $`5.459\times10^{5}`$ | $`8.657\times10^{5}`$, $`0.356\times10^{5}`$, $`1.158\times10^{5}`$, $`5.441\times10^{5}`$ |
| Cap displacement $`a`$ (cm), $`\beta`$ (rad) | 0.2539, $`36.04\times10^{-5}`$ | 0.2544, $`36.05\times10^{-5}`$ |
| $`N_{\max}`$, $`N_{\min}`$, $`Q`$, $`M`$ | 1432.4, 590.6, 48.76, -98.29 | 1432.9, 590.1, 48.75, -98.33 |

The differences come from the rounding of intermediate values in the hand calculation.

### 12.8 Calculation Report

Click **Export calculation report...** on the right of the top bar of any pile foundation page, tick the parts to include in the dialog that opens, and choose where to save the file to get a PDF calculation report of the pile foundation. The report uses the **current settings** of the four pile pages: the capacity method and its parameters on the Bearing Capacity page, the influence function method and the selected pile on the Pile Forces page, and the object and spring parameters on the Spring Stiffness page. Check the settings on those pages first. There is no need to click Calculate on the Pile Forces page.

![Cover and pages of the report](docs/images/25_pile_report.png)

![Choosing the contents](docs/images/26_pile_report_options.png)

The dialog lets you tick the six parts separately: general description, input data, axial capacity, lateral analysis, spring stiffness and summary of results. **Select all** and **Clear all** change every box at once. The cover and the contents are always included, and the contents list only the selected parts. The summary lists the results of the calculation parts that were selected, so it is unavailable until axial capacity, lateral analysis or spring stiffness is ticked. At least one part other than the summary is required. The boxes ticked by default depend on the page you open the dialog from: only the parts up to the current step are ticked. For example, opened from the Bearing Capacity page, general description, input data, axial capacity and summary of results are ticked. Tick more parts if you need them.

- **Cover**: the report title, the software, its version and the calculation date. It has no organization name or signature block.
- **Contents and page numbers**: the contents follow the cover. The header shows the report title and the software version, and the footer shows the page number.
- **Body**: general description (basis, units and sign conventions), input data (tables of the pile and materials, soil layers, pile layout and loads, with the elevation and plan sketches), axial capacity (formulas, parameters, calculation, pile group and solid foundation checks, and the check of the maximum pile-top axial force), lateral analysis (substituted calculation of the parameters, flexibilities and stiffness coefficients, the group stiffness matrix, cap displacements, pile-top forces and the charts and tables of the forces along the pile), spring stiffness (six-DOF matrix, tables and chart of the distributed springs) and a summary of results. Equations, tables and figures are numbered.
- **Language**: the report follows the interface language. Switch the interface language before exporting to get the report in the other language.
- **Formulas**: formulas are typeset with LaTeX and stored as images (`resources/report/formulas`, generated by `scripts/build_report_formulas.py`), so LaTeX is not needed to run the program. The calculations with substituted values are set as text.

The report cannot be exported when the input is invalid (for example a zero layer thickness), and the page shows the reason.

### 12.9 Assumptions and Scope

- The m method treats the soil beside the pile as an elastic foundation whose stiffness grows linearly with depth. It suits the service state with small horizontal pile-top displacements and cannot represent soil nonlinearity or the ultimate state.
- Batter piles and rectangular piles are treated in a simplified way. The m-method parameters of a batter pile follow those of a vertical pile and the inclination only enters the planar transformation. The calculation width and the moment of inertia of a rectangular pile are taken in the loading direction.
- The torsional stiffness and the friction mobilization displacement $`s`$ are assumptions of the program and not part of the code or the sources used.
- The axial capacity formulas and parameter tables come from the older railway bridge foundation code (《铁路桥涵地基和基础设计规范》, TB 10002.5-2005). Values and format may differ from the current code, so check them before use.
- The program checks $`N_{\max} \le [P]`$ but does not check reinforcement or crack width of the pile section, nor special calculations such as liquefaction or soil resistance reduction under earthquake action.

---

## 13. Export

### 13.1 Figure Export (Right-click)

Right-click any figure → **Save figure as image…** to open the figure export window. The left side shows a preview at the actual aspect ratio and the right side the settings; the preview updates as settings change. Export works on a copy of the figure and does not change the figure in the program.

![Figure export window](docs/images/13_figure_export.png)

#### Size and Format

| Setting | Description | Default |
| --- | --- | --- |
| Export | Only for loop decomposition: Combined figure / Separate files (one file per cycle) | Combined figure |
| Aspect ratio | 4:3, 3:2, 16:9, 1:1, 5:4, 2:1, Golden (1.618:1), 3:4 (portrait), Free | 4:3 |
| Width / Height | Figure size (cm). For fixed ratios the height follows the width; with **Free** enter the height or drag the handle at the bottom right of the preview | 16 cm × 12 cm |
| Resolution | Resolution (dpi) | 500 dpi |
| Format | PNG, JPEG, TIFF (raster), SVG, PDF, EPS (vector) | PNG |
| Output size | Pixel size of raster output (read-only) | — |

#### Content

| Setting | Description |
| --- | --- |
| Title | Figure title (overall title for figures with subplots; in Separate files mode each cycle keeps its own title) |
| X label / Y label | Axis labels (common labels for figures with subplots) |
| Font size | Base font size (pt), determined from the figure width by default (8 cm → 8 pt, 16 cm → 10 pt, 7–14 pt). The title is 1 pt larger, tick labels 1.5 pt smaller (2.5 pt for subplots) and the legend 0.5 pt smaller than the base size |
| Ticks | Tick direction: Inward, Outward, Both |
| Legend | Show the legend; position As shown (keep the position in the program, e.g. the outside legend of the tripartite spectrum), Best, Upper left, Upper right, Lower left, Lower right |
| Grid | Show the grid (also controls the custom grid of the tripartite spectrum) |

The default path is `{export folder}/Export Figures/{figure name}`. The program checks whether the content exceeds the canvas and extends the bounds if necessary, so labels are never cut off.

### 13.2 Table Export (Right-click)

Right-click any data table → **Export table data…**:

![Table export window](docs/images/15_table_export.png)

| Setting | Description |
| --- | --- |
| Columns | Columns to export |
| Header | Include the header |
| Numerical precision | Number of decimal places |
| Delimiter | Delimiter; enter `\t` for tab |
| File format | `*.csv`, `*.txt`, or `*.other` with a custom suffix (e.g. `*.dat`) |
| Export file | Output file, default `{export folder}/Export Table/{table name}` |
| Save these options as global defaults | Save header, precision, delimiter and file format to the global settings |

Header, Numerical precision, Delimiter and File format are initialized from the global settings. Data are exported at full precision formatted to the given decimals (independent of the displayed digits); empty values are exported as empty strings. Files are UTF-8 with BOM so that Excel opens them directly and shows characters such as Δ and ξ in headers correctly.

### 13.3 Batch Export (Export all)

The **Export all** button of each page opens the batch export window:

![Batch export window](docs/images/14_batch_export.png)

| Area | Description |
| --- | --- |
| Curves | All loaded curves or Current curve only. Not shown for Ground Motion Params, which exports summary results |
| Contents | Exportable contents grouped as Data tables / Figures; check the items to export (see each page) |
| Data format | Header, Precision, Delimiter, File format, initialized from the global settings |
| Figure format | Format (PNG), Resolution (500 dpi), Aspect ratio (4:3), Width (16 cm); the font size follows the width. Hysteresis Analysis also has Loop figures |
| Export folder | Output folder, default `{export folder}/Export {page name}` |
| Separate folder for each curve | Put the files of each curve in a subfolder named after the curve |

Click **Export** to start; a progress dialog is shown and the number of files and the folder are reported at the end.

Default folders:

| Page | Default folder |
| --- | --- |
| Response Spectrum | Export Response Spectrum |
| Wave Response Spectrum | Export Wave Spectrum |
| Amplitude Seismic Wave | Export Amplitude Wave |
| Ground Motion Params | Export Ground Motion Parameters |
| Hysteresis Analysis | Export Hysteresis Results |
| Skeleton Curve | Export Skeleton Curve |

### 13.4 Exporting Selected Waves and Spectra

The **Export selected …** commands in the context menus of the Response Spectrum list and the Amplitude Seismic Wave page, and the **Export selected** button, use the wave export window:

| Setting | Description |
| --- | --- |
| Time column / Accel column | Column numbers of time (period) and acceleration in the output file; uncheck to omit the column |
| Header | Include the header |
| Separate folder | One subfolder per wave |
| Numerical precision | Number of decimal places |
| Delimiter | Delimiter |
| File format | File suffix |
| Export path | Output folder |

All settings are initialized from the global settings.

---

## 14. Settings

Click **Settings** in the sidebar to open the settings window:

![Settings window](docs/images/16_settings.png)

| Setting | Description |
| --- | --- |
| Import path | Initial folder of import dialogs |
| Export path | Root folder of exports (default location of figures, tables and batch exports) |
| Time column / Accel column | Default column numbers of time and acceleration in the wave export window, and whether to export them |
| Header | Include the header in exports by default |
| Separate folder | Create a subfolder per curve by default |
| Numerical precision | Default number of decimal places |
| Delimiter | Default delimiter |
| File format | Default file format |
| Language | Interface language (English / Chinese), same as the language button in the sidebar |
| Acceleration/Velocity/Displacement Units | Units of the results on the Ground Motion Params page (see [Section 7.2](#72-parameter-options)), applied after clicking Calculate on that page; other pages use units as described on each page |

- **OK**: save the settings to `settings.ini`.
- **Program Defaults**: after confirmation, restore and save the program defaults (import folder `Import Data` and export folder `Export Data` under the working folder; exports without header, comma separated, 4 decimals, `*.csv`).
- **Cancel**: discard changes.

Checking "Save these options as global defaults" in the table export window also updates the export defaults here.

The working folder of the program is the current folder at startup. After installation with the installer, the Start menu and desktop shortcuts start in `Documents\SeismicTools`, where `settings.ini`, `Import Data` and `Export Data` are kept. When the exe is started directly from a folder that is not writable (for example under `Program Files`), the program switches to this folder automatically. Uninstalling does not delete the data in it.

---

## 15. FAQ and Notes

**Q1: An intermediate parameter of the code spectrum shows `--` with "Failed to find … value".**
The selected combination has no value in the code tables (e.g. E2 for Class D bridges); enter the intermediate parameter manually before calculating.

**Q2: How do I import ground motion files aligned with tabs or spaces?**
Select Tab or Whitespace as Delimiter in the import window. Whitespace treats any run of spaces or tabs as one delimiter, suitable for fixed-width text. Custom requires one character; leaving it empty shows "Please enter a custom delimiter!".

**Q3: A PEER file fails to import.**
The program looks for a line such as `NPTS=  5000, DT=   .0100 SEC` in the first 4 lines. If the header was modified or has another format, convert the data to one value per line and import it as Singal accel: skip the header with First line and enter Time step.

**Q4: The figure is empty after switching to another record in Wave Response Spectrum.**
After changing Spectrum options only the current record is recalculated automatically; click **Calculate** again for the other records.

**Q5: Hysteresis Analysis reports "No complete loading cycle was found in the data!".**
The data contain no complete cycle returning to zero displacement. Check the displacement and force columns, whether the data are in loading order, and whether Zero tolerance is too large (a large tolerance treats small cycles as fluctuations around zero).

**Q6: The skeleton curve misses loading levels or picks one level twice.**
Adjust Level tolerance: increase it when the amplitudes of repeated cycles vary a lot, decrease it when adjacent levels differ only slightly.

**Q7: The ultimate point row is marked with `*`.**
The load did not drop to the Ultimate load ratio in the test; the last skeleton point is used as the ultimate point, so the ductility factor is a lower bound.

**Q8: The farthest point yield point is empty.**
The skeleton curve is nearly straight (essentially elastic up to the peak), so the farthest point method cannot determine a distinct yield point.

**Q9: Text or the legend is incomplete in an exported image.**
The program extends the canvas to include all content during export. If the result is still unsatisfactory, increase Width or decrease Font size in the export window; the tripartite spectrum has its legend outside on the right, so 16:9 or Free is recommended.

**Q10: The window is too narrow and the figures are crowded.**
Click the collapse button (») of the sidebar; the narrow rail gives about 150 pixels more width to the result area.

**Q11: The peak load changes a lot after the correction, or the result looks unreasonable.**
Check the units of the geometry parameters (lengths are entered in the current display unit), the sign of the vertical force (compression positive) and whether the selected loading setup matches the test. When the vertical force is large compared with the horizontal force and the loading height $`L`$ is small, the $`P\Delta/L`$ correction becomes large.

**Q12: The vertical and horizontal curves have different displacement precision. How are they aligned?**
No preprocessing is needed. Select Force-displacement as Source and import the vertical force–displacement curve; the program fuses the curves by cumulative displacement path (see [Section 10.4](#104-fusing-the-vertical-force-with-the-horizontal-curve)). With only a vertical force time history, select Time history, which aligns by point index.

**Q13: Test Curve Processing detects too many or too few waiting segments.**
Check the marked regions in the time histories at the lower right, then adjust Flatness tolerance and Minimum length (see [Section 11.2](#112-detection-and-removal)); you can also uncheck individual segments that should be kept.

**Q14: A pile page reports a failed calculation or shows no result after Calculate.**
Check the inputs on the Pile Data page first (for example, layer thickness and $`m`$ must be greater than zero and at least one pile is required). The red text at the top of the page gives the reason.

**Q15: Pile Forces differs slightly from the hand-calculated examples.**
The hand calculation uses rounded intermediate values. The difference is usually 0.1 % to 0.5 %. Choose Direct calculation to rule out the interpolation of the table.

**Q16: The Bearing Capacity page has no $`N_{\max}`$ check row.**
Click Calculate on the Pile Forces page first. Any change on the Pile Data page clears that result.

---

## 16. References

1. GB 50111-2006 Code for Seismic Design of Railway Engineering (2009 edition) [S].
2. JTG/T 2231-01-2020 Specifications for Seismic Design of Highway Bridges [S].
3. GB 50011-2010 Code for Seismic Design of Buildings (2016 edition) [S].
4. JGJ/T 101-2015 Specification for Seismic Test of Buildings [S].
5. Feng P, Qiang H L, Ye L P. Discussion and definition on yield points of materials, members and structures[J]. Engineering Mechanics, 2017, 34(3): 36-46.
6. Chopra A K. Dynamics of Structures: Theory and Applications to Earthquake Engineering[M]. Pearson. (Section 5.2, exact solution for piecewise-linear excitation)
7. Nigam N C, Jennings P C. Calculation of response spectra from strong-motion earthquake records[J]. Bulletin of the Seismological Society of America, 1969, 59(2).
8. Park R. Evaluation of ductility of structures and structural assemblages from laboratory testing[J]. Bulletin of the New Zealand National Society for Earthquake Engineering, 1989, 22(3).
9. Arias A. A measure of earthquake intensity[M]//Hansen R J. Seismic Design for Nuclear Power Plants. Cambridge: MIT Press, 1970: 438-483.
10. Housner G W. Spectrum intensities of strong-motion earthquakes[C]//Proceedings of the Symposium on Earthquake and Blast Effects on Structures. Los Angeles, 1952.
11. Trifunac M D, Brady A G. A study on the duration of strong earthquake ground motion[J]. Bulletin of the Seismological Society of America, 1975, 65(3).
12. Park Y J, Ang A H-S, Wen Y K. Seismic damage analysis of reinforced concrete buildings[J]. Journal of Structural Engineering, 1985, 111(4).
13. Nuttli O W. The relation of sustained maximum ground acceleration and velocity to earthquake intensity and magnitude[R]. Miscellaneous Paper S-71-1, Report 16. Vicksburg: U.S. Army Engineer Waterways Experiment Station, 1979.
14. Sarma S K, Yang K S. An evaluation of strong motion records and a new parameter A95[J]. Earthquake Engineering & Structural Dynamics, 1987, 15(1): 119-132.
15. Von Thun J L, Roehm L H, Scott G A, Wilson J A. Earthquake ground motions for design and analysis of dams[C]//Earthquake Engineering and Soil Dynamics II. ASCE Geotechnical Special Publication 20, 1988: 463-481.
16. Bradley B A. Empirical equations for the prediction of displacement spectrum intensity and its correlation with other intensity measures[J]. Soil Dynamics and Earthquake Engineering, 2011, 31(8).
17. Rathje E M, Abrahamson N A, Bray J D. Simplified frequency content estimates of earthquake ground motions[J]. Journal of Geotechnical and Geoenvironmental Engineering, 1998, 124(2): 150-159.
18. Applied Technology Council. ATC 3-06 Tentative provisions for the development of seismic regulations for buildings[R]. 1978.
19. Numerical evaluation and experimental study of seismic performance of reinforced concrete bridge piers[D]. Dalian: Dalian University of Technology. Sect. 3.1.3, P-Δ effect.
20. Benjamin J R & Associates. A criterion for determining exceedance of the Operating Basis Earthquake[R]. EPRI Report NP-5930. Palo Alto, California: Electric Power Research Institute, 1988.(EDA)
21. Anderson J C, Bertero V V. Uncertainties in establishing design earthquakes[J]. Journal of Structural Engineering, 1987, 113(8): 1709-1724.(MIV)
22. Malhotra P K. Cyclic-demand spectrum[J]. Earthquake Engineering & Structural Dynamics, 2002, 31(7): 1441-1457.(DI, Ne)
23. ASTM. Cycle counting in fatigue analysis[S]. Annual Book of ASTM Standards, Vol. 03.01, Designation E1049-85, 1985.(rainflow counting)
24. Panella D S, Tornello M E, Frau C. A simple and intuitive procedure to identify pulse-like ground motions[J]. Soil Dynamics and Earthquake Engineering, 2017, 94: 234-243.(IP)
25. Bianchini M, Diotallevi P, Baker J W. Prediction of inelastic structural response using an average of spectral accelerations[C]//10th International Conference on Structural Safety and Reliability (ICOSSAR09). Osaka, 2009. (Sa,avg)
26. Campbell K W, Bozorgnia Y. Prediction equations for the standardized version of cumulative absolute velocity as adapted for use in the shutdown of U.S. nuclear power plants[J]. Nuclear Engineering and Design, 2011, 241: 2558-2569.(CAV_STD)
27. Guaman J W. Empirical ground motion relationship for maximum incremental velocity[D]. University of Notre Dame, 2010.(MIV)
28. Hancock J, Bommer J J. The effective number of cycles of earthquake ground motion[J]. Earthquake Engineering & Structural Dynamics, 2005, 34: 637-664.(DI, Ne)
29. 王慧东, 朱英磊. 桥梁墩台与基础工程[M]. 2版. 北京: 中国铁道出版社, 2017: 第5章及附录A、B.（Bridge Piers, Abutments and Foundations, 2nd ed.; chapter 5 and appendices A and B; pile foundation）
30. JTG 3362-2018 Code for Design of Highway Reinforced Concrete and Prestressed Concrete Bridges and Culverts[S].(elastic modulus of concrete)

---

## Appendix A　Units

| Quantity | Internal / output unit | Note |
| --- | --- | --- |
| Ground motion acceleration | g | Converted from Unit on import, $`g = 9.80665\ \mathrm{m/s^2}`$ |
| Velocity, displacement (parameters, spectra) | cm/s, cm | Integrated from acceleration |
| SA, PSA | g | |
| SV, PSV | cm/s | |
| SD | cm | |
| Code response spectrum | g | Period in s |
| Hysteresis displacement | mm internally; displayed in mm/cm/m/in | $`1\ \mathrm{in} = 25.4\ \mathrm{mm}`$ |
| Hysteresis force | kN internally; displayed in N/kN/kip | $`1\ \mathrm{kip} = 4.4482\ \mathrm{kN}`$ |
| Stiffness | force unit/displacement unit | e.g. kN/mm |
| Loop area (energy) | force unit·displacement unit | e.g. kN·mm |
| Pile foundation: force, length, stress | kN, m, kPa | Cap displacement in m (shown in mm in tables), rotation in rad |
| Pile foundation: stiffness | kN/m, kN·m/rad | Six-DOF springs and distributed springs |

## Appendix B　Context Menus

| Location | Menu item |
| --- | --- |
| Any figure | Save figure as image… |
| Any data table (Table tabs, characteristic values, etc.) | Export table data… |
| Response Spectrum list | Delete selected response spectrum, Export selected response spectrum |
| Amplitude Seismic Wave: Imported wave list | Delete selected wave, Export selected wave |
| Amplitude Seismic Wave: Target Accel list | Delete selected wave, Export selected wave |
| Wave Response Spectrum / Ground Motion Params: Imported waves list | Delete selected waves |
| Hysteresis Analysis / Skeleton Curve: Imported data list | Delete selected data |
| Hysteresis Correction: Characteristic values table | Export table data… |
| Test Curve Processing: Waiting segments list | Export table data… |
