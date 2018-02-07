The Little Details of UI Design
-------------------------------

_Steve Schoger, February 7, 2018, 11am EST_

### Intro

Most devs know good design when they see it, but don't know how to get them.

Steve will refactor (visually) an app called... SneakedIn.  XD

### Header

Top bar: Logo, search input, notifications, profile icon; bland grey.  Instead
of making the bar the brand color (too strong), try adding a bit of the brand
color to the grey.

Use HSL rather than RGB / HEX for better understanding of color relationships.

```
HSL
<--- SATURATION --->
^
|
|
(lightness)
|
|
v
(==================)
        HUE

HSB
<--- SATURATION --->
^
|
|
(brightness) <--- DIFFERENT
|
|
v
(==================)
        HUE
```

Use dribbble.com/colors to limit color choices and get ideas, by the way.

- White feels too bright for the input in the header; you can reduce opacity by
10%-20% or add a touch of a grey / near color.
- Use a consistent corner radius (border-radius: 4px).
- Search icon is 100% black (too harsh) - saturate with a bit of the background
color.
- Use a consistent icon set (entypo, material icons, zondicons, heroicons...)

Surround the profile icon at top right with a bit more space and borders, and
make it a dropdown; make the profile picture a circle rather than a square.

```
justify-content: space-around
```

### Activity Panel

Again, convert profile picture to circle; move profile completion percent to
the left, associating it more closely with the user / photo.

Labels are important, but shouldn't compete with values.  _Use font size to
emphasize information_.  **But limit your choices.**  How?

**Typographic scale.**  Create a hierarchy.

Classic (in pixels):

12 14 16 18 21 24 36 48 60 72

Tailwind (in pixels):

12 14 16 18 20 24 30 36 48

Tone down the 24px welcome text; all the little changes allow the right-hand
sidebar to be pushed up to the top of the page.

In addition to font size, you can use _color_ to create a hierarchy.  Once
again, **limit your choices**.  You can probably get away with three shades of
grey, for example.

- Use a consistent spacing scale. (0.375rem 0.75rem 1rem 1.25rem...)
- Use color to draw attention. (use brand color background, invert other colors)

**Saturate greys when using a colored background.**

Add a subtle drop shadow to the 'bottom line' values for a touch more emphasis.

**Offset box shadows.** (0 2 1px hsla(0, 0%, 0% 0.08);

### News Feed

**Go easy on link styles.** (dark, bold text is legible and looks clickable)

Color hierarchy again: Name (primary), job title (tertiary), body (secondary)

**Use contrast to create balance.** (Make icons lighter than text)

**Pick an appropriate line height.** (There isn't a one-size-fits-all)

**Use alignment to clean up design.** In this case, the top-left profile pics
in the feed 'want' to push things to the right.  Getting this right can help
allow for the removal of visual borders.

### New Post Section

There is a weird green button.  **Use button colors to create hierarchy.**  Use
a strong / bold / brand color for the primary action, an outline for secondary
actions, and text links for tertiary actions.

Remove the News Feed title.

'Progressive disclosure', icons for image / video in New Post to right of input
before 'activation'.

### Right-Hand Sidebar

Lots of little changes to make that section less attention-grabbing.  Replace
'add / 'follow' buttons with icons; remove borders, increase spacing slightly.

### Left-Hand Navigation

Update icons and make them lighter because of their weight vs. nav text.

Remove borders.  Remove the panel that makes the nav 'pop', so it sits a bit
more in the background.

### Full-Page

Change the background for the whole page to a subtle off-white to draw
attention; divide the nav links into groups with lighter, uppercase section
headings.  (Widen letter-spacing for all-caps)

### Finishing Touches

- Saturate all grays with a touch of color, but not the same intensity for
every shade
    - Note that it doesn't always work, but it's worth a trial
- Consider the temperature of the color (cool or warm)
- Fonts: (most on Google Fonts are not good for UI)
    - Tweak for Sans-Serif only on GF and 8+ styles
    - Alternatively TypeKit or similar choices if you have the budget
        - Proxima Nova
        - Aktiv Grotesk
        - Acumin Pro
        - Museo Sans

**Little Details Go a Long Way**.

www.refactoringui.com

