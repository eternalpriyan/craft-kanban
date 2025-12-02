# Craft Kanban

A beautiful, minimalist Kanban board for managing your Craft tasks directly from your browser.

## Features

### Views
- **Standard View**: 4 columns - Inbox | Backlog | Today | Future
- **7-Day Week View**: Full week layout with date columns (V key or ☰/▦ button to toggle)
- **Focus View**: Distraction-free mode showing only today's tasks (F key to toggle)
- **Week Navigation**: Browse past/future weeks in 7-day view with ← → buttons and Today button

### Columns
- **Smart Columns**:
  - **Inbox** - Unscheduled tasks (collapsible)
  - **Backlog** - Overdue tasks (collapsed by default)
  - **Today/Dates** - Current and upcoming tasks
  - **Future** - Tasks beyond the current view
- **Collapsible Columns**: Click headers to collapse/expand
- **Column Toggles**: Use 1-9, 0 keys to toggle individual columns; A for smart toggle all
- **Auto-Expand on Drag**: Collapsed columns expand when dragging tasks over them

### Task Management
- **Drag & Drop**: Move tasks between columns and dates
- **Quick Note**: Spacebar - append timestamped entry to today's daily note
- **Add Task**: Shift+Space - create Inbox or Daily Note task with optional date
- **Inline Editing**: Click ✎ button to edit task content in place
- **Task Deletion**: Hover + Delete key, or click 🗑️ button
- **Task Type Toggle**: Choose between Inbox and Daily Note when creating tasks

### Customization
- **Theme Support**: Dark and light mode
- **Week Start**: Sunday or Monday (configurable in settings)
- **Custom Board Name**: Click title to rename
- **Smart Date Picker**: Quick buttons for Today, Tomorrow, Next Week

## Setup

1. Open `index.html` in your browser
2. Enter your Craft Share Link (from a Craft document with guest access enabled)
3. Enter your Space ID (found in your Craft URL)
4. Start managing your tasks!

## Craft API Limitations

This Kanban board works within the constraints of the Craft API. Understanding these limitations helps explain certain design decisions.

### 1. Task Block Movement Between Pages

**Limitation**: The API cannot move task blocks between different Craft pages (e.g., from a daily note to Inbox).

**Impact on Kanban**:
- ✅ **Works**: Creating tasks in Inbox (no date)
- ✅ **Works**: Scheduling Inbox tasks to any date
- ✅ **Works**: Rescheduling tasks between different dates
- ❌ **Doesn't Work**: Moving daily note tasks back to Inbox

**What Happens When You Try**:
1. Drag task from daily note to "Inbox" column
2. Kanban tries to clear the `scheduleDate` property
3. Task block **stays in original daily note** (can't move via API)
4. Task **disappears from all Craft scopes** (becomes "orphaned")

**Protection Added**:
- The Kanban now prevents you from dragging daily note tasks to Inbox
- A warning message will explain the limitation if you try
- This prevents accidental orphaning of tasks

**Best Practice**:
- Create tasks in Inbox first (leave date empty when adding new tasks)
- Schedule them to dates as needed
- **Backlog is for overdue tasks only** - it keeps the existing date
- If a daily note task is no longer needed, delete it rather than trying to move it to Inbox

### 2. Subtask Hierarchy Flattening

**API Behavior**: The `/tasks` API endpoint returns a flat list of ALL tasks, including both parent tasks and their subtasks as separate items. **No hierarchy metadata is provided** - there is no way to distinguish parent tasks from subtasks in the API response.

**Impact on Kanban**:
- Both parent tasks and their subtasks appear as separate cards on the board
- This creates visual duplication (the same work appears multiple times)
- There is no way to filter out subtasks or show hierarchy with the current API

**Why We Can't Filter Subtasks**:
- The API provides no `parentId`, `indentationLevel`, or hierarchy metadata in task objects
- Subtasks appear as standalone task objects, indistinguishable from top-level tasks
- The only way to discover the hierarchy is to fetch each task individually via `/blocks` endpoint and check its `content` array
- This would require N+1 API calls and is impractical for real-time use

**Current Approach**:
- The Kanban displays all tasks returned by the API (including subtasks)
- Click on any task card to open it in Craft and view the full hierarchy there
- Use the Kanban board for overall task visibility
- Manage detailed subtask relationships directly in Craft

**Note**: If Craft updates the `/tasks` API to include hierarchy metadata (like `parentId` or `indentationLevel`) in the future, we can implement subtask filtering or collapsible parent/child views.

### 3. Document Tasks Not Included

**API Scope**: The Daily Notes API only provides access to tasks in the **Inbox** and **Daily Notes**. Tasks created within regular Craft documents are not accessible.

**Impact on Kanban**:
- Only tasks from Inbox and Daily Notes appear on the board
- Tasks embedded in regular documents (projects, meeting notes, etc.) won't be visible
- This is by design - the API is specifically scoped to daily notes workflow

**Workaround**:
- Move important tasks from documents to Inbox or schedule them to daily notes
- Use Craft's native task views to see all workspace tasks
- The Kanban is best suited for daily/weekly task planning rather than comprehensive project management

**Technical Detail**: The `/tasks` endpoint returns tasks with `location.type` of either `inbox` or `dailyNote` only. There is no `document` location type in the API response.

## Understanding Inbox vs Daily Note Tasks

Craft has two fundamentally different task systems that work differently on this Kanban board:

### Inbox Tasks

**What they are**: Tasks created in Craft's central Inbox with no date assigned.

**How they work on the Kanban**:
- Live in the "Inbox" column
- When you drag them to a date column, their `scheduleDate` property is updated
- The task **stays in Craft's Inbox** but gains a date
- You can move them between any columns freely

**Label on the board**: No label in Inbox column | "Inbox / [date]" in other columns

**Best for**: Open-ended tasks, ideas, or things you'll tackle later

### Daily Note Tasks

**What they are**: Tasks or task blocks embedded inside daily note pages in Craft.

**How they work on the Kanban**:
- Live in their respective date columns (Monday, Tuesday, etc.)
- When you drag them between date columns, the **entire task block physically moves** to the other day's note
- Their `scheduleDate` property also updates to match
- **Cannot be moved back to Inbox** (API limitation - see above)

**Label on the board**: "Daily Note" in date columns | "Daily Note / [original date]" in Backlog/Future

**Best for**: Tasks tied to specific days, daily planning, recurring routines

### Why the Difference?

This asymmetry exists because Craft's API treats these as separate systems:
- **Inbox tasks** are centralized, date-able entities stored in the Tasks system
- **Daily Note tasks** are markdown blocks embedded in document pages (the Daily Notes)

The Kanban board respects this architectural split. Understanding it helps you work more intuitively with Craft.

### Best Practices

**Choose one workflow and stick with it:**

**Option 1: Inbox-Based Workflow** (Recommended for task management)
- Keep all tasks in Inbox and schedule them to dates from there
- Don't create tasks directly in daily notes
- Daily note tasks will be rare, making the "Daily Note" label useful when they do appear
- Tasks stay in your central Inbox until you delete them, creating a clear archive

**Option 2: Daily Note-Based Workflow** (Better for daily planning)
- Create tasks directly in your daily note pages
- Use the Kanban to see all daily notes and shuffle tasks between days
- Keep Inbox mostly empty (only for incoming items needing triage)
- Tasks are naturally scoped to the days they belong to

**Don't mix workflows:** Having tasks split between Inbox and Daily Notes creates confusion about where things live and how to move them around. Pick the approach that fits your brain and stick with it.

**Use the labels**: The "Inbox" and "Daily Note" labels on each card tell you exactly where to find it in Craft docs, which is especially helpful during the occasional cross-system task.

## Keyboard Shortcuts

### Views
- **V**: Toggle between Standard and 7-Day views
- **F**: Toggle Focus view (distraction-free, today only)

### Task Actions
- **Space**: Open quick note modal
- **Shift + Space**: Open add task modal
- **Enter**: Submit in modals (Shift+Enter for new line)
- **Escape**: Close modals
- **Delete/Backspace**: Delete hovered task (hover first)

### Column Controls
- **1-9, 0**: Toggle individual columns (left to right)
- **A**: Smart toggle - close all except Today, or open all

### Help
- **?**: Show keyboard shortcuts reference

## Tips

- Press **V** to toggle views, **F** for focus mode when you need to concentrate
- **Click column headers** to collapse/expand - great for reducing visual noise
- Use **A** key to quickly collapse everything except Today
- Leave due date empty when creating tasks to send them to Inbox
- **Backlog shows overdue tasks** - past-dated tasks appear here automatically
- Use quick date buttons (Today, Tomorrow, Next Week) for faster scheduling
- Drag tasks to "Future" column to trigger the date picker
- In 7-day view, use **← →** buttons to browse past/future weeks
- Press **?** anytime to see all keyboard shortcuts
- **Click ✎** on any task to edit its content inline

## Roadmap

### Phase 1: Web Enhancements
- **Tag-based Columns** - Dynamic columns based on #tags in task content
- **Month View** - Calendar grid showing task distribution across the month
- **Time Blocking** - Associate tasks with specific time slots within days

### Phase 2: Productivity Tools
- **Pomodoro Timer** - Built-in timer tied to active task
- **Time Tracking** - Start/stop timer per task with daily/weekly summaries
- **Project View** - Group tasks by source document

### Phase 3: External Integrations
- **Calendar Sync** - Show Google/Apple Calendar events alongside tasks

### Phase 4: Native Platform
- **iOS App** - Native app with widgets and notifications

## Support

This project was created for the Craft Winter Challenge. For issues or questions, please check the Craft documentation or community forums.

---

**Built with**: Pure JavaScript, Craft API, and a love for productivity tools
