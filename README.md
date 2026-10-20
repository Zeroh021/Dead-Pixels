# DEAD PIXELS – Hand Gesture Zombie Shooter

A DOOM-style pixelated zombie shooter that runs in your browser and is played with **your webcam and hand gestures**. It uses a small raycasting engine rendered at 320×200 and scaled up with hard pixel edges, plus [MediaPipe Hands](https://developers.google.com/mediapipe/solutions/vision/hand_landmarker) for hand tracking. It is a single `index.html` with no build step.

## Controls

| Action | Hand gesture | Keyboard / mouse |
|---|---|---|
| Aim / track your shot | Point your **index finger** (the crosshair follows it) | Move the mouse |
| Change POV (turn) | Move your hand to the **left or right edge** of the frame | Mouse to the screen edges, or ←/→ |
| Fire | Make a **fist**, or point the finger-gun straight **up** | Click or Space |
| Reload | **Open palm** (all fingers extended) | R |
| Move | – | W A S D |

Notes:

- Firing happens once each time you close the fist, so open your hand and close it again for the next shot.
- Aim with your index finger out, then close your fist to shoot. The crosshair stays where you last aimed while you fire.
- The small camera preview (bottom-right) shows the detected hand points. The line under the game shows the recognised gesture: `GUN (aiming)`, `FIRE (fist / finger up)` or `RELOAD (open palm)`.

## Gameplay

- You start with 100 HP, an 8-round magazine and 32 spare rounds.
- Each zombie takes 3 hits. Zombies walk toward you, get faster each wave, and bite for 8 damage when they reach you.
- Clearing a wave starts a bigger one and gives you 16 more spare rounds.
- Reloading takes 1 second. When you die, click or press Enter to restart.

## Run locally

The browser only allows webcam access on **HTTPS or localhost**, so serve the file instead of double-clicking it:

```bash
git clone https://github.com/<your-user>/<your-repo>.git
cd <your-repo>
python3 -m http.server 8000
# open http://localhost:8000
```

Click **START WITH CAMERA**, allow webcam access, and keep your hand about 40–60 cm from the camera in good light. **START (KEYBOARD / MOUSE)** plays without a camera.

An internet connection is needed, because MediaPipe is loaded from the jsDelivr CDN.

## Deploy to a website (GitHub Pages)

1. Push the repo to GitHub with `index.html` in the root.
2. Go to **Settings → Pages**, choose the `main` branch and the root folder, and save.
3. Open `https://<your-user>.github.io/<your-repo>/`. GitHub Pages uses HTTPS, so the camera works.

Netlify, Vercel and Cloudflare Pages work the same way.

## How it works

- **Rendering:** a raycaster draws the walls, and zombies are drawn as pixel-art sprites that are hidden behind walls using a depth buffer.
- **Hand tracking:** MediaPipe Hands runs in your browser. The video is not uploaded anywhere.
- **Gestures:** a finger counts as extended when its tip is farther from the wrist than its middle joint. An open palm is four extended fingers, a fist is none, and the finger-gun is an extended index finger with the middle and ring fingers curled. "Pointing up" means the index tip is well above its knuckle.
- **Aim and turning:** the crosshair follows the mirrored position of your index finger. When it enters the outer 22% of the screen on either side, the view turns, faster the closer to the edge.
- **Hit detection:** when you fire, the crosshair position is checked against each zombie's on-screen box and the wall depth, and the nearest zombie in line is hit.

## Tuning

All of these are in `index.html`:

| What | Where | Default |
|---|---|---|
| Finger-extended sensitivity | `ext()` in `onHands()` | `1.1` |
| "Pointing up" strictness | `up=...<-.6` in `onHands()` | `-.6` (closer to 0 is easier) |
| Turn zone width | `E` in `upd()` | `.22` |
| Turn speed | `2.6` in `upd()` | `2.6` rad/s |
| Time between shots | `fireCd` in `shoot()` | `.28` s |
| Hand smoothing | `*.45` in `onHands()` | `.45` (lower is smoother but slower) |
| Map layout | the `M` array | 16×16 grid, `0` is floor and `1`/`2` are walls |

## Troubleshooting

- **No camera prompt or "unavailable" message:** open the page over HTTPS or `http://localhost`, and check the browser's camera permission for the site.
- **Fist doesn't fire:** show your whole hand to the camera and curl all four fingers fully.
- **Crosshair jumps or the points flicker:** improve the lighting and keep your hand against a plain background.
- **Hand tracking stays blank:** check that the page can reach `cdn.jsdelivr.net`.

## Credits and license

Hand tracking by Google [MediaPipe](https://github.com/google-ai-edge/mediapipe). Released under the MIT License.
