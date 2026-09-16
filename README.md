# Coco LiveDesk

[![HACS Custom](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://github.com/hacs/integration)
![version](https://img.shields.io/badge/version-1.2.1--coco-blue)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2023.1+-green)
![license](https://img.shields.io/badge/license-MIT-lightgrey)

Coco LiveDesk is an English-only Home Assistant dashboard companion based on [LiveDesk](https://github.com/doanlong1412/Live-desk) by `@doanlong1412`.

It keeps the original Live2D characters, sensor reactions, speech bubbles, TTS, mini mode and pin mode, while adding a full-dashboard foundation for Coco:

- Full dashboard and classic card layouts
- Separate motion, physical door, doorbell and smoke inputs
- Front-door and car-view camera panels
- Home status tiles for security, weather, temperature and humidity
- Recent event history stored locally in the dashboard browser
- Optional Home Assistant entities for AI-generated messages, event titles and priority
- Responsive desktop, tablet and mobile layouts
- English interface and English TTS defaults

The dashboard is only Coco's visual interface. Important whole-house TTS and phone notifications should be run by Home Assistant automations so they still work when this dashboard is closed.

## Preview branch

Development takes place on the [`coco-dev`](https://github.com/CtrlQuest/Live-desk/tree/coco-dev) branch. The `main` branch remains the original working copy until the Coco version is ready.

## Installation for testing

This is currently a development version. To test it through HACS:

1. Open HACS in Home Assistant.
2. Open the three-dot menu and choose **Custom repositories**.
3. Add `https://github.com/CtrlQuest/Live-desk` as a **Dashboard** repository.
4. Install the repository.
5. Make sure `/hacsfiles/Live-desk/livedesk.js` is loaded as a Lovelace JavaScript module.
6. Clear the browser cache or reload Home Assistant after updating the file.

HACS normally installs a release/default-branch version. While `coco-dev` is under active development, manual branch testing or a prerelease may be easier than replacing the stable `main` branch.

## Recommended full-dashboard setup

Create a dedicated Home Assistant dashboard view and enable **Panel (1 card)** for that view. Add this card as its only card:

```yaml
type: custom:live-desk
name: Ben
char_nickname: Coco
layout: fullscreen
height: 620
float_height: 600
float_width: 350
card_blur: 8

temp_sensor: sensor.downstairs_temperature
humid_sensor: sensor.downstairs_humidity
weather_entity: weather.forecast_home

front_door_motion_entity: event.front_door_motion
door_sensor: binary_sensor.front_door_door
doorbell_entity: event.front_door_ding

# Add this after ONVIF exposes a C510W motion/person binary sensor:
# car_motion_entity: binary_sensor.tapo_c510w_motion_alarm

front_door_camera: camera.front_door_live_view
car_camera: camera.tapo_c510w_live_view

tts:
  engine: webspeech
  lang: en-GB
  rate: 1.05
  pitch: 1.1
```

Replace any example entity IDs with the exact IDs from your Home Assistant instance.

## Optional AI result inputs

Home Assistant automations, AI Task or a local Ollama workflow can later write results to helper/template entities. Coco LiveDesk watches these entities and displays changes in the main message and Recent Events list:

```yaml
message_entity: sensor.coco_last_message
priority_entity: sensor.coco_priority
event_entity: sensor.coco_last_event
```

Supported priority states are `normal`, `info`, `activity`, `attention`, `security`, and `critical`.

Nested configuration is also accepted for compatibility with the planned Coco automation package:

```yaml
coco:
  message_entity: sensor.coco_last_message
  priority_entity: sensor.coco_priority
  event_entity: sensor.coco_last_event
```

## Configuration reference

| Key | Default | Description |
| --- | --- | --- |
| `layout` | `card` | `card` keeps the original layout; `fullscreen` enables Coco Home |
| `name` | `you` | Name used in greetings |
| `char_nickname` | character name | Character self-name, such as `Coco` |
| `height` | `440` card / minimum `520` fullscreen | Character area height |
| `float_height` | `650` | Floating character height |
| `float_width` | `400` | Floating character width |
| `card_blur` | `0` | Original card background blur from 0 to 30 |
| `temp_sensor` | none | Temperature sensor |
| `humid_sensor` | none | Humidity sensor |
| `weather_entity` | none | Weather entity |
| `front_door_motion_entity` | none | Front-door motion event entity or binary sensor |
| `car_motion_entity` | none | Car-view motion/person event entity or binary sensor |
| `door_sensor` | none | Physical open/closed door binary sensor |
| `doorbell_entity` | none | Doorbell/ding event entity or binary sensor |
| `smoke_sensor` | none | Smoke/fire binary sensor |
| `front_door_camera` | none | Camera shown in the Front Door panel |
| `car_camera` | none | Camera shown in the Car View panel |
| `message_entity` | none | Latest AI/Coco message |
| `priority_entity` | none | Priority for the latest AI/Coco message |
| `event_entity` | none | Title for the latest AI/Coco event |
| `toolbar_enabled` | `false` | Enables the original dashboard entity hover feature |
| `alert_tts_enabled` | `true` | Reads frontend sensor alerts while the card is loaded |

## TTS

The original browser and Home Assistant TTS engines remain available for testing. For reliable announcements on wall tablets and notifications on phones, use Home Assistant automations instead of relying on a browser tab.

```yaml
tts:
  engine: webspeech
  lang: en-GB
  rate: 1.05
  pitch: 1.1
```

Other supported engines are `google_translate`, `ha_service`, and `none`.

## Camera and privacy notes

- Camera images stay inside Home Assistant's authenticated camera proxy.
- Coco LiveDesk does not send camera images to an AI service.
- Vision analysis will be added later as a Home Assistant/Ollama workflow, with explicit camera selection and fallback alerts.
- Do not expose an unauthenticated Ollama server to the internet.

The legacy `motion_sensor`, `car_motion_sensor`, and `doorbell_sensor` keys remain supported for existing configurations.

## Credits and licence

Original LiveDesk code and design: [`doanlong1412/Live-desk`](https://github.com/doanlong1412/Live-desk).

Coco fork and Home Assistant integration work: [`CtrlQuest/Live-desk`](https://github.com/CtrlQuest/Live-desk).

Released under the existing [MIT licence](LICENSE). Original attribution has been retained.
