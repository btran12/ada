# Robot Eyes

A mobile-first interactive robot companion. Open `index.html` on a phone and interact with the glowing eyes.

## Included interactions

- Tap and drag: the eyes look toward your finger.
- Hold: curious, suspicious expression with a rising hum.
- Swipe: dramatic directional movement and excited beeps.
- Tilt and rotate: eyes follow gravity and play servo sounds.
- Moderate movement: alert animation.
- Shake: wobble, dizzy animation, vibration, and descending robotic tones.
- Idle: blinking, wandering, curious, happy, confused, and sleepy states.
- Microphone: loud sounds or claps startle the robot when permission is granted.
- Camera: supported browsers can detect a face, follow it, widen when it gets close, and search when it disappears.
- Battery: low battery makes the robot sleepy; charger changes trigger a power-up sequence.
- Returning to the app: distracted reaction.
- Audio: all beeps, chirps, servo tones, and startup sounds are synthesized with Web Audio.
- Reduced motion: system accessibility preferences are respected.

## Run and install

Serve the folder over HTTPS or localhost. Directly opening the HTML file supports touch, but browser security rules may block motion, camera, microphone, service-worker, and battery APIs.

Static hosts such as GitHub Pages, Netlify, Cloudflare Pages, or S3 + CloudFront work well. Once hosted, use the browser's **Add to Home Screen** action; `manifest.webmanifest` and `service-worker.js` provide the installable offline shell.

Camera face detection depends on the browser's `FaceDetector` API and is therefore optional. All other unsupported sensors degrade to the touch experience.


### TODO 
Yes — this would work very well as a **mobile-first interactive “robot eyes” web app**, almost like a little WALL-E-inspired creature living inside the phone.

I’d design it so the eyes are the entire interface: **black screen + two expressive glowing eyes + procedural robotic sounds**. No buttons unless they’re needed for permissions/settings.

### Core interactions

| Phone/User action         | Eye reaction                                       | Sound                                    |
| ------------------------- | -------------------------------------------------- | ---------------------------------------- |
| 📱 Shake                  | Eyes wobble, spin/dizzy, pupils lose focus         | Rapid `beep-beep-beep` + descending tone |
| 👆 Tap screen             | Eyes look toward tap                               | Small curious *boop*                     |
| 👆 Hold                   | Eyes become suspicious/curious and slowly approach | Rising electronic hum                    |
| 👈 Swipe left/right       | Eyes follow finger dramatically                    | Servo movement sounds                    |
| 🔄 Rotate phone           | Eyes tilt/roll with device                         | Mechanical servo                         |
| 📳 Vibration              | Eyes shake slightly                                | Tiny rattling beep                       |
| 📱 Tilt phone             | Eyes look toward the direction of gravity          | Soft servo sounds                        |
| 🤳 Move phone quickly     | Eyes become startled                               | "Alert" beeps                            |
| 📸 Camera sees a face     | Eyes look toward the face                          | Curious chirp                            |
| 😮 Face gets close        | Eyes widen                                         | Excited beeps                            |
| 👀 Face disappears        | Eyes search around for you                         | Searching beeps                          |
| 🔊 Clap/loud sound        | Eyes jump/startle                                  | Sharp beep                               |
| 🌑 Screen untouched       | Eyes slowly blink/look around                      | Occasional idle noises                   |
| 🔌 Plug/unplug charger    | Eyes react like they're being powered              | Startup/shutdown sequence                |
| 🔋 Low battery            | Sleepy eyes                                        | Slow warning beeps                       |
| 📞 Incoming notification* | Eyes become distracted                             | Alert animation                          |

*Depending on what browser/mobile OS APIs are available.

### The personality system

Rather than making each sensor event directly control an animation, I'd give the character an internal state:

```text
IDLE
 ↓
CURIOUS
 ↓
ALERT
 ↓
EXCITED
 ↓
CONFUSED
 ↓
DIZZY
 ↓
SLEEPY
```

Then sensors generate **events**:

```text
SHAKE
TAP
SWIPE
TILT
FACE_DETECTED
FACE_LOST
LOUD_SOUND
PHONE_MOVED
CHARGER_CONNECTED
```

For example:

```text
SHAKE
   ↓
DIZZY state
   ↓
eye rotation
pupil wandering
eyelid wobble
head/eye bounce
   ↓
robotic sound sequence
   ↓
recover
   ↓
CONFUSED
   ↓
back to IDLE
```

That will make it feel much more like a **character** rather than a collection of animations.

### Eyes

I'd make the eyes highly procedural rather than using GIFs.

For example:

```text
          ╭────────────╮       ╭────────────╮
          │            │       │            │
          │     ●      │       │      ●     │
          │            │       │            │
          ╰────────────╯       ╰────────────╯
```

But visually:

* soft glowing eyes
* pupils that move independently
* eyelids
* squinting
* widening
* asymmetric expressions
* blinking
* looking at touch points
* looking at detected faces
* shaking
* spinning
* sleepy half-closed eyes
* surprised wide eyes

**Canvas/WebGL** would be ideal for this.

### Robotic audio

Rather than playing prerecorded sounds, we can use the **Web Audio API** to synthesize a small vocabulary of robotic sounds:

```text
boop
beep
chirp
servo
whirr
click
buzz
startup
error
happy
confused
sleep
```

Then combine them.

For example:

```text
TAP

    eye movement
       ↓
  "beep... boop"
```

Shake:

```text
     BEEP!
       ↓
   beep-beep
       ↓
  WOOOOOOP
       ↓
   ...beep...
```

And idle behavior could randomly produce tiny sounds so the creature feels alive.

### One especially cool feature

**The eyes should notice when you're watching them.**

Using the front camera:

```text
No face
    ↓
Eyes wander around

Face detected
    ↓
Eyes suddenly stop

Face moves
    ↓
Eyes follow

Face gets closer
    ↓
Eyes widen

Face gets very close
    ↓
Eyes back away 😳
```

That could make the app feel surprisingly alive.

### Technical architecture

I'd build it as a PWA:

```text
React / TypeScript
        │
        ├── Eye Engine
        │      ├── pupils
        │      ├── eyelids
        │      ├── expressions
        │      └── animation
        │
        ├── Sensor Manager
        │      ├── accelerometer
        │      ├── gyroscope
        │      ├── orientation
        │      ├── touch
        │      ├── microphone
        │      └── camera
        │
        ├── Personality Engine
        │      ├── idle
        │      ├── curious
        │      ├── scared
        │      ├── dizzy
        │      └── sleepy
        │
        └── Robot Audio Engine
               ├── beeps
               ├── chirps
               ├── servo
               └── voice-like sounds
```

### I would also make it installable

The app could be opened from a URL and then **“Add to Home Screen”** so it behaves almost like a native app.

The home screen experience would simply be:

**black screen → eyes appear → tiny startup beep → character wakes up.**

Then it could remember a personality/state locally, so every time you open it, the eyes might react differently.

If you want, I can **build the first working version of this web app for you** — starting with the black background, expressive animated eyes, touch interaction, shake detection, tilt detection, and synthesized WALL-E-like robotic beeps.
