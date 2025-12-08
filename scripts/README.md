# Smart Light Control Scripts

Reusable Home Assistant script blueprints for intelligent light control. These scripts can be used with any Home Assistant automation, script, or button.

## Features

- **Smart Brightness Control**: Adjust brightness only for lights that are currently ON
- **Smart Toggle**: Turn off only lights that were ON, leaving off lights untouched
- **Area/Floor Support**: Works with Home Assistant areas and floors
- **Minimum Brightness**: Prevents lights from turning completely off (1% minimum)
- **Transition Support**: Optional smooth brightness transitions

## Installation

### Smart Brightness Control (v1.0.0)

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FMPFGlaser%2Fha-blueprints%2Fblob%2Fmain%2Fscripts%2Fsmart_brightness_control.yaml)

### Smart Light Toggle (v1.0.0)

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FMPFGlaser%2Fha-blueprints%2Fblob%2Fmain%2Fscripts%2Fsmart_light_toggle.yaml)

## Usage

### Smart Brightness Control

Adjusts brightness for lights that are currently on, with a minimum of 1% to prevent turning off.

**Service Call:**
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room  # or floor_id, device_id, entity_id
  action: up  # or 'down'
  step: 10  # optional, default 10%
  transition: 1  # optional, default 0 seconds
```

**Parameters:**
- `target` (required): Specify lights using:
  - `area_id`: One or more area IDs
  - `floor_id`: One or more floor IDs
  - `device_id`: One or more device IDs
  - `entity_id`: One or more light entity IDs
- `action` (required): `up` or `down`
- `step` (optional): Brightness change in percentage (1-100, default: 10)
- `transition` (optional): Duration of brightness change in seconds (0-60, default: 0)

### Smart Light Toggle

Toggles only lights that are currently on, leaving off lights unchanged.

**Service Call:**
```yaml
service: script.smart_light_toggle
data:
  target:
    area_id: living_room  # or floor_id, device_id, entity_id
```

**Parameters:**
- `target` (required): Specify lights using:
  - `area_id`: One or more area IDs
  - `floor_id`: One or more floor IDs
  - `device_id`: One or more device IDs
  - `entity_id`: One or more light entity IDs

## Use Cases

These scripts can be called from anywhere in Home Assistant:

### Example Configuration

When setting up your Hue Dimmer automation using the blueprint, configure the button actions as follows:

#### On Button - Single Press (Toggle Lights)
## Use Cases

These scripts can be called from anywhere in Home Assistant:

### From Automations

Use with any automation trigger:

```yaml
automation:
  - trigger:
      - platform: state
        entity_id: binary_sensor.motion_hallway
        to: 'on'
    action:
      - service: script.smart_brightness_control
        data:
          target:
            area_id: hallway
          action: up
          step: 20
          transition: 2
```

### From Buttons/Dashboards

Add to Lovelace dashboards:

```yaml
type: button
tap_action:
  action: call-service
  service: script.smart_light_toggle
  data:
    target:
      area_id: living_room
name: Toggle Living Room Lights
icon: mdi:lightbulb
```

### From Other Scripts

Chain scripts together:

```yaml
script:
  bedtime_routine:
    sequence:
      - service: script.smart_brightness_control
        data:
          target:
            area_id: bedroom
          action: down
          step: 50
          transition: 5
```

### With Hue Dimmer Blueprint

Configure button actions in the [Hue Dimmer Switch blueprint](../blueprints/controllers/hue-dimmer.yaml):

#### On Button - Single Press (Toggle Lights)
```yaml
service: script.smart_light_toggle
data:
  target:
    area_id: living_room
```

#### Up Button - Single Press (Increase Brightness)
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: up
  step: 10
  transition: 0.5
```

#### Down Button - Single Press (Decrease Brightness)
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: down
  step: 10
  transition: 0.5
```

### Multi-Area/Floor Support

Control multiple areas or floors at once:

```yaml
# Control multiple areas
service: script.smart_brightness_control
data:
  target:
    area_id:
      - living_room
      - kitchen
  action: up
  step: 10
```

```yaml
# Control entire floor
service: script.smart_light_toggle
data:
  target:
    floor_id: ground_floor
```

## Advanced Examples

### Specific Lights Only
```yaml
service: script.smart_brightness_control
data:
  target:
    entity_id:
      - light.ceiling_1
      - light.ceiling_2
      - light.floor_lamp
  action: up
  step: 15
  transition: 1
```

### Multiple Devices
```yaml
service: script.smart_light_toggle
data:
  target:
    device_id:
      - abc123device
      - def456device
```

### Mixed Targets
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
    entity_id: light.hallway
  action: down
  step: 5
```

### Smooth Transitions
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: bedroom
  action: down
  step: 30
  transition: 3  # 3 seconds for smooth dimming
```

## How It Works

### Smart Brightness Control
1. Identifies all light entities from the target (area, floor, device, or entity)
2. Filters to only lights that are currently ON
3. Calculates new brightness based on current level and adjustment step
4. Ensures brightness never goes below 1% or above 100%
5. Applies the new brightness to each light individually with optional transition

### Smart Light Toggle
1. Identifies all light entities from the target
2. Filters to only lights that are currently ON
3. Turns off only those lights, leaving already-off lights unchanged

This prevents the annoying behavior where pressing a dimmer's "off" button turns on lights that were previously off!

## Troubleshooting

## Troubleshooting

### Scripts don't appear after import
- Make sure you clicked the import button and confirmed the import
- Check Settings > Automations & Scenes > Scripts to verify they were added
- Restart Home Assistant if needed

### Brightness changes affect all lights, not just on lights
- Ensure you're using the `smart_brightness_control` script, not the regular `light.turn_on` service
- Check that the script is properly loaded in Developer Tools > Services

### Areas not working
- Verify your lights are assigned to the correct areas in Home Assistant
- Check that area names match exactly (case-sensitive)

## Version History

- **v1.0.0** (2024-12-08)
  - Initial release
  - Smart brightness control with minimum 1% brightness
  - Smart toggle for on lights only
  - Support for areas, floors, devices, and entities
  - Optional transition time for brightness changes

## Contributing

Found a bug or have a suggestion? Please open an issue on the [GitHub repository](https://github.com/MPFGlaser/ha-blueprints).

## License

These scripts are provided as-is for use with Home Assistant. Feel free to modify and share!
