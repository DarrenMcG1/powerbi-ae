# Spec: a Power BI report on Scotland's A&E waits, October to November 2026

The source of truth for what is due and when. Each step below is also a
block on Alfred's planner, in the Learning area, Monday to Wednesday
afternoons: in week 1 from 13:15 London time on the Monday and 13:45 on
the Tuesday and Wednesday (12:15 and 12:45 UTC), after the RoS practice
and lunch; from 2026-10-26, once the clocks have gone back, 13:00 to
15:00. Change a date here and the block with it.

## Why

Power BI is the one thing missing from about a dozen adverts in the
searches of 2026-10-12 and 2026-10-13, often "Power BI and DAX
essential". Your Power BI so far is one pick-rate report rebuilt from an
Oracle CSV export: charts only, no Power Query, no model, no DAX. This
project is one report you build yourself on public data, so that "talk
me through a Power BI report you built" has an answer you can give line
by line. It turns "missing: Power BI" into a partial match; it does not
meet "three years of Power BI", and is not meant to.

Costed and agreed 2026-10-14 (`~/projects/web/cv`,
`docs/notes/strategy.md`, "The Power BI gap, costed 2026-10-14"): about
18 hours, three afternoons a week from 2026-11-02, finishing Wednesday
2026-11-18. Brought forward by the owner on 2026-10-18 to start the next
day: the same nine steps from Monday 2026-10-19, finishing Wednesday
2026-11-04. Kaggle's November episode starts Sunday 2026-11-01, so week
3 shares its Monday to Wednesday with Kaggle's short first steps.

## The rules

1. **You write every line of DAX, and can read every line of M.** No
   coding agents, no Copilot, no code pasted from an assistant. Power
   Query's buttons write M for you, and using them is fine, as long as
   you can read each applied step aloud from the Advanced Editor and say
   what it does. DAX you type yourself. Asking an assistant to explain a
   concept is fine; asking it to write, fix or review a measure is not.
2. **A measure you have not checked does not go on a page.** Check each
   one against a number worked out another way: a row of the CSV, or a
   sum done by hand in LibreOffice Calc on the host. Write the check in
   `notes.md`. This is the question an interviewer asks of any
   dashboard: how do you know that number is right?
3. **Save as a Power BI project (`.pbip`), not only a `.pbix`.** A
   project saves the model and its M and DAX as text files, so each
   commit shows what you wrote that sitting. Save a `.pbix` copy at the
   end for anyone who wants to open it.
4. **Every sitting ends in a commit, pushed.** Commit from the VM, where
   the files are; pull on the host before a session edits this spec.
5. **The data is committed.** Public Health Scotland publishes it under
   the Open Government Licence, which allows it, as long as the README
   credits the source. A snapshot in git means the report rebuilds the
   same next year.
6. **The VM runs on the CPU only.** The box's GPU is shared by four
   services; give the VM no GPU passthrough. Power BI does not need it.

**How to use this list.** One step per sitting, in order; the day beside
each is the planner's afternoon. Tick a step here (`- [x]`) when its
"Done when" is true, and commit the tick with the work. **Read** is what
to read before you start the step, and **How** is the questions to
answer, never the code. If an interview takes a planned afternoon, the
step moves to the next free one and the order stays.

**When you are stuck for 45 minutes:** reread the step's Read; then
search the error or the function's name on <https://dax.guide/> or
Microsoft Learn; then ask an assistant to *explain the concept* (rule
1). Then write down where you are stuck in `notes.md`, commit it and use
the next catch-up hour (Tuesday 27 October or Tuesday 3 November) for it.

## Week 1: the environment, the data and the model

- [x] **Mon 19 Oct: the Windows VM** (~2 h)
  - Why a VM: Power BI Desktop runs only on Windows; Wine does not run
    it, and the browser version refuses a personal Gmail account. This
    machine has KVM (`/dev/kvm` exists, 32 threads, 186 GB of memory)
    but no virtualisation software installed, and 148 GB of disk free.
  - First, start the download, since it is about 6 GB: Windows 11
    Enterprise evaluation, the ISO, 64-bit, English (United Kingdom),
    from <https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise>.
    It asks for a name and email first. The evaluation lasts 90 days,
    which outlasts the project.
  - Read, while it downloads: the Arch wiki's libvirt page,
    <https://wiki.archlinux.org/title/Libvirt> (Installation, and
    Configuration's "Using libvirt group"), and its virt-manager page,
    <https://wiki.archlinux.org/title/Virt-manager>.
  - How:
    1. Install `qemu-desktop`, `libvirt`, `virt-manager`, `edk2-ovmf`,
       `swtpm` and `dnsmasq` with pacman; start `libvirtd.service`; add
       yourself to the `libvirt` group and log in again.
    2. In virt-manager, a new VM from the ISO: Windows 11, 8 CPUs,
       16 GB of memory, a 64 GB disk. Tick "Customize configuration
       before install" and check two things Windows 11 refuses to
       install without: the firmware is UEFI with secure boot (an
       `OVMF_CODE.secboot` file), and there is a TPM 2.0 device
       (emulated, which is what `swtpm` is for).
    3. Install Windows with a local account, not a Microsoft one (on
       the sign-in screen: Sign-in options, then "Domain join
       instead").
    4. In the VM: Power BI Desktop from
       <https://www.microsoft.com/en-us/download/details.aspx?id=58494>,
       and Git for Windows from <https://git-scm.com/download/win>.
    5. On the host: make this repository public on GitHub,
       `gh repo create DarrenMcG1/powerbi-ae --public --source ~/projects/learning/powerbi-ae --push`
       (your review gate runs first, as on any push). In the VM, clone
       it; Git for Windows signs in to GitHub through the browser.
    6. In Power BI Desktop: File, Options and settings, Options,
       Preview features. If "Power BI Project (.pbip) save option" is
       listed, turn it on, and "Store semantic model using TMDL format"
       if it is there too; if neither is listed, it is no longer a
       preview and is on already. Read:
       <https://learn.microsoft.com/en-us/power-bi/developer/projects/projects-overview>.
  - Done when: an empty report saved as `report/ae.pbip` in the clone,
    committed and pushed from the VM, and `git pull` on the host shows
    it. Add the two lines that page tells you to put in `.gitignore`
    (the local settings and the cache file) in the same commit.
- [ ] **Tue 20 Oct: Power Query** (~2 h)
  - Read: <https://learn.microsoft.com/en-us/power-query/power-query-what-is-power-query>,
    the tutorial
    <https://learn.microsoft.com/en-us/power-bi/connect-data/desktop-shape-and-combine-data>,
    and the M reference's landing page,
    <https://learn.microsoft.com/en-us/powerquery-m/>, enough to know
    what `let`, `in` and a step name are.
  - Data, three CSVs into `data/` (on the host or in the VM, then
    committed):
    - the latest "Monthly Accident and Emergency Activity" file from
      <https://www.opendata.nhs.scot/dataset/monthly-accident-and-emergency-activity-and-waiting-times>
      (about 40,000 rows, one per month, site, department type and
      attendance category, from July 2007);
    - `hospitals.csv` from
      <https://www.opendata.nhs.scot/dataset/hospital-codes>;
    - "Health Board 2014 - Health Board 2019" from
      <https://www.opendata.nhs.scot/dataset/geography-codes-and-labels>.
  - What the columns are: `Month` is the month as text, `YYYYMM`.
    `HBT` is the health board of treatment, a code; its name is in the
    health board file. `TreatmentLocation` is the site, a code; its name
    is in `hospitals.csv`. `DepartmentType` is `Type 1` (an emergency
    department) or `Type 3` (a minor injury unit or smaller site).
    `AttendanceCategory` is `All`, `Unplanned` or `New planned`, and
    `All` is the sum of the others. The `…All` columns cover every site
    since 2007; the `…Episode` columns cover only sites that send
    patient-level data, and the 8- and 12-hour waits exist only there.
    A `…QF` column is a qualifier flag: `z` means not applicable.
  - How:
    1. Load the three files. Which `AttendanceCategory` do you keep,
       and why? What goes wrong if you keep them all?
    2. Turn `Month` into a real date (the first of the month). Set
       every column's type. Remove the `QF` columns.
    3. How many `TreatmentLocation` codes find no row in
       `hospitals.csv`? Decide what the report shows for them, and
       write why in `notes.md`.
    4. The health board file has boards that no longer exist. Which
       rows do you keep?
    5. One query per table: the fact, the hospitals and the boards.
  - Done when: you have read every applied step of each query aloud
    from the Advanced Editor, and the fact query's row count matches
    the CSV's rows for the category you kept (count those in
    LibreOffice on the host), noted in `notes.md`.
  - Deeper dive, written 2026-11-03 after the first sitting on this
    step. Where to look and what to ask; no code.
    - A query here is the whole recipe for one table, not something you
      run against it. Each button adds an applied step, which reads like
      a chain of CTEs, each selecting from the one before. Home, then
      Transform data, opens the editor: the queries on the left, a
      preview in the middle, the applied steps on the right. View, then
      Advanced Editor, shows the M those steps wrote.
    - The preview and the column statistics cover the first 1,000 rows
      unless you change it: at the bottom left, set column profiling to
      the entire data set before you trust any count. View's Column
      quality, Column distribution and Column profile are your
      `SELECT DISTINCT` and `GROUP BY … COUNT(*)`.
    - Question 1: the profile counts rows, not attendances. On
      2026-11-03 it showed 40,227 rows: 19,022 `Unplanned`, about as
      many `All`, and roughly 2,200 `New planned`, because a site with
      no planned return visits has no row for them. To test "`All` is
      the sum of the others", filter to one site and one month and
      compare `NumberOfAttendancesAll` across its two or three rows; do
      it again for a site with no `New planned` row. Then: what would a
      sum over all three categories give? Remove the two filters
      afterwards, since they are applied steps too.
    - Question 2: `Month` is text such as `200707`. What is it missing
      that a date needs, before you change its type?
    - Question 3 is `NOT EXISTS` in SQL. Home, then Merge Queries, joins
      two queries; read its Join Kind list for the one that keeps only
      the rows with no match.
    - Question 4: find the column in the board file that says when a
      board stopped, and look at what its filter offers.
    - Question 5: the merge in question 3 makes a fourth query, only for
      checking. Right-click it: Enable load decides whether it reaches
      the model.
    - The row count for "Done when": after Close & Apply, the Table
      view shows each table's rows at the bottom.
    - Read: <https://learn.microsoft.com/en-us/power-query/merge-queries-overview>
      and <https://learn.microsoft.com/en-us/power-query/data-profiling-tools>.
- [ ] **Wed 21 Oct: the model** (~2 h)
  - Read: <https://learn.microsoft.com/en-us/power-bi/guidance/star-schema>,
    <https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-relationships-understand>,
    <https://learn.microsoft.com/en-us/power-bi/guidance/model-date-tables>
    and
    <https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-date-tables>.
  - How:
    1. A date table in DAX, one row per day, covering the data's
       months, with year, month, month name and financial year (the NHS
       year starts in April). Mark it as the date table.
    2. Relationships from the fact to the date table, the hospitals and
       the boards, one to many, filtering one way. Hide the codes on
       the fact.
    3. A hospital also belongs to a board. Do you relate the fact to
       the boards directly, or through the hospital? What goes wrong
       with both?
  - Done when: a table visual of attendances by board and year works,
    and you can draw the model on paper and say why each relationship
    filters the way it does.

## Week 2: DAX

- [ ] **Mon 26 Oct: measures and row context** (~2 h)
  - Read: <https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-quickstart-learn-dax-basics>,
    <https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-calculated-columns>,
    SQLBI's
    <https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/>,
    and <https://dax.guide/divide/>.
  - How:
    1. Measures: attendances, seen within 4 hours, over 4 hours, and
       the share seen within 4 hours. Why is that share a `DIVIDE` of
       two sums, and not an average of the CSV's percentage column?
    2. The same over 8 and 12 hours, from the episode columns. What do
       they show for a site that sends no patient-level data?
    3. One calculated column, and one measure using `SUMX`. Say, in
       `notes.md`, when a calculated column is the wrong choice.
  - Done when: the 4-hour share for one site and month matches that
    row's `PercentageWithin4HoursAll`, and Scotland's for August 2026
    matches your sum in LibreOffice.
- [ ] **Tue 27 Oct: CALCULATE and share of total** (~1 h, then an hour
  to catch up)
  - Read: <https://dax.guide/calculate/>,
    <https://dax.guide/removefilters/>, and SQLBI's
    <https://www.sqlbi.com/articles/understanding-context-transition/>.
  - How:
    1. Emergency department attendances only, with `CALCULATE` and a
       filter on department type.
    2. Each board's share of Scotland's attendances. What has to stop
       filtering, and what must not?
    3. Scotland's standard is 95% seen within 4 hours. Count the sites
       meeting it in a month, with your share measure inside a filter
       over the hospitals. Why does that work, when the same expression
       in a calculated column would not?
  - Done when: each measure checked (rule 2). The second hour: whatever
    is stuck, or <https://dax.guide/> on each function you used.
- [ ] **Wed 28 Oct: time intelligence** (~2 h)
  - Read: <https://learn.microsoft.com/en-us/dax/time-intelligence-functions-dax>,
    <https://dax.guide/sameperiodlastyear/>,
    <https://dax.guide/datesinperiod/> and <https://dax.guide/datesytd/>.
  - How:
    1. Attendances in the same month last year, the change, and the
       change as a share.
    2. A rolling 12 months.
    3. Financial year to date, the year ending 31 March.
    4. What does last year's figure return for July 2007, and what
       should the change show there?
  - Done when: the change for one board and month checked by hand
    against two rows of the CSV.

## Week 3: the report and telling it

- [ ] **Mon 2 Nov: the overview page** (~2 h)
  - Read: <https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-tips-and-tricks-for-creating-reports>
    and <https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-slicers>.
  - How: write the page's one question at its top first (for example:
    is Scotland meeting the 4-hour standard, and where not?). Then the
    cards, a line over time against the 95% line, the boards compared,
    and slicers for date and department type. Every number on it is a
    measure you checked.
  - Done when: someone who has not seen it can answer the page's
    question from it in a minute.
- [ ] **Tue 3 Nov: the site page and the PDF** (~1 h, then an hour to
  catch up)
  - Read: <https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-drillthrough>
    and <https://learn.microsoft.com/en-us/power-bi/collaborate-share/end-user-pdf>
    (its Power BI Desktop section).
  - How: a page for one site, reached by drill-through from the
    overview's boards or a list of sites. Then export both pages to
    `report/ae.pdf`.
  - Done when: right-clicking a site drills through to it, and the PDF
    is committed. The second hour: whatever is stuck.
- [ ] **Wed 4 Nov: the write-up and saying it aloud** (~2 h)
  - How: in `README.md`, in your own words: the question, the data and
    its credit, a screenshot of the model, each measure and what it
    answers, how you checked them, and what went wrong. Save a `.pbix`
    copy as `report/ae.pbix`.
  - Done when: you have explained the whole report aloud in three
    minutes without notes, as if an interviewer asked "talk me through
    a Power BI report you built", and the commit is pushed.

## After it

A session in `~/projects/web/cv` decides what the BI CV's skills line
and its self-directed section may claim from this, once you have told it
aloud. Until then the CVs say nothing new about Power BI.
