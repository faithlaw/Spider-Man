# Spider-Man
Random inspiration after watching Spider-Man: Brand New Day

## Spidey Drop 🕸️
A single HTML page where you upload any picture, and it drops down from the top of the browser on a "web thread" then swings like a pendulum. Click it anytime to give it a push.

## How the code works (for beginners like me)
Every webpage is built from three layers stacked together:

- **HTML** — the structure (what's on the page: text, images, buttons)
- **CSS** — the styling (colours, sizes, positions — how it looks)
- **JavaScript** — the behaviour (what happens when you click, what moves, what changes over time)

***This file has all three in one document.***

### 1. HTML — the skeleton

```html
<div id="upload-bar">
  <label>Pick your Spidey (or anyone): <input type="file" id="file-input" accept="image/*"></label>
</div>
```

Think of **`<div>`** as an invisible box you can put things in and later style or move around. This box holds a label and a **file picker button** (`<input type="file">`) — the "Choose File" button. `accept="image/*"` tells the browser to only allow image files.

```html
<div id="rig">
  <div id="web-thread"></div>
  <div id="spidey-wrap">
    <img id="spidey-img" src="" alt="Spidey">
  </div>
</div>
```

This is the important part:

- **`#rig`** is a box that holds everything that swings together: the thread and the image. Rotating this one box rotates both, like a real pendulum arm.
- **`#web-thread`** is a plain empty box that gets stretched downward with JavaScript to look like a thin web line.
- **`#spidey-wrap`** holds the actual picture (`<img>`), which starts empty (`src=""`) until you upload one.

The `id="..."` on each element is just a name tag.

### 2. CSS — the styling

```css
#rig {
  position: absolute;
  top: 0;
  left: 50%;
  width: 0;
}
```

This says: "Take the box with `id="rig"`, and..."

- **`position: absolute`** → place it exactly where I want
- **`top: 0; left: 50%`** → pin it to the top of the screen, at the horizontal midpoint
- **`width: 0`** → an invisible vertical line down the center of the screen

```css
#spidey-wrap {
  position: relative;
  left: -60px;
  width: 120px;
}
```

The image is 120px wide. If we didn't shift it, its **left edge** would sit exactly on the center line, meaning the whole image would hang off to the right. **`left: -60px`** nudges it back half its own width, so the middle of the image sits on the center line instead — this is what keeps the web thread aligned no matter the image.

**`transform-origin: top center`** tells the browser: "when you rotate this box, pivot from the top-center point"
### 3. JavaScript — the behaviour

**a) Reading the uploaded file**

```javascript
fileInput.addEventListener('change', (e) => {
```

This means: "watch the file-picker input, and when its value changes (the user picked a file), run this code."

```javascript
const reader = new FileReader();
reader.onload = (evt) => {
  img.src = evt.target.result;
```

A **`FileReader`** is a built-in browser tool that converts your uploaded image file into a long text string (a "data URL") that an **`<img>`** tag can display directly — no server needed. **`reader.readAsDataURL(file)`** starts that conversion, and once it's done, **`reader.onload`** fires and sets that result as the image's `src`.

**b) Animating the drop**

```javascript
function drop() {
  if (pos < dropHeight) {
    pos += dropSpeed;
    thread.style.height = pos + 'px';
    requestAnimationFrame(drop);
  }
}
```

**`requestAnimationFrame`** is how you make smooth animations in a browser — it says "run this function again right before the next screen refresh" (usually 60 times per second). This function keeps calling itself, each time growing the thread's height a little (`dropSpeed`), until it reaches `dropHeight`. The "falling" effect is really just a box getting taller, very fast, many times a second.

**c) The pendulum swing**

```javascript
function pendulumStep() {
  const angleRad = angle * Math.PI / 180;
  const angularAccel = (-gravity / (length / 100)) * Math.sin(angleRad);
  angularVel += angularAccel;
  angularVel *= damping;
  angle += angularVel;
  rig.style.transform = `rotate(${angle}deg)`;
  requestAnimationFrame(pendulumStep);
}
```

A tiny physics simulation, updated 60 times a second:

- **`angle`** — how far the pendulum is currently tilted
- **`angularVel`** — how fast the angle is currently changing
- **`angularAccel`** — how much gravity pulls it back toward center right now
- **`damping`** — a number just under 1 (0.998) that shaves a tiny bit of speed off every frame, mimicking air resistance, so the swing gradually loses energy and settles instead of swinging forever

Each frame: gravity nudges the velocity, velocity gets slightly damped, velocity changes the angle, and the angle is applied with `rig.style.transform = rotate(...)`. Sixty times a second, that adds up to smooth, natural-looking swinging.

**d) Clicking to push**

```javascript
wrap.addEventListener('click', (e) => {
  angularVel += (Math.random() > 0.5 ? 1 : -1) * 4;
});
```

**`addEventListener('click', ...)`** means "run this code whenever this element is clicked."
A click adds a burst of speed (`4`) in a random direction, chosen by flipping a virtual coin with `Math.random()`.

## Ideas to build on later
- Add a web-shoot sound effect on click
- Cycle through multiple uploaded images
- Build an Electron version so it floats over your *entire* desktop instead of just the browser tab
