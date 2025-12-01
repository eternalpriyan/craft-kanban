# Craft Kanban

A beautiful, minimalist Kanban board for managing your Craft tasks directly from your browser.

## Features

- **Two Views**: Switch between Standard and 7-Day Week view with one-click toggle button (V key or ☰/▦ button)
- **Smart Columns**:
  - **Backlog** - Overdue tasks (collapsed by default to save space)
  - **Inbox** - Unscheduled tasks (collapsible)
  - **Today/Dates** - Current and upcoming tasks
  - **Future** - Tasks beyond the current view
- **Collapsible Columns**: Click column headers to collapse/expand Backlog and Inbox
- **Drag & Drop**: Easily move tasks between columns and dates
- **Quick Add**: Spacebar for quick notes, Shift+Space for tasks
- **Keyboard Shortcuts**: V to toggle views, Delete/Backspace to remove tasks
- **Theme Support**: Dark and light mode
- **Week Customization**: Start your week on Sunday or Monday (7-day view)
- **Smart Date Picker**: Quick buttons for Today, Tomorrow, and Next Week

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

## Keyboard Shortcuts

- **V**: Toggle between Standard and 7-Day views
- **Space**: Open quick note modal
- **Shift + Space**: Open add task modal
- **Enter**: Submit in modals (Shift+Enter for new line)
- **Escape**: Close modals
- **Delete/Backspace**: Delete hovered task
- **Hover over task**: Required before using Delete/Backspace

## Tips

- Press **V** or click the ☰/▦ button to quickly switch between Standard and 7-Day views
- **Click Backlog/Inbox headers** to collapse them and save screen space
- Leave the due date empty when creating tasks to send them directly to Inbox
- **Backlog shows overdue tasks** - tasks with past dates automatically appear here
- Use the quick date buttons (Today, Tomorrow, Next Week) for faster scheduling
- Drag tasks to "Future" column to trigger the date picker
- **7-Day view shows full day names** like "Sunday, 30th Nov" for better readability
- The view toggle also remains available in the settings menu (⚙️)

## Support

This project was created for the Craft Winter Challenge. For issues or questions, please check the Craft documentation or community forums.

---

**Built with**: Pure JavaScript, Craft API, and a love for productivity tools
