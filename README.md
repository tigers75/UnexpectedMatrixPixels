# 🎨 UnexpectedMatrixPixels (UMP)

**Home Assistant integration for controlling BLE LED matrix displays** (IDOTMatrix, iPixel, etc.) directly via Bluetooth without bridges.

---

## ⚡ Features

- **Direct Bluetooth Control** - No cloud, no bridges. Runs on HA host or ESPHome proxy
- **Multiple Visual Elements** - Text (static/scrolling), icons (MDI), images, raw pixels
- **Live Camera Preview** - Real-time display of matrix content
- **Jinja2 Templates** - Dynamic content with HA templating
- **Performance Optimized** - Frame diffing to reduce bandwidth

---

## 🚀 Installation

1. Copy `ump` folder to `/config/custom_components/`
2. Restart Home Assistant
3. Go to Settings → Devices & Services → Create Integration
4. Search for **UnexpectedMatrixPixels**
5. Enter MAC address and display dimensions (e.g., `16x64`, `32x32`)

**Requirements:**
```
Pillow >= 10.0.0
bleak
bleak-retry-connector >= 1.0.0
```

---

## 📌 Quick Examples

### Static Text
```yaml
service: ump.draw_visuals
target:
  entity_id: light.my_display
data:
  elements:
    - type: text
      content: "Hello"
      x: 0
      y: 5
      font: "5x7"
      color: [255, 0, 0]
```

### Scrolling Text
```yaml
service: ump.draw_visuals
target:
  entity_id: light.my_display
data:
  elements:
    - type: textscroll
      content: "News"
      y: 8
      color: [0, 255, 255]
      font: "awtrix"
      speed: 15
  fps: 5
```

### Icon
```yaml
service: ump.draw_visuals
target:
  entity_id: light.my_display
data:
  elements:
    - type: icon
      name: mdi:home
      x: 8
      y: 8
      size: 16
      color: [100, 255, 150]
```

### Smart Text (Pagination)
```yaml
service: ump.draw_visuals
target:
  entity_id: light.my_display
data:
  elements:
    - type: textlong
      content: "Long message that scrolls"
      x: 0
      y: 5
      font: "awtrix"
      color: [255, 255, 0]
      speed: 2.0
      scroll_duration: 0.5
      direction: "up"
```

---

## 📚 Element Types

| Type | Description | Key Params |
|------|-------------|-----------|
| `text` | Static text | `content`, `x`, `y`, `color`, `font` |
| `textscroll` | Scrolling text | `content`, `y`, `speed`, `font`, `color` |
| `textlong` | Smart pagination + scroll | `content`, `y`, `speed`, `scroll_duration`, `direction` |
| `icon` | MDI icon | `name` (mdi:*), `x`, `y`, `size`, `color` |
| `image` | Image from URL/file | `path`/`url`, `x`, `y`, `width`, `height` |
| `pixels` | Raw pixels | `pixels`: `[[x,y,r,g,b], ...]` |

---

## ⚙️ Services

### `ump.draw_visuals`
Render content on display.

**Parameters:**
- `background` [R,G,B] - Background color (default: [0,0,0])
- `fps` (1-30) - Frame rate (default: 10, **lower = more stable**)
- `elements` - List of visual elements

### `ump.clear_display`
Clear display screen.

### `ump.sync_time`
Sync display clock with HA.

---

## ⚠️ Stability Notes

**High refresh rate causes instability** - especially when updating display per second:

✅ **Use lower FPS:**
```yaml
fps: 5  # Better stability
```

✅ **Avoid frequent updates** - batch changes together instead of multiple calls/sec

✅ **Test with realistic intervals:**
- Status updates: 30-60s
- Media/music: 5-10s
- Animations: 2-5s (with `fps: 5-8`)

---

## 🔧 Real-World Example: Spotify

See `examples/spotify.yaml` for advanced template-based implementation with:
- Artist/title scrolling
- Progress bar rendering
- Conditional playback display

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Display not responding | Lower `fps` to 3-5, check MAC address |
| Connection drops | Reduce update frequency, lower `fps` |
| Text garbled | Verify font name (`"5x7"`, `"3x5"`, `"awtrix"`) |
| Image not loading | Use absolute path: `/config/www/image.png` |

---

## 📁 Component Structure

```
ump/
├── __init__.py         # Service handlers
├── config_flow.py      # UI configuration
├── light.py            # Light entity
├── camera.py           # Camera preview
├── ble_client.py       # BLE communication
├── fonts.py            # Font rendering
├── services.yaml       # Service definitions
└── manifest.json       # Metadata
```

---

## 📄 License

MIT License - See LICENSE file

---

**Acknowledgments:** Home Assistant, Bleak, Material Design Icons, Community

**Support:** [GitHub Issues](https://github.com/suchyindustries/UnexpectedMatrixPixels/issues)
