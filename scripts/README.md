# Smart Light Control Scripts

This directory contains reusable Home Assistant script templates for intelligent light control. These scripts are designed to work seamlessly with the Hue Dimmer Switch blueprint but can be used with any Home Assistant automation or script.

## Features

- **Smart Brightness Control**: Adjust brightness only for lights that are currently ON
- **Smart Toggle**: Turn off only lights that were ON, leaving off lights untouched
- **Area/Floor Support**: Works with Home Assistant areas and floors
- **Minimum Brightness**: Prevents lights from turning completely off (1% minimum)

## Installation

1. Copy the script files to your Home Assistant configuration directory:
   - `smart_brightness_control.yaml`
   - `smart_light_toggle.yaml`

2. Add the scripts to your `configuration.yaml`:

   ```yaml
   script: !include_dir_merge_named scripts/
   ```

   Or if you already have a scripts section, include them individually:

   ```yaml
   script:
     smart_brightness_control: !include scripts/smart_brightness_control.yaml
     smart_light_toggle: !include scripts/smart_light_toggle.yaml
   ```

3. Restart Home Assistant to load the new scripts.

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
```

**Parameters:**
- `target` (required): Specify lights using:
  - `area_id`: One or more area IDs
  - `floor_id`: One or more floor IDs
  - `device_id`: One or more device IDs
  - `entity_id`: One or more light entity IDs
- `action` (required): `up` or `down`
- `step` (optional): Brightness change in percentage (1-100, default: 10)

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

## Integration with Hue Dimmer Blueprint

Here's how to use these scripts with the [Hue Dimmer Switch blueprint](../blueprints/controllers/hue-dimmer.yaml):

### Example Configuration

When setting up your Hue Dimmer automation using the blueprint, configure the button actions as follows:

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
```

#### Up Button - Hold (Increase Brightness Faster)
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: up
  step: 20
```

#### Down Button - Single Press (Decrease Brightness)
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: down
  step: 10
```

#### Down Button - Hold (Decrease Brightness Faster)
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: down
  step: 20
```

#### Off Button - Single Press (Turn Off Lights)
```yaml
service: light.turn_off
target:
  area_id: living_room
```

### Multi-Area/Floor Support

You can control multiple areas or floors at once:

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

## Use Cases

### Living Room Dimmer
- **On button**: Toggle only the lights that were on
- **Up/Down buttons**: Adjust only the lights that are on
- **Off button**: Turn off all lights in the area

### Bedroom Dimmer
- **On button**: Turn on bedside lamps
- **Up/Down buttons**: Adjust brightness (keeping them at least 1% for night lights)
- **Off button**: Turn everything off

### Kitchen Multi-Zone
- **On button**: Toggle only active zones
- **Up/Down buttons**: Adjust only active zones
- **Off button**: Turn off everything

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

## How It Works

### Smart Brightness Control
1. Identifies all light entities from the target (area, floor, device, or entity)
2. Filters to only lights that are currently ON
3. Calculates new brightness based on current level and adjustment step
4. Ensures brightness never goes below 1% or above 100%
5. Applies the new brightness to each light individually

### Smart Light Toggle
1. Identifies all light entities from the target
2. Filters to only lights that are currently ON
3. Turns off only those lights, leaving already-off lights unchanged

This prevents the annoying behavior where pressing a dimmer's "off" button turns on lights that were previously off!

## Troubleshooting

### Scripts don't appear in Home Assistant
- Verify the scripts are properly included in `configuration.yaml`
- Check Configuration > Logs for any YAML syntax errors
- Restart Home Assistant after adding the scripts

### Brightness changes affect all lights, not just on lights
- Ensure you're using the `smart_brightness_control` script, not the regular `light.turn_on` service
- Check that the script is properly loaded in Developer Tools > Services

### Areas not working
- Verify your lights are assigned to the correct areas in Home Assistant
- Check that area names match exactly (case-sensitive)

## Contributing

Found a bug or have a suggestion? Please open an issue on the [GitHub repository](https://github.com/MPFGlaser/ha-blueprints).

## License

These scripts are provided as-is for use with Home Assistant. Feel free to modify and share!
