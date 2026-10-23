# Functional Specification: Classroom Seating Charts

- **Status:** Initial draft
- **Audience:** Teachers and product/design stakeholders
- **Purpose:** Describe expected product behavior without selecting a technical implementation.

## 1. Overview

The app helps teachers create and manage seating charts for students in their classes. A teacher maintains class rosters and classroom layouts, creates charts by placing students into seats, and uses seating constraints to guide placement. The app can suggest a placement and lets the teacher review and edit it. It identifies constraint violations while leaving the final decision with the teacher.

Teachers can manage multiple classes, optionally keep a student's seat consistent across selected classes, and revisit previously saved charts.

## 2. Goals and boundaries

### Goals

- Make it practical to create a chart from a class roster and a teacher-defined classroom layout.
- Use student, seat, and student-to-student constraints when suggesting placements.
- Make placements and constraint violations understandable and editable.
- Keep class information, constraints, layouts, and saved charts available for later use.
- Allow consistency of a student's seat across classes when the teacher opts in.

### Not specified in this draft

- Authentication, account administration, integrations, roster formats beyond CSV, or collaboration between teachers.
- A particular placement algorithm, optimization objective, or guarantee that all constraints can be satisfied.
- Exact distance or adjacency semantics for “near” and “far.”
- Technical architecture, database schema, hosting, or quantified performance targets.

## 3. Actors

- **Teacher:** Creates classes, maintains rosters and classroom layouts, configures constraints, generates and edits charts, reviews warnings, and consults chart history.
- **Student:** A person represented in a class roster and assigned a seat. A student does not need to use the app directly in the initial scope.

## 4. Use cases

### UC-1: Set up a class and roster

1. The teacher creates or opens one of their classes.
2. The teacher adds students to the roster, edits their roster details, or removes students who are no longer in the class.
3. The app makes the current roster available for chart creation.

**Outcome:** The class has a current roster that can be assigned to seats. CSV is the initial import format; the required fields and import workflow remain open.

### UC-2: Model a classroom layout

1. The teacher creates or edits a layout to reflect the room.
2. The teacher adds, removes, and positions seats and may label or group seats.
3. The teacher can associate relevant specifications with seats or groups, such as a location or seating characteristic.
4. The app makes the layout selectable when creating a chart.

**Outcome:** The teacher has a reusable visual representation of available seats and any relevant seat groupings/specifications.

### UC-3: Create a seating chart

1. The teacher chooses a class and a classroom layout.
2. The app uses the roster, student-specific constraints for its students, and class-scoped constraints to suggest student-to-seat placements.
3. The app reports students who could not be assigned and any detected constraint violations.
4. The teacher reviews the result and can edit it before saving.

**Outcome:** A chart is available for review, with incomplete assignments and known violations visible rather than silently ignored.

### UC-4: Configure class-scoped constraints

1. The teacher adds, edits, or removes constraints for a class.
2. Class-scoped constraints may describe class-specific seating preferences or whether students should be near or separated from one another.
3. The teacher may use seat locations, groups, or specifications from the classroom layout as part of a constraint.
4. The app applies the class's current constraints when suggesting or checking a chart, in addition to any student-specific constraints that apply to rostered students.

**Outcome:** Class-specific constraints inform placement and can be checked against the resulting chart. The exact constraint types, priority levels, and proximity rule are open questions.

### UC-5: Record a student's seating needs

1. The teacher records or updates a seating requirement or preference on the student's record, rather than on an individual class.
2. The app applies that student-specific constraint whenever the student is on a class roster and a chart for that class is created or checked.
3. The teacher can review the student-specific constraint and the charts or placements to which it applies.

**Outcome:** Student-specific needs, including seating needs related to an IEP or 504 plan, follow the student across the teacher's classes where they are rostered. They do not require the student to retain the same seat across classes; same-seat consistency is a separate opt-in choice. The app should store the seating requirement needed for placement, not require a diagnosis or detailed plan.

### UC-6: Review and override a constraint violation

1. The teacher reviews a chart with a detected violation.
2. The app identifies the affected student or students, placement, and whether the violated constraint is student-specific or class-scoped.
3. The teacher changes the placement or chooses to proceed with the chart despite the violation.
4. The app retains the teacher's final chart.

**Outcome:** Violations are visible and actionable, but the app does not prevent the teacher from proceeding. Whether to record a reason for an override is undecided.

### UC-7: Keep a student's seat consistent across selected classes

1. The teacher opts selected students and classes into same-seat consistency.
2. When creating a chart for an opted-in class, the app attempts to keep the student in the same corresponding seat.
3. If the seat is unavailable or conflicts with the current layout or constraints, the app identifies the conflict for the teacher to resolve.

**Outcome:** Consistency is optional and scoped to teacher-selected students/classes; incompatible placements are surfaced rather than silently changing the saved preference.

### UC-8: Save and revisit chart history

1. The teacher saves a chart for a class.
2. The teacher can view previously saved charts and select one to inspect.
3. When creating a later chart, the teacher can consult prior charts.

**Outcome:** Prior charts remain available for reference. Whether the app should actively discourage duplicate arrangements, and how it should compare charts, remains open.

### UC-9: Reject a proposed chart and generate another

1. After reviewing a generated chart, the teacher decides not to use it.
2. The teacher can request another chart directly, without first saving or providing a reason for rejecting the current one.
3. Alternatively, the teacher can add or adjust a constraint and then request another chart.
4. The app generates a new proposal using the applicable current constraints and presents it for review.

**Outcome:** The teacher can iterate quickly, with or without changing constraints. Generating another proposal does not itself save or approve it; a previously saved chart is not silently overwritten.

### UC-10: Add a temporary seating constraint

1. The teacher adds a constraint that is intended to apply temporarily, such as separating two students after a recent disagreement.
2. The teacher specifies when the constraint should apply; it may be a limited period or have an end date.
3. The app applies the constraint to chart generation and checking while it is active, alongside the student's persistent and class-scoped constraints.
4. After the active period ends, the app no longer applies the temporary constraint to new charts. The teacher can also remove it earlier.

**Outcome:** A temporary situation can influence placement without becoming an indefinite constraint. The exact ways to set a duration, whether it applies to one class or multiple classes, and how to review expired constraints remain to be defined.

## 5. Functional requirements derived from the use cases

- **FR-1 Classes and rosters:** A teacher can manage multiple classes and maintain a roster for each. Teachers can import roster data from a CSV file. Imported student records include a unique Student ID used to recognize the same student across classes.
- **FR-2 Layouts:** A teacher can create and edit a visual room layout with seats, seat labels/groups, and seat specifications.
- **FR-3 Class-scoped constraints:** A teacher can maintain constraints for a class, including class-specific student-seat placement and student-to-student relationships.
- **FR-4 Student-specific constraints:** A teacher can maintain seating needs on a student's record; those constraints apply in every class where that student is rostered.
- **FR-5 Suggested placement:** The app can propose placements using the selected class roster, layout, student-specific constraints, and class-scoped constraints.
- **FR-6 Assignment coverage:** The app indicates whether every rostered student has a seat and whether any seats remain unused.
- **FR-7 Violation reporting:** The app identifies detected violations, the affected placement and constraint, whether the constraint is student-specific or class-scoped, and whether a temporary constraint is active.
- **FR-8 Teacher control:** The teacher can manually edit placements and proceed despite reported violations.
- **FR-9 Cross-class consistency:** The teacher can opt selected students/classes into same-seat consistency; conflicts are surfaced. This is separate from student-specific constraints, which follow the student regardless of seat.
- **FR-10 Chart history:** The teacher can save and inspect prior charts for a class.
- **FR-11 Regeneration:** A teacher can request another chart from a proposal without saving it, either directly or after adding/editing constraints. New proposals use currently applicable constraints, including active temporary constraints.
- **FR-12 Temporary constraints:** A teacher can create a time-bounded constraint, review it, and remove it early. The app applies it only while active when generating or checking charts.
- **FR-13 Change handling:** Changes to a roster, layout, or any constraint must be reflected when a chart is next generated or checked. How saved charts respond to later edits is an open question; the app should not silently rewrite historical charts.

## 6. Storage concepts

The product needs to retain the following information. This is a conceptual inventory, not a proposed database design:

- Teacher-owned classes and each class's roster.
- Student records imported from a CSV roster file, with:
  - A unique Student ID used to identify that student across classes and associate student-specific constraints. Student IDs, rather than names, are the identity key.
  - First and last name, and middle name if available.
  - Birthday/date of birth as a candidate field; whether it is necessary to collect remains to be confirmed.
  - Student-specific seating constraints that apply across the teacher's classes where the student is rostered.
- Ongoing class-scoped constraints, kept distinct from student-specific constraints, and optional cross-class seat-consistency choices.
- Temporary constraints, their applicable scope (student or class), and their active period or end date.
- Classroom layouts, including seats, their positions, labels, groupings, and specifications.
- Saved seating charts, including the class, layout, student-to-seat assignments, and enough context to review the chart and its reported violations later.

CSV is the initial roster import format; additional formats may be supported in the future. The exact CSV columns, validation, and import workflow remain to be defined. The spec also does not yet decide whether layouts are shared between classes, whether charts are versioned, or how long records are retained. The Student ID is assumed to be stable and unique across the teacher's classes.

## 7. UI and interaction requirements

- **Supported platform:** The app is intended for desktop computers: PC/Windows is the initial platform, with Mac support planned. Mobile and tablet support are not part of the initial target.
- **Class list/detail:** Find and manage multiple classes and their rosters, including importing roster data from CSV and viewing student names and Student IDs.
- **Layout editor:** Visually arrange seats to match the classroom and edit seat labels, groupings, and specifications.
- **Constraint management:** Add and review class-scoped constraints for an individual class and student-specific seating needs on the student's record, with clear indication of where each applies.
- **Chart workspace:** Choose a class and layout, request a suggested chart, view student/seat assignments, and manually change placements.
- **Regenerate actions:** From a proposed chart, offer a direct “Generate another chart” action and an “Add a constraint and generate another” path. The teacher can specify whether a new constraint is temporary or ongoing where relevant.
- **Violation presentation:** Make affected students, seats, and violated constraints apparent; provide a clear way to continue after review.
- **History:** Browse and inspect saved charts for a class.

The UI should preserve a teacher's ability to make the final placement decision and be designed for desktop use, including precise layout editing with a mouse or trackpad and clear presentation on typical PC and Mac screens. Detailed interaction design, accessibility criteria, and supported screen sizes need further definition.

The distribution model (for example, browser-based or installed desktop software) is not yet specified.

## 8. Important edge cases

- More students than available seats, or more seats than students.
- A student has no eligible seat under the active constraints.
- Constraints conflict with one another or with a cross-class seat preference.
- A student-specific seating need conflicts with the layout or a class-scoped constraint in one or more classes.
- A temporary constraint starts or expires near chart generation, or a chart is checked after its constraint has expired.
- A teacher generates several proposals without saving them, or a new constraint makes an earlier proposal out of date.
- A student is added, removed, or moved between classes after charts have been saved.
- A layout is changed after it has been used by a saved chart.
- A teacher proceeds with a chart containing violations or unassigned students.

In each case, the app should make the condition visible and avoid presenting an incomplete or conflicting placement as fully compliant.

## 9. Privacy considerations

Some student-specific seating needs may be associated with sensitive educational or accessibility information, including accommodations. The app should support expressing and applying the seating requirement across the student's classes without requiring the teacher to store a diagnosis or detailed accommodation record. Student IDs and birthdays/date of birth are also identifying information; the necessity of collecting a birthday should be confirmed and unnecessary personal data should not be required. Access, retention, and applicable policy requirements need to be determined before implementation.

## 10. Open questions

1. What columns, validation, and workflow should CSV roster import use? CSV is the initial format; other formats may be considered later. Imported records are assumed to include a unique Student ID used across the teacher's classes, first and last name, and an optional middle name. Is birthday/date of birth necessary, or should it be omitted?
2. What does “near” or “far” mean in irregular room layouts, and should teachers define that relationship?
3. Are student-specific and class-scoped constraints preferences, requirements, or both? How should the app prioritize conflicts while still allowing teacher overrides? Which constraint scopes may be temporary?
4. What should the app do when it cannot assign every student without violating constraints?
5. Should an override require a reason, and should the app preserve a record of overrides?
6. How should same-seat consistency map across different layouts, and should it be available for a whole class or selected students?
7. Should the app actively avoid repeating prior arrangements, or only make chart history available?
8. Are layouts shared across a teacher's classes? What should happen to saved charts when a roster or layout changes?
9. What student information, chart history, and retention rules are appropriate for the intended school context?
10. What accessibility and desktop screen-size requirements should guide the UI?
11. Should the PC and Mac versions be browser-based or installed desktop software?
12. Should rejected/unsaved chart proposals be retained for later comparison, or replaced when another proposal is generated?
13. Should temporary constraints use an end date, a number of classes/sessions, or another duration model? Should they apply to a single class or across classes, and how should expired constraints be shown? When reviewing an older chart, should a temporary constraint be evaluated as it was at chart creation or by its current active status?
