# 仙人跳 · Fairy jump

Browser game with touch and keyboard controls. Hold to charge and release to jump.

## Sensing the void

Platforms are the patches of space the character has to devour through a black
portal. The platform ahead is therefore almost invisible at first — the character
does not know where it is yet. While you hold, the tile is gradually sensed and
fades in. It reaches full opacity at 72% of the charge that would land you dead
centre (`senseRevealAt` in index.html), i.e. before the optimal jump distance.
Charging further still makes the jump longer, but the tile no longer changes, so
the last part of the distance has to be judged by feel. No sound or flash marks
that moment — any "now!" cue would make the timing trivial.

The frame (`drawTargetFrame`) is three solid concentric diamonds around the *next*
platform. It is there from the first frame of the hold, grows continuously with
the charge, and keeps breathing outward (each ring pulses in size and brightness,
out of phase) for as long as the hold lasts — it never stops moving, so it cannot
be read as a "this is the moment" scale. It is drawn at that platform's depth, so
platforms in front of it occlude it correctly.

Tunables in `CFG`: `senseRevealAt` (where the fade tops out), `senseGhost`
(residual glow of an unsensed tile), `senseFarGhost` (platforms further ahead).

## Missing a jump

`jumpMax` (380) is deliberately larger than any platform gap, so a long charge
really does overshoot: the character leaves the tile. Too short and too long are
handled the same way — the target platform sweeps an extension toward the
character's feet (`prepareRescue`) and the character loses one life. Only once no
lives are left does the character fall into the void, for either kind of miss.
Platforms never move or teleport to the landing point.

The renderer also sorts the character by its real world depth while it is
falling, so a fall past a platform is occluded by it, while standing/landing
keeps the character in front of the platform it is on.

## Portal

The portal is a plain black ellipse — no halo, no energy arcs, no shockwave, no
particles around it, no wobbling edge. It is already half open while you charge
and then opens slowly to full size once the jump starts (`portalOpenMs`, 420 ms,
smoothstep), so the character begins sinking only once it is wide enough; it
closes again as the body is swallowed. The exit portal in
the sky grows open the same way before the character drops out of it. To use
hand-drawn art instead, drop a horizontal strip at `assets/portal-open.png` —
8 frames, 256x160 each (sheet 2048x160), PNG with alpha, ellipse centre at
(128, 96) of every frame and the portal ellipse 200x100 in the last frame. It is
drawn so that the ellipse covers the gameplay hole (124 px wide at full open);
anything outside the ellipse is shown as-is, so no baked glow is needed.

## Voice

Recorded clips live in `audio-bank.js` (groups: `jump`, `meow`, `idle`). Besides
the landing meow and the jump sound, the character talks on his own: a caution
line after an edge/rescue landing, a 30% chance of an idle line after a normal
landing (throttled to 12–18 s), and — when you stop playing for 8–15 s — a random
idle line. That last one is `RecordedAudio.tick()` driven; set `CFG.idleChatter`
to `false` to keep only the trigger-based lines.

Milestones on platforms 300 and 600 show completion and rescue messages. The message
and visitor disappear on the next valid jump; gameplay then continues.

## GitHub Pages

Upload these files to the root of a repository, preserving the assets directory.
In Settings > Pages, choose Deploy from a branch, main and /(root), then Save.
Use the published website link to play. Opening index.html in the repository shows source.

## Controls

- Mouse or touch: hold and release.
- Space: hold and release.
- M or the sound button: toggle sound.
- Landing hint button: toggle the optional landing indicator (it draws the
  predicted landing point directly, so it bypasses the sensing mechanic).

Best scores and landing-hint preferences are stored locally in each browser.
