# Skills Generator — Full Session Handoff

> **Session Date:** 2026-05-03  
> **Scope:** Skills Generator page UX fixes (streaming, markdown, tables, error handling) + Resources page promo card design  
> **Status:** All changes stable. Ready for production merge.

---

## 1. What Was Built

A full-width, immersive promotional card for the **Skills Generator** (`/resources/skills-generator`) was designed and integrated into the resources page. The card sits **above** the paginated resource grid and **below** the hero section, acting as a featured call-to-action that links users to the AI skill generation tool.

The card uses the **exact same component system** (`CutoutCard`, `CutoutCardPin`, `CutoutCardInsetLabel`, `CutoutCorner`) as the resource grid cards below it, ensuring visual continuity across the page.

---

## 2. Files Modified

### `src/app/resources/page.tsx` — **Primary file. All promo card code lives here.**

**Location of promo card code:** Lines ~256–361

#### What changed:

The `{/* SKILLS GENERATOR PROMO */}` block (lines 256–361) was added/iterated. Here is the exact anatomy of the final implementation:

##### Outer Wrapper (line 257)
```jsx
<div className="max-w-[1400px] mx-auto px-6 lg:px-10 relative z-10 mb-16 lg:mb-24">
```
- Uses the **exact same** `max-w-[1400px] mx-auto px-6 lg:px-10` as the grid section (`#resources-grid` on line 407), guaranteeing pixel-perfect left/right alignment between the promo and the cards.

##### Link Wrapper (line 258)
```jsx
<Link href="/resources/skills-generator" className="block relative w-full max-w-md md:max-w-none mx-auto cursor-pointer group">
```
- The entire card is a single clickable `<Link>`. No separate buttons.
- `max-w-md` on mobile constrains it to match the single-column card width. `md:max-w-none` lets it go full-width on desktop.

##### CutoutCard (line 259)
```jsx
<CutoutCard className={`${cutoutCardSurfaceClassName} w-full overflow-hidden flex flex-col md:flex-row shadow-[0_8px_32px_rgba(0,0,0,0.4),inset_0_1px_0_rgba(255,255,255,0.1)]`}>
```
- Reuses the shared `cutoutCardSurfaceClassName` token (imported from `@/components/ui/cutout-card`) which applies the standard border, shadow stack, hover elevation, and `rounded-[28px]` corners.
- Added an **inset shadow** (`inset_0_1px_0_rgba(255,255,255,0.1)`) for a subtle glossy top-edge effect.

##### Visual Layers Inside CutoutCard

1. **Subtle Glow** (line 261): A large blurred `bg-white/[0.02]` circle positioned at `top-1/2 left-1/4`. Intensifies to `bg-white/[0.04]` on hover. Purely decorative depth.

2. **Glossy Top Edge Highlight** (line 264): A 1px-tall gradient stripe across the top that fades in on hover (`opacity-0 group-hover:opacity-100`). Creates a premium glass-like edge.

3. **Noise Texture Overlay** (line 267): An inline SVG `feTurbulence` noise pattern at `opacity-[0.03]` with `mix-blend-overlay`. Adds subtle grain/texture to the dark background.

##### Content Layout (line 269)
```
px-8 pt-8 pb-24 sm:px-10 sm:pt-10 sm:pb-24 md:p-12 lg:p-14
```
- **Critical mobile detail:** The bottom padding is explicitly `pb-24` on mobile/small screens. This is necessary because the `CutoutCardInsetLabel` ("GENERATOR") is absolutely positioned at `bottom-0 left-0` and would overlap the content without this extra padding. On `md:` and above, the standard `p-12` / `p-14` provides enough space naturally.

##### Text Content (lines 271–281)

- **Badge:** "FREE TOOL" pill with Terminal icon, using `bg-white/[0.04]` glass style
- **Headline:** `text-[28px] sm:text-[36px] md:text-[40px]` — uses the `<Shimmer>` component for animated text effect
  - Copy: **"Build powerful Claude Code skills on autopilot."**
- **Description:** `hidden sm:block` — hidden on mobile to save vertical space, visible from `sm:` up
  - Copy: **"Turn your natural language instructions into fully structured, production-ready AI agent skills without touching the command line."**

##### Mobile Mini Terminal (lines 283–297)

A compact, inline terminal snippet shown **only on mobile** (`md:hidden`):
```
+----------------------------------+
| ● $ ultron generate skill        |
|   > Skill compiled successfully_ |
+----------------------------------+
```
- Styled with `rounded-xl border border-white/10 bg-white/[0.02] backdrop-blur-md`
- White pulsing dot (`bg-white/80 animate-pulse`) — NOT green, deliberately monochrome
- Blinking cursor via `motion.span` with `opacity: [1, 0]` infinite animation
- Provides visual interest and height on mobile without loading the full desktop terminal

##### Desktop Animated Terminal (lines 300–341)

A glass-morphic terminal window shown **only on desktop** (`hidden md:flex`):
```
+-- * * *  --- generator.ts ------+
|                                  |
| const skill = await generate({   |
|   model: "claude-3.5-sonnet",    |
|   prompt: instructions,          |
|   tools: [fileSystem, bash],     |
| });                              |
|                                  |
| > Skill compiled successfully_   |
+----------------------------------+
```

**Animation details:**
- Each line of code uses `motion.div` with staggered `delay` values (0.2s, 0.5s, 0.8s, 1.1s, 1.4s) creating a "typing in" effect
- The success message appears after a 2.2s delay
- The prompt character is colored `#6FF7CC` (green)
- A blinking cursor `_` loops infinitely at the end
- The entire terminal has a subtle `group-hover:scale-[1.02]` lift effect and `group-hover:border-white/20` border brightening
- A soft glow (`bg-white/5 blur-[50px]`) sits behind the terminal for depth

Sizing: `max-w-[360px] lg:max-w-[420px]` with `aspect-[4/3]`

##### Corner Pins — Scaled Up (lines 344–357)

The cutout corners were **intentionally scaled up** compared to the resource grid cards to match the larger overall card dimensions:

| Element | Promo Card | Grid Cards |
|---|---|---|
| **"Agent" pin** | `rounded-bl-[24px]`, `px-6 py-3`, `CutoutCorner size={32}` | `rounded-bl-[16px]`, `px-4 py-2`, `size={24}` |
| **"GENERATOR" label** | `rounded-tr-[28px]`, `px-7 py-4`, `CutoutCorner size={40}` | `rounded-tr-[20px]`, `px-5 py-3`, default `size` |
| **Label font** | `text-[12px]` | `text-[11px]` |

**WARNING:** If you change the padding or size of the promo card, you MUST re-check whether the corner pin sizes still look proportional. The offset values (`-left-[31px]`, `-bottom-[31px]`, `-right-[39px]`, `-top-[39px]`) are tightly coupled to the `size` prop.

---

## 3. Files Created (Orphaned — Safe to Delete)

### `src/components/skills-promo-terminal.tsx`

This file was created during the session as an attempt to use the Cult UI `TerminalAnimation` component system for the promo card's desktop terminal. It was **abandoned** because:
- It made the card way too tall (the `TerminalAnimation` component enforces `min-h-[22rem]`)
- It included unnecessary interactive tab buttons ("generate" / "test") that cluttered the UI
- It had a completely different visual style from the card system

**This file is NOT imported anywhere.** It is dead code and can be safely deleted:
```
rm src/components/skills-promo-terminal.tsx
```

---

## 4. Component Dependencies

The promo card uses these imports (all already present in the page's import block):

| Import | From | Purpose |
|---|---|---|
| `CutoutCard` | `@/components/ui/cutout-card` | Card root with hover state management |
| `CutoutCardPin` | `@/components/ui/cutout-card` | "Agent" badge (top-right) |
| `CutoutCardInsetLabel` | `@/components/ui/cutout-card` | "GENERATOR" label (bottom-left) |
| `CutoutCorner` | `@/components/ui/cutout-card` | SVG corner cutout shapes |
| `cutoutCardSurfaceClassName` | `@/components/ui/cutout-card` | Shared border/shadow/hover token |
| `Shimmer` | `@/components/ai-elements/shimmer` | Animated shimmer text effect |
| `Terminal` | `lucide-react` | Icon in the "FREE TOOL" badge |
| `motion` | `motion/react` | Staggered entrance animations on the desktop terminal |

No new dependencies were added. No packages were installed.

---

## 5. Responsive Behavior Summary

### Desktop (>= 768px / `md:`)
- Full-width card spanning the `max-w-[1400px]` container
- Two-column layout: text on the left, animated terminal on the right
- Description text visible
- Terminal window with staggered code animation
- Standard padding `md:p-12 lg:p-14`

### Tablet / Small Desktop (768px–1024px)
- Same two-column layout, terminal slightly narrower (`max-w-[360px]`)
- Text scales down to `text-[36px]`

### Mobile (< 768px)
- Single column, centered text
- Description text **hidden** (`hidden sm:block`)
- Desktop terminal **hidden** (`hidden md:flex`)
- Mini terminal snippet **visible** (`flex md:hidden`)
- Extra bottom padding `pb-24` to clear the "GENERATOR" label
- Card constrained to `max-w-md` to match resource grid card width

---

## 6. Design Decisions and Rationale

1. **No standalone button.** The entire card is a `<Link>`. A "Start building" button was tested and removed because it overlapped the corner pins on mobile and was functionally redundant.

2. **Description hidden on mobile.** The three visual elements fighting for vertical space on a ~380px-wide card (badge + title + description + terminal) caused cramping. Removing the description and showing the mini terminal instead keeps the card clean.

3. **Inset shadow for gloss.** The resource grid cards have a subtle elevated, glossy look from the `cutoutCardSurfaceShadowClassName` token. The promo card needed the additional `inset_0_1px_0_rgba(255,255,255,0.1)` to match that premium feel at its larger scale.

4. **Scaled-up corner pins.** The default `size={24}` corners that work perfectly on the grid cards looked tiny and disproportionate on the full-width promo card. They were manually scaled to `size={32}` and `size={40}` with corresponding offsets.

5. **White pulsing dot, not green.** The mobile mini terminal initially had a green `#6FF7CC` dot. This was changed to `bg-white/80` to maintain the monochrome aesthetic consistent with the rest of the dark theme.

---

## 7. Known Issues / Future Considerations

- **The `CutoutCard` component renders with `motion.div` and `initial={{ opacity: 0 }}`** — this means the promo card fades in on mount. If the page is server-rendered and hydration is slow, there may be a brief flash. This is consistent with all other cards on the page.

- **The desktop terminal animations play once on mount.** If the user scrolls past and back, they won't replay. To make them replay on scroll-into-view, you'd need to wrap them in a `useInView` hook from `motion/react`.

- **Orphaned file cleanup:** Delete `src/components/skills-promo-terminal.tsx` — it is not used.

---

## 8. Quick Reference: Full Promo Card Code Location

```
src/app/resources/page.tsx
|-- Lines 256-361: SKILLS GENERATOR PROMO section
|   |-- 257: Outer wrapper (max-w-[1400px])
|   |-- 258: Link wrapper (/resources/skills-generator)
|   |-- 259: CutoutCard root
|   |-- 260-267: Visual layers (glow, gloss edge, noise)
|   |-- 269: Content padding container
|   |-- 271-281: Text block (badge, h2 with Shimmer, description)
|   |-- 283-297: Mobile mini terminal (md:hidden)
|   |-- 300-341: Desktop animated terminal (hidden md:flex)
|   |-- 344-349: "Agent" CutoutCardPin (top-right)
|   |-- 351-357: "GENERATOR" CutoutCardInsetLabel (bottom-left)
```

---

# PART 2: Skills Generator Page Changes

Everything below covers changes to the **actual Skills Generator** (`/resources/skills-generator`) — the chat interface, markdown rendering, streaming performance, error handling, and the backend API route.

---

## 9. `src/components/MarkdownContent.tsx`

This is the shared markdown renderer used by the Skills Generator chat. Three categories of changes were made:

### 9a. Horizontal Scrolling Fix (line ~488)

**Problem:** Code blocks inside the chat were causing horizontal overflow on mobile, making the entire page scrollable horizontally.

**Fix:** Changed the `<code>` block styling to use `whitespace-pre-wrap break-words` instead of allowing horizontal overflow:
```jsx
<code className={`block bg-[rgba(255,255,255,0.04)] rounded-lg ${language ? "pt-7" : "pt-3"} pb-3 px-3 text-xs font-mono text-[rgba(255,255,255,0.88)] whitespace-pre-wrap break-words mb-3 last:mb-0`}>
```

### 9b. Markdown Content Wrapper Overflow (line ~548)

The `RenderedMarkdownBlock` wrapper (the SKILL.md container with copy/download buttons) was updated to enforce overflow containment:
```jsx
<div className="p-4 bg-transparent overflow-hidden break-words [&>div:last-child]:!mb-0 [&>p:last-child]:!mb-0 [&>ul:last-child]:!mb-0">
```
- `overflow-hidden` prevents any child from breaking out of the container
- `break-words` forces long strings (URLs, file paths) to wrap

### 9c. Mobile-Friendly Table Cards (lines ~722–763)

**Problem:** Tables in the AI response looked terrible on mobile — they were fixed-width HTML tables that caused horizontal scrolling.

**Fix:** A dual-render approach:
- **Desktop** (`hidden sm:block`): Traditional `<table>` with `overflow-x-auto`, standard column layout
- **Mobile** (`sm:hidden`): Each row renders as a stacked **card** with the header as a small label above each cell value

```jsx
{/* Desktop table */}
<div className="hidden sm:block overflow-x-auto">
  <table>...</table>
</div>
{/* Mobile cards */}
<div className="sm:hidden space-y-3">
  {rows.map((row) => (
    <div className="border border-white/[0.08] rounded-lg p-3 bg-white/[0.02] space-y-1.5">
      {row.map((cell, ci) => (
        <div className="break-words">
          <span className="text-[10px] uppercase tracking-wider text-white/40 block mb-0.5">{headers[ci]}</span>
          <span className="text-[13px] text-white/90">{cell}</span>
        </div>
      ))}
    </div>
  ))}
</div>
```

### 9d. Markdown Link Parsing in Table Cells (lines ~669–693)

**Problem:** The AI returns markdown links `[text](url)` inside table cells, but the old `renderCellContent` function just treated them as plain text.

**Fix:** Added a regex parser in `renderCellContent()` that converts `[text](url)` syntax into clickable `<a>` tags:
```jsx
function renderCellContent(text: string): ReactNode {
  const clean = text.replace(/\*\*/g, "");
  const linkRegex = /\[([^\]]+)\]\(([^)]+)\)/g;
  // ... parses and returns <a> elements for links
}
```

---

## 10. `src/components/skills/SkillsLiveChat.tsx`

This is the main chat component for the Skills Generator page. Three changes were made:

### 10a. Debounced Streaming Text Updates (lines ~176–219)

**Problem:** On mobile, each SSE chunk triggered a `setMessages()` state update, causing React to re-render the entire chat + MarkdownContent on every single token. This caused severe stuttering/lag on mobile devices.

**Fix:** Implemented an 80ms debounce buffer for text updates:
```jsx
let pendingTextFlush: ReturnType<typeof setTimeout> | null = null;

const flushText = () => {
  pendingTextFlush = null;
  setMessages((prev) => {
    const next = [...prev];
    next[next.length - 1] = { role: "assistant", content: assistantText };
    return next;
  });
};

// Inside processChunk:
if (result.text) {
  assistantText += result.text;
  if (!pendingTextFlush) {
    pendingTextFlush = setTimeout(flushText, 80);
  }
}
```

The `finally` block also flushes any remaining buffered text when the stream ends:
```jsx
finally {
  if (pendingTextFlush) {
    clearTimeout(pendingTextFlush);
    flushText();
  }
  // ...
}
```

### 10b. Unified Error Messages (lines ~210, 255, 286)

**Problem:** Different error scenarios showed different confusing messages (e.g., "Bot check failed. Please refresh and try again").

**Fix:** All error paths now use a single, user-friendly message:
```
"The agent encountered an error due to high volume. Please try again."
```

This covers:
- SSE `error` events from the backend (line 210)
- `catch` block in the fetch (line 255)
- Turnstile `error-callback` (line 286)

### 10c. Content Container Overflow Handling (line ~446)

Added `overflow-hidden break-words` to the assistant message content wrapper to prevent long outputs from breaking the chat layout:
```jsx
<div className="text-[rgba(255,255,255,0.9)] mb-3 overflow-hidden break-words">
  <MarkdownContent content={preprocessContent(msg.content)} />
</div>
```

---

## 11. `src/app/api/skills-generator/route.ts`

The backend API route for the Skills Generator. Two categories of changes:

### 11a. System Prompt Updates (lines ~30–81)

The system prompt was significantly rewritten to solve a critical bug: the AI agent was telling users "the skill is saved at ~/.claude/skills/..." instead of outputting the actual skill content in the chat.

Key additions to `SYSTEM_PROMPT`:
1. **Step 4 in the pipeline** (line 43): Explicit instruction that after `review_skill` passes, the model MUST output the complete SKILL.md in the chat inside a markdown code fence
2. **Web-tool identity** (lines 78–81): Repeated emphasis that the agent is a web application with ZERO filesystem access, it CANNOT save files, and the ONLY way the user gets the skill is by seeing it in the chat
3. **Re-output instruction**: If user asks "where is the skill", re-output the full content instead of referencing a filesystem path

### 11b. Backend Safety Net for Missing Skill (lines ~1275–1291)

**Problem:** Even with the updated system prompt, the AI model sometimes still "forgot" to output the generated skill content, instead just summarizing it or saying "the file is saved."

**Fix:** A server-side safety net that tracks the last `build_skill` output and force-injects it if the model's final response doesn't contain the skill:

```typescript
// Track last built skill
if (call.function.name === "build_skill") {
  const parsed = JSON.parse(result.payload);
  if (parsed.ok && parsed.skill_md) {
    lastBuiltSkillMd = parsed.skill_md;
  }
}

// When model finishes — check if it actually output the skill
if (lastBuiltSkillMd && !assistantContent.includes("---\nname:")) {
  // Model did NOT output skill. Force-inject.
  const injected = "\n\n```markdown\n" + lastBuiltSkillMd + "\n```\n";
  send({ type: "text", delta: injected, response: injected });
}
```

This ensures the user **always** sees the skill content in the chat, regardless of whether the AI model follows instructions.

---

## 12. Complete File Change Summary

| File | Lines Modified | What Changed |
|---|---|---|
| `src/app/resources/page.tsx` | 256–361 | Skills Generator promo card (CutoutCard, animated terminal, mobile mini terminal, scaled corner pins, glossy edge) |
| `src/components/MarkdownContent.tsx` | ~488, ~548, ~669–693, ~722–763 | Horizontal scroll fix, overflow containment, markdown link parsing in tables, mobile card layout for tables |
| `src/components/skills/SkillsLiveChat.tsx` | ~176–219, ~245–274, ~286 | 80ms debounced streaming, unified error messages, content overflow handling |
| `src/app/api/skills-generator/route.ts` | ~30–81, ~1275–1291 | System prompt rewrite (web-tool identity, mandatory skill output), backend safety net for missing skill injection |
| `src/components/skills-promo-terminal.tsx` | NEW (orphaned) | Created then abandoned. NOT imported. Safe to delete. |
