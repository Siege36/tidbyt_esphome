# 📺 Tidbyt ESPHome Display

An ESP32-powered RGB LED matrix display inspired by the Tidbyt, integrated into Home Assistant. It features dynamic scrolling text, album art visualization, and real-time weather/time synchronization.

---

## 🛠 Home Assistant Services

### `esphome.tidbyt_update_media`
Use this service to push metadata from your media players (Spotify, Apple Music, etc.) to the display.

| Field | Type | Description |
| :--- | :--- | :--- |
| `title` | string | The song name (auto-capitalized on device). |
| `artist` | string | The artist name (auto-capitalized on device). |
| `album` | string | The album name. |
| `image_path` | string | URL for the 17x17 album art. |
| `is_playing` | boolean | If `true`, switches display to "Now Playing" mode. |

---

## 🎨 Available Entities

### Color Choosers (Light Entities)
Each line of text is exposed as an RGB light. Adjust the color wheel in Home Assistant to change the display in real-time.

* **light.track_title_color**: The scrolling song title.
* **light.artist_color**: The scrolling artist name.
* **light.album_color**: The scrolling album name (and Weather summary).
* **light.clock_color**: The HH:MM digital clock.
* **light.weather_color**: The current temperature text.
* **light.alert_color**: The full-screen emergency alert text.

### Controls & Sensors
* **select.display_mode**: Switch between `Clock`, `Weather`, and `Now Playing`.
* **number.brightness**: Controls the global intensity of the LEDs (0-255).
* **number.tidbyt_scroll_speed**: Controls text crawl speed (ms per pixel).
* **switch.display_power**: Master toggle to black out the screen.
* **text.alert_message**: Enter text here to override the screen with a scrolling alert.
* **binary_sensor.tidbyt_mode_touch**: Reflects the state of the physical touch sensor.

---

## 🔌 Hardware Pinout (ESP32 DevKit V1)



| Matrix Pin | ESP32 GPIO | Function |
| :--- | :--- | :--- |
| **R1 / G1 / B1** | 5 / 23 / 4 | Top Panel Data |
| **R2 / G2 / B2** | 2 / 22 / 32 | Bottom Panel Data |
| **A / B / C / D** | 25 / 21 / 26 / 19 | Row Selection |
| **LAT / OE / CLK**| 18 / 27 / 15 | Latch / Enable / Clock |
| **Touch Sensor** | 33 | Mode Toggle |
| **I2S Audio** | 13 (LRCLK), 12 (BCLK), 14 (DOUT) | Speaker Output |

---

## 🤖 Automations

### Spotify Sync Example
```yaml
alias: "Tidbyt: Spotify Sync"
trigger:
  - platform: state
    entity_id: media_player.spotify
action:
  - service: esphome.tidbyt_update_media
    data:
      title: "{{ state_attr('media_player.spotify', 'media_title') }}"
      artist: "{{ state_attr('media_player.spotify', 'media_artist') }}"
      album: "{{ state_attr('media_player.spotify', 'media_album') }}"
      image_path: "{{ state_attr('media_player.spotify', 'entity_picture') }}"
      is_playing: "{{ is_state('media_player.spotify', 'playing') }}"
