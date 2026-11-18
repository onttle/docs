---
title: 'Custom Themes'
description: 'How to change the colors, corner radius, font, density, logo, favicon and tab title of a Workspace CMS. Applies on app.onttle.com and on custom domains.'
category: 'core-features'
slug: 'custom-themes'
locale: 'en'
apiVersion: 'v1'
order: 9
updatedAt: '2026-11-03'
---

# Custom Themes

A custom theme makes a Workspace's CMS match **your team's or your customer's brand**. You set the colors, corner radius, font and screen density, plus the header logo, favicon and browser tab title. The saved theme applies both when the Workspace is opened on `app.onttle.com` and when it is reached through a [custom domain](/docs/core-features/custom-domains).

| Item | Details |
| ---- | ------- |
| Plan | **Pro and above** (on Free the menu is disabled and shows an upgrade notice) |
| Permission | Owner (OWNER) or Admin (ADMIN) |
| Location | **Workspace → Theme** |

---

## Presets

Selecting a preset previews it immediately. Changing a color or the radius yourself switches the theme to **Custom**, and your values are what gets saved.

| Preset | Description |
| ------ | ----------- |
| Onttle | The default theme: mostly black and white with square corners. |
| Minimal | Calm greys with slightly rounded corners |
| Dark | Dark background with a blue accent |
| Custom | Your own colors, radius, font and density |

---

## Colors and Style

### Colors

Light mode and dark mode colors are set separately. Use the color picker or type a hex value such as `#1d4ed8`.

| Token | Used for |
| ----- | -------- |
| Primary | Primary buttons, active states, emphasised links |
| Text on primary | Text placed on the primary color |
| Background | Page and card backgrounds |
| Text | Body text |
| Muted background | Sidebar, inactive areas, input backgrounds |
| Muted text | Helper text, disabled text |
| Border | Dividers and input borders |
| Accent background | Hover and selected-item backgrounds |
| Destructive | Delete buttons and error states |

If you leave the dark mode colors unset, the selected preset's dark colors are used. Check in the preview that the primary color and its text have enough contrast.

### Radius, Font and Density

| Setting | Options |
| ------- | ------- |
| Radius | None (square corners), Small, Medium, Large, Extra large. Applied proportionally to every element: buttons, inputs, cards, menus, popovers. |
| Font | System, Inter, Pretendard, Noto Sans KR |
| Density | Comfortable, Compact (smaller text and spacing) |

---

## Branding

| Item | Format | Shown in |
| ---- | ------ | -------- |
| Logo | PNG, JPG or WebP · up to 2 MB · landscape recommended | Header and login screen |
| Favicon | PNG 512×512 or ICO · up to 2 MB | Browser tab |
| App title | Up to 60 characters | Browser tab title (defaults to `Onttle` when empty) |

Removing the logo brings the Onttle logo back. SVG files cannot be uploaded.

---

## Saving and Where It Applies

- Changes take effect only after you click **Save**. Leaving the page with unsaved changes shows a confirmation.
- **Reset** returns to the last saved state. To go back to the default theme, select the **Onttle** preset and save.
- Saving applies immediately. Other members see the new theme on their next navigation or reload.
- On `app.onttle.com` the theme of the **Workspace you currently have open** is applied, and it switches when you switch Workspaces. The Workspace list, account settings and the `app.onttle.com` login screen use the default theme.
- On a [custom domain](/docs/core-features/custom-domains) the theme applies to every screen, including the login screen.

---

## When You Downgrade

On the Free plan the Theme menu is disabled, so the theme cannot be edited. The saved theme is kept and is used again once the Workspace is back on Pro or above.
