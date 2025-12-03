# Changelog

## [1.1.0] - 2025-12-03

Stable release with mobile improvements and bug fixes.

### Added
- Mobile hamburger menu for cleaner header
- Floating action button for quick task creation on mobile
- Touch-friendly "Move to" action sheet (replaces drag-drop on mobile)
- Mobile context menu for task actions
- Upward column collapse direction on mobile

### Fixed
- Mobile columns now display full width
- Mobile task interactions (drag-drop and deeplinks)
- Desktop header button alignment
- Mobile context menu move task functionality

---

## [1.0.0] - 2025-12-02

First stable release of Craft Kanban - a beautiful, minimalist Kanban board for managing Craft tasks directly from your browser.

### Features

#### Views
- **Standard View**: 4 columns - Inbox | Backlog | Today | Future
- **7-Day Week View**: Full week layout with date columns (V key to toggle)
- **Focus View**: Distraction-free mode showing only today's tasks (F key)
- **Week Navigation**: Browse past/future weeks with navigation buttons

#### Task Management
- **Drag & Drop**: Move tasks between columns and dates
- **Quick Note**: Spacebar to append timestamped entry to daily notes
- **Add Task**: Shift+Space to create Inbox or Daily Note tasks
- **Inline Editing**: Click edit button to modify task content
- **Task Deletion**: Delete key or trash button
- **Task Type Toggle**: Choose between Inbox and Daily Note when creating

#### Column Features
- **Smart Columns**: Inbox, Backlog (overdue), Today, Future
- **Collapsible Columns**: Click headers to collapse/expand
- **Column Toggles**: 1-9, 0 keys to toggle individual columns
- **Smart Toggle**: A key to collapse all except Today
- **Auto-Expand on Drag**: Collapsed columns expand when dragging over

#### Mobile Support
- Responsive mobile layout with full-width columns
- Hamburger menu for mobile header controls
- Floating action button for quick task creation
- Touch-friendly "Move to" action sheet (replaces drag-drop on mobile)
- Mobile context menu for task actions
- Upward column collapse direction on mobile

#### Customization
- **Theme Support**: Dark and light mode
- **Week Start**: Sunday or Monday (configurable)
- **Custom Board Name**: Click title to rename

#### Keyboard Shortcuts
- `V` - Toggle views
- `F` - Focus mode
- `Space` - Quick note
- `Shift+Space` - Add task
- `1-9, 0` - Toggle columns
- `A` - Smart toggle all
- `?` - Show shortcuts reference

### Technical
- 100% client-side, no backend required
- Open source and fully auditable
- No tracking or analytics
- Local storage only for credentials
- Pure JavaScript, single HTML file

---

Built for the Craft Winter Challenge
