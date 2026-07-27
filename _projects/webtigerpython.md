---
layout: page
title: WebTigerPython
description: A low-floor, high-ceiling Python IDE for the browser.
img: assets/img/webtigerpython-icon.png
importance: 0
related_publications: true
---

A browser-based Python IDE for education, supporting everything from Turtle graphics and educational robotics to advanced libraries like NumPy and Matplotlib. Presented at SIGCSE TS 2026 {% cite bachmann2026webtigerpython %}.

Try it live: [webtigerpython.ethz.ch](https://webtigerpython.ethz.ch)

<iframe
  id="wtp-iframe"
  src="https://webtigerpython.ethz.ch"
  allow="usb;clipboard-write"
  style="height: 500px; width: 100%; border: 0; border-radius: 8px;"
></iframe>

<script>
document.getElementById('wtp-iframe').addEventListener('load', function () {
  this.contentWindow.postMessage({
    type: 'files',
    data: [{
      name: 'main.py',
      data: "import turtle\nt = turtle.Turtle()\nt.speed(0)\nfor i in range(60):\n    t.forward(150)\n    t.right(122)\n"
    }]
  }, '*');
});
</script>
