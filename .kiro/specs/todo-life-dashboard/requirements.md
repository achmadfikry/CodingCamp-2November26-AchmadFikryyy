# Requirements Document

## Introduction

The To-Do List Life Dashboard is a single-page web application that helps users organize their day. It displays the current time, date, and a contextual greeting, provides a Pomodoro-style focus timer, a full-featured to-do list, and a quick-links launcher. All data is persisted client-side using the browser's Local Storage API. The app is built with plain HTML, CSS, and Vanilla JavaScript — no frameworks or backend required.

## Glossary

- **Dashboard**: The single-page web application described in this document.
- **Greeting_Widget**: The UI section that displays the current time, date, and greeting message.
- **Focus_Timer**: The countdown timer widget that follows the Pomodoro technique (default 25 minutes).
- **Task_Manager**: The to-do list widget that allows users to add, edit, mark, and delete tasks.
- **Quick_Links**: The widget that displays user-defined shortcut buttons to external websites.
- **Local_Storage**: The browser's `localStorage` API used to persist tasks and links between sessions.
- **Task**: A single to-do item consisting of a text description and a completion status.
- **Link**: A user-defined shortcut consisting of a display name and a URL.

---

## Requirements

### Requirement 1: Time and Date Display

**User Story:** As a user, I want to see the current time and date at a glance, so that I always know what time it is without leaving the Dashboard.

#### Acceptance Criteria

1. THE Greeting_Widget SHALL display the current local time in HH:MM:SS 24-hour format, updating every 1000 milliseconds (±50 ms).
2. THE Greeting_Widget SHALL display the current date in the format "[Full Weekday Name], [Full Month Name] [Day], [Full Year]" (e.g., "Tuesday, January 27, 2026").
3. WHEN the Dashboard page loads, THE Greeting_Widget SHALL render the current time and date within 500 milliseconds, without requiring user interaction.
4. IF the browser environment does not provide a valid local time, THEN THE Greeting_Widget SHALL display a placeholder indicating time is unavailable.

---

### Requirement 2: Contextual Greeting

**User Story:** As a user, I want to see a greeting that reflects the time of day, so that the Dashboard feels personal and contextually aware.

#### Acceptance Criteria

1. WHEN the local hour is between 05:00 and 11:59, THE Greeting_Widget SHALL display the message "Good Morning".
2. WHEN the local hour is between 12:00 and 17:59, THE Greeting_Widget SHALL display the message "Good Afternoon".
3. WHEN the local hour is between 18:00 and 20:59, THE Greeting_Widget SHALL display the message "Good Evening".
4. WHEN the local hour is between 21:00 and 04:59, THE Greeting_Widget SHALL display the message "Good Night".
5. THE Greeting_Widget SHALL update the greeting message within 60 seconds whenever the displayed time crosses a greeting boundary, without a page reload.
6. WHEN the Dashboard first renders, THE Greeting_Widget SHALL immediately display the correct greeting based on the current local time.

---

### Requirement 3: Focus Timer — Countdown

**User Story:** As a user, I want a 25-minute countdown timer, so that I can use the Pomodoro technique to stay focused.

#### Acceptance Criteria

1. WHEN the Dashboard loads, THE Focus_Timer SHALL display an initial countdown value of 25:00 (MM:SS format).
2. WHEN the user activates the Start control, THE Focus_Timer SHALL begin counting down one second per second from the current displayed value.
3. WHILE the Focus_Timer is counting down, THE Focus_Timer SHALL update the displayed MM:SS value every second.
4. WHEN the countdown reaches 00:00, THE Focus_Timer SHALL stop automatically, emit an audio cue lasting 1–3 seconds, and retain the 00:00 display until the Reset control is activated.
5. WHILE the Focus_Timer is paused, WHEN the user activates the Start control, THE Focus_Timer SHALL resume countdown from the retained remaining time.
6. IF the user activates the Start control while the Focus_Timer is already running, THEN THE Focus_Timer SHALL ignore the activation and continue counting down without resetting or creating a duplicate interval.

---

### Requirement 4: Focus Timer — Controls

**User Story:** As a user, I want Start, Stop, and Reset controls for the timer, so that I can manage my focus sessions flexibly.

#### Acceptance Criteria

1. WHEN the user activates the Stop control while the Focus_Timer is running, THE Focus_Timer SHALL pause the countdown and retain the current remaining time.
2. WHEN the user activates the Start control after the Focus_Timer has been paused, THE Focus_Timer SHALL resume the countdown from the retained remaining time.
3. WHEN the user activates the Reset control, THE Focus_Timer SHALL stop any active countdown, restore the display to 25:00, and transition to the stopped state.
4. WHILE the Focus_Timer is running, THE Focus_Timer SHALL disable the Start control, enable the Stop control, and enable the Reset control.
5. WHILE the Focus_Timer is stopped or paused, THE Focus_Timer SHALL enable the Start control, disable the Stop control, and enable the Reset control.
6. WHEN the Dashboard loads, THE Focus_Timer SHALL display the Start control as enabled, the Stop control as disabled, and the Reset control as enabled.

---

### Requirement 5: Task Management — Add and Display

**User Story:** As a user, I want to add tasks and see them listed, so that I can track what I need to do today.

#### Acceptance Criteria

1. THE Task_Manager SHALL provide a text input field and an Add button for creating new tasks.
2. WHEN the user submits a non-empty task description (1–200 characters, excluding leading and trailing whitespace) via the Add button or the Enter key, THE Task_Manager SHALL append the new Task to the bottom of the task list with an unchecked completion status and clear the text input field.
3. IF the user submits an empty or whitespace-only task description, THEN THE Task_Manager SHALL reject the submission and display a validation message indicating that a task description is required, without clearing the input field.
4. THE Task_Manager SHALL render each Task with a checkbox, the task description text, an Edit button, and a Delete button.
5. IF the user enters a task description exceeding 200 characters, THEN THE Task_Manager SHALL prevent input beyond 200 characters and display a message indicating the character limit.

---

### Requirement 6: Task Management — Edit

**User Story:** As a user, I want to edit existing tasks, so that I can correct or update task descriptions without deleting and re-adding them.

#### Acceptance Criteria

1. WHEN the user activates the Edit button for a Task, THE Task_Manager SHALL replace the task description text with an editable input field pre-filled with the current description.
2. WHEN the user confirms the edit (via a Save button or the Enter key), THE Task_Manager SHALL update the Task description with the new non-empty text (1–200 characters, excluding leading and trailing whitespace) and restore the display to read-only mode.
3. IF the user confirms an edit with an empty or whitespace-only value, THEN THE Task_Manager SHALL retain the original task description and restore the display to read-only mode.
4. WHEN the user cancels the edit (via a Cancel button or the Escape key), THE Task_Manager SHALL discard the changes and restore the display to read-only mode.
5. IF the user confirms an edit with a description exceeding 200 characters, THEN THE Task_Manager SHALL prevent saving and display a validation message indicating the 200-character limit.

---

### Requirement 7: Task Management — Complete and Delete

**User Story:** As a user, I want to mark tasks as done and delete them, so that I can track my progress and keep the list clean.

#### Acceptance Criteria

1. WHEN the user toggles the checkbox for a Task, THE Task_Manager SHALL flip the Task's completion status from incomplete to complete or from complete to incomplete, and SHALL render the Task's text with strikethrough styling if and only if the Task is in the completed state.
2. WHEN the user activates the Delete button for a Task, THE Task_Manager SHALL permanently remove that Task from the task list so that it no longer appears in any filter view.
3. IF removing a Task from persistent storage fails, THEN THE Task_Manager SHALL display an error message indicating the deletion could not be completed and SHALL retain the Task in the task list unchanged.

---

### Requirement 8: Task Persistence

**User Story:** As a user, I want my tasks to be saved automatically, so that I don't lose my task list when I close or refresh the browser.

#### Acceptance Criteria

1. WHEN the task list changes (task added, edited, completed, or deleted), THE Task_Manager SHALL immediately save the complete task list to Local_Storage before the change is reflected in the UI.
2. WHEN the Dashboard loads, THE Task_Manager SHALL read the task list from Local_Storage and render all previously saved Tasks.
3. IF Local_Storage contains no task data (key absent or value is not a valid array), THEN THE Task_Manager SHALL initialize an empty task list and render it without error.
4. IF saving the task list to Local_Storage fails due to a quota exceeded error, THEN THE Task_Manager SHALL display an error message indicating storage is full and retain the in-memory task list unchanged.
5. IF the task list data retrieved from Local_Storage cannot be parsed as a valid task list, THEN THE Task_Manager SHALL discard the corrupted data, initialize an empty task list, and display a notification informing the user that previous task data could not be recovered.

---

### Requirement 9: Quick Links — Add and Display

**User Story:** As a user, I want to add shortcut buttons to my favorite websites, so that I can open them quickly from the Dashboard.

#### Acceptance Criteria

1. THE Quick_Links widget SHALL provide a link name input field accepting up to 50 characters, a URL input field accepting up to 2048 characters, and an Add Link button.
2. WHEN the user submits a non-empty link name and a valid URL via the Add Link button, THE Quick_Links widget SHALL append a new Link button to the links list within 200 milliseconds.
3. IF the user submits an empty link name, THEN THE Quick_Links widget SHALL reject the submission, preserve the entered values in the input fields, and display a validation message indicating that the link name is required.
4. IF the user submits a URL that does not begin with "http://" or "https://", THEN THE Quick_Links widget SHALL reject the submission, preserve the entered values in the input fields, and display a validation message indicating that the URL must begin with "http://" or "https://".
5. WHEN the user activates a Link button, THE Quick_Links widget SHALL open the associated URL in a new browser tab.
6. IF the links list already contains 20 Link buttons and the user activates the Add Link button, THEN THE Quick_Links widget SHALL reject the submission and display a validation message indicating that the maximum number of links has been reached.

---

### Requirement 10: Quick Links — Remove and Persistence

**User Story:** As a user, I want to remove links I no longer need and have my links saved automatically, so that the links list stays relevant and persists between sessions.

#### Acceptance Criteria

1. THE Quick_Links widget SHALL render each Link button with a remove (×) control that is focusable and activatable via both click and keyboard.
2. WHEN the user activates the remove control for a Link, THE Quick_Links widget SHALL immediately remove that Link from the links list without requiring confirmation.
3. WHEN the links list changes (link added or removed), THE Quick_Links widget SHALL save the complete links list to Local_Storage.
4. WHEN the Dashboard loads, THE Quick_Links widget SHALL read the links list from Local_Storage and render all previously saved Links.
5. IF Local_Storage contains no links data (key absent, non-array value, or unparseable data), THEN THE Quick_Links widget SHALL initialize an empty links list and render it without error.
6. WHEN the user removes the last remaining Link, THE Quick_Links widget SHALL render an empty links area without error and without leaving any orphaned UI elements.

---

### Requirement 11: Technology and File Structure Constraints

**User Story:** As a developer, I want the project to use only vanilla web technologies in a defined folder structure, so that the app is simple to understand, review, and deploy without any build toolchain.

#### Acceptance Criteria

1. THE Dashboard SHALL be implemented using only HTML, CSS, and Vanilla JavaScript, with no external frameworks, libraries, or third-party scripts loaded via CDN URLs or package managers.
2. THE Dashboard SHALL store all styling in a single CSS file located at `css/style.css`.
3. THE Dashboard SHALL store all JavaScript logic in a single JS file located at `js/app.js`.
4. WHEN loaded in the latest stable major version of Chrome, Firefox, Edge, or Safari, THE Dashboard SHALL render all UI elements and execute all user interactions without JavaScript console errors or missing resource errors.
5. THE Dashboard SHALL use the browser Local Storage API as the sole data persistence mechanism with no backend or server-side code.
6. IF the browser Local Storage API is unavailable or throws an access error, THEN THE Dashboard SHALL display an error message indicating that storage is unavailable and that data will not be saved.

---

### Requirement 12: Visual Design and Responsiveness

**User Story:** As a user, I want a visually clean and readable interface, so that the Dashboard is pleasant to use and easy to navigate.

#### Acceptance Criteria

1. THE Dashboard SHALL apply a distinct background (e.g., gradient or solid color) and white card panels for each widget to establish clear visual hierarchy.
2. THE Dashboard SHALL use typography where text-to-background contrast ratio meets a minimum of 4.5:1 for normal text and 3:1 for large text (18px or larger).
3. THE Dashboard SHALL lay out all widgets on a single page without requiring vertical scrolling on a standard desktop viewport (1280×800 or larger).
4. WHEN the user interacts with any button or input, THE Dashboard SHALL provide visual feedback within 100ms (e.g., hover state, focus outline, or active state styling).
5. WHEN the viewport width is between 768px and 1279px, THE Dashboard SHALL reflow widgets into a layout of no more than 2 columns without horizontal overflow or content clipping.
6. IF the viewport width is below 768px, THEN THE Dashboard SHALL stack all widgets in a single column with no horizontal overflow.
