# Office Portfolio

A 3D, third-person portfolio. You walk a character around a single office room;
each prop (desk, plant, bookshelf, whiteboard…) is a doorway to one of my projects.

Built as a **single self-contained `index.html`** using [Three.js](https://threejs.org)
from a CDN — no build step, no dependencies to install. It's served as a static page
(e.g. GitHub Pages).

## Controls

- **W A S D** / arrow keys — walk
- **Drag** the mouse (or **← / →**) — orbit the camera
- Touch: bottom-left of the screen acts as a movement joystick; drag elsewhere to look
- Walk up to a prop until its label turns blue, then press **E** or click it to open the linked project

## How to run locally

Just open `index.html` in a browser. (It needs internet the first time to fetch
Three.js from the CDN.) Or serve it:

```bash
python -m http.server 8000
# then visit http://localhost:8000
```

## How to extend it

Everything lives in `index.html`.

### Map a prop to one of your projects

Edit the `LINKS` map near the top of the script:

```js
const LINKS = {
  desk:  'https://github.com/<you>/<repo>',
  plant: 'https://github.com/<you>/materiality-map',
  shelf: 'https://...',
  board: 'https://...'
};
```

If a link is left as a placeholder, clicking the prop just shows a banner.

### Add a new prop

Add one `addProp(...)` call. The signature is:

```js
addProp(id, label, mapsToCaption, labelHeight, collisionBox, buildFn)
```

- `id` — must match a key in `LINKS`
- `label` — the floating text above the prop
- `mapsToCaption` — small caption shown in the "opening" banner
- `labelHeight` — how high (in metres) the label floats
- `collisionBox` — `{minx, maxx, minz, maxz}` the character can't walk through
- `buildFn(group, meshes)` — draw the prop with `part(group, meshes, geometry, material, x, y, z)`

Example — a coffee mug on a side table:

```js
addProp('mug','About me','say hello',1.1,
  {minx:2.4,maxx:3.6,minz:-1.6,maxz:-0.4}, (g,m)=>{
    part(g,m,new THREE.BoxGeometry(1,0.8,1), mat(0x8a6b4f), 3,0.4,-1);          // table
    part(g,m,new THREE.CylinderGeometry(0.15,0.15,0.25,16), mat(0xffffff), 3,0.9,-1); // mug
});
```

Then add `mug: 'https://...'` to `LINKS`. The glow, label, proximity detection,
and click-to-open all work automatically.

### Beautify over time

Because each prop draws itself in its own `buildFn`, you can swap crude primitives
for nicer geometry, add floor/wall textures, lighting, an intro screen, or load real
`.glb` models — without touching the movement, camera, or interaction code.
