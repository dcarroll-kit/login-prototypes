# Report Tab Menu: Design and Motion Spec

Source: Figma, *Mobile Player App* > page **Reports** > `Menu Scroll` component inside the `Menu3 - *` screens, with the `Mid3 - *` frames as animation keyframes.

This spec is shared by `UnderlineTabMenu.swift` (iOS, SwiftUI) and `UnderlineTabMenu.kt` (Android, Jetpack Compose). Both implementations expose every value below as a style property so they can be swapped for design tokens.

## Anatomy

```
 ┌──────────────── scroll viewport (346pt, inset 22pt each side on a 390pt screen) ────────────────┐
 │ [Attacking Threat] 8 [Defensive Actions] 8 [Discipline] 8 [Goals] 8 [Team Contribution]  ...   │  tabs, 34pt tall
 │                                                                                                 │  9pt gap
 │ ▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀────────────────────────────────────────────────────────────────────────────  │  4pt indicator on 1pt track
 └─────────────────────────────────────────────────────────────────────────────────────────────────┘
```

- The row scrolls horizontally. Track and indicator scroll with the tabs.
- The indicator spans the **label text** of the selected tab (tab width minus 8pt horizontal padding each side).

## Tokens

| Token | Value |
|---|---|
| Label font | Premier League, 16, Regular (unselected) / Bold (selected) |
| Label tracking | -0.32 |
| Label colour | `#37003C` |
| Track | 1pt, `#37003C` |
| Indicator | 4pt, `#37003C`, square ends, vertically centred on the track |
| Screen background | `#FCF7FC` |
| Tab height | 34pt |
| Tab padding | 8pt horizontal, 6pt vertical |
| Space between tabs | 8pt |
| Gap from tab bottom to indicator top | 9pt |
| Menu horizontal inset on screen | 22pt |

## Motion: "stretch and settle" indicator

Tapping a tab plays two phases back to back (total 460ms):

| Phase | What moves | Duration | Easing (Figma name / cubic-bezier) |
|---|---|---|---|
| 1. Stretch | Selected label switches to Bold immediately. The indicator grows to cover **both** the old and the new label (left edge = min of both, right edge = max of both). | 300ms | Ease in and out / `0.42, 0, 0.58, 1` |
| 2. Settle | The indicator contracts onto the new label only. | 160ms | Ease out / `0, 0, 0.58, 1` |

In Figma this is built as: tap → `Mid3` frame (Smart Animate 300ms ease in and out) → after 1ms delay → `Menu3` frame (Smart Animate 160ms ease out).

Behaviour rules:

1. **Interruptions.** A new tap during an animation cancels it and starts a new stretch from the interrupted target.
2. **Reduce Motion.** With iOS Reduce Motion on, the indicator moves straight to the new tab. On Android, Compose already honours the system *Animator duration scale* (0 = instant).
3. **Layout changes** (Dynamic Type / font scale, rotation, label width change from Regular to Bold) snap the indicator to the selected label while idle.
4. **Scroll into view** (addition, not in the prototype): the selected tab scrolls towards the centre of the viewport during phase 1. Can be turned off with `scrollsSelectedIntoView` / `scrollSelectedIntoView`.
5. **Accessibility.** Each tab is announced as a selectable tab with its selected state.
6. **Driving from content.** The menu is fully controlled by a `selection` value, so swiping a pager under it animates the indicator the same way. Both sample screens show this.

## Report tabs in the prototype

1. Attacking Threat
2. Defensive Actions
3. Discipline
4. Goals
5. Team Contribution
