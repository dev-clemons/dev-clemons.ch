---
layout: page
title: keytrack
description: Track which keyboard keys are currently pressed in the browser.
importance: 3
---

keytrack is a small npm package that lets you ask whether a key is currently held down in the browser. In JavaScript, you only get `keydown` and `keyup` events as they happen, so if you want to know the current state, you have to keep track of it yourself. For games or anything else that checks the keyboard in a loop, keytrack does that for you.

I wrote it for [WebTigerPython](https://webtigerpython.ethz.ch/), where I needed to port the functions `isKeyPressed` and `isKeyCodePressed` from TigerJython. You can read more about it in the [blog post]({% post_url 2026-11-13-keytrack %}).

## Usage

Install it with `npm install keytrack`. You turn tracking on with `logKeys(true)`, and then you can query for the keys, e.g. in a game loop:

```js
import { logKeys, isKeyPressed } from "keytrack";

logKeys(true);

function loop() {
  if (isKeyPressed("ArrowLeft")) player.x -= 1;
  requestAnimationFrame(loop);
}
loop();
```

`isKeyPressed` checks the `key` of the event, e.g. `"A"` or `"ArrowLeft"`. `isKeyCodePressed` checks the deprecated numeric `keyCode`, which keytrack gets from the `code` with a mapping table for the US keyboard layout. It is mainly there for compatibility with TigerJython; for new code, `key` might be the better choice. With `logKeys(false)` you turn tracking off again.

Note that it only works while the browser window is focused, since browsers only send key events to the focused page.

## Demo

<style>
  .kt-demo { border: 1px solid var(--global-divider-color, #ddd); border-radius: 8px; padding: 1rem; }
  .kt-demo p { margin: 0 0 .75rem; }
  .kt-wrap { display: flex; gap: 1rem; flex-wrap: wrap; align-items: flex-start; }
  .kt-canvas { flex: 1 1 260px; max-width: 100%; aspect-ratio: 16 / 10; border: 1px solid var(--global-divider-color, #ccc); border-radius: 6px; cursor: pointer; outline: none; }
  .kt-canvas:focus { border-color: var(--global-theme-color, #b509ac); }
  .kt-state { font: .85rem monospace; line-height: 1.7; margin: 0; padding: 0; list-style: none; }
  .kt-state .kt-on { color: var(--global-theme-color, #b509ac); font-weight: bold; }
</style>
<div class="kt-demo">
  <p>Click the box, then hold the arrow keys. Hold two at once to move diagonally. Every frame, the loop just asks <code>isKeyPressed(...)</code>; there is no key event handling in the demo itself.</p>
  <p><small>This demo was written by Claude.</small></p>
  <div class="kt-wrap">
    <canvas class="kt-canvas" id="kt-canvas" width="480" height="300" tabindex="0" aria-label="keytrack demo: move the dot with the arrow keys"></canvas>
    <ul class="kt-state" id="kt-state"></ul>
  </div>
</div>
<script type="module">
  import { logKeys, isKeyPressed } from "https://cdn.jsdelivr.net/npm/keytrack@0.1.2/dist/index.js";

  const canvas = document.getElementById("kt-canvas");
  const ctx = canvas.getContext("2d");
  const state = document.getElementById("kt-state");
  const keys = ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"];

  logKeys(true);

  // stop the arrow keys from scrolling the page while the demo is focused
  canvas.addEventListener("keydown", (e) => {
    if (keys.includes(e.key)) e.preventDefault();
  });

  const dot = { x: canvas.width / 2, y: canvas.height / 2, r: 12 };
  const speed = 220; // pixels per second
  let last = performance.now();

  function frame(now) {
    const dt = Math.min((now - last) / 1000, 0.05);
    last = now;

    if (isKeyPressed("ArrowLeft")) dot.x -= speed * dt;
    if (isKeyPressed("ArrowRight")) dot.x += speed * dt;
    if (isKeyPressed("ArrowUp")) dot.y -= speed * dt;
    if (isKeyPressed("ArrowDown")) dot.y += speed * dt;
    dot.x = Math.max(dot.r, Math.min(canvas.width - dot.r, dot.x));
    dot.y = Math.max(dot.r, Math.min(canvas.height - dot.r, dot.y));

    const color = getComputedStyle(document.documentElement).getPropertyValue("--global-theme-color").trim() || "#b509ac";
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = color;
    ctx.beginPath();
    ctx.arc(dot.x, dot.y, dot.r, 0, Math.PI * 2);
    ctx.fill();

    state.innerHTML = keys
      .map((k) => {
        const on = isKeyPressed(k);
        return `<li${on ? ' class="kt-on"' : ""}>isKeyPressed("${k}") → ${on}</li>`;
      })
      .join("");

    requestAnimationFrame(frame);
  }
  requestAnimationFrame(frame);
</script>

## Links

- [Blog post]({% post_url 2026-11-13-keytrack %})
- [npm](https://www.npmjs.com/package/keytrack)
- [GitHub](https://github.com/dev-clemons/keytrack)
- MIT
