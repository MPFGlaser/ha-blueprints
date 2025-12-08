# Quick Start Guide

## What Was Created

Two reusable Home Assistant script blueprints that can be called from anywhere:

1. **Smart Brightness Control (v1.0.0)**
   - Adjusts brightness ONLY for lights that are already on
   - Never turns off lights (minimum 1% brightness)
   - Optional transition time for smooth changes
   - Works with areas, floors, and individual lights

2. **Smart Light Toggle (v1.0.0)**
   - Toggles ONLY lights that were previously on
   - Leaves off lights unchanged
   - Perfect for any automation or button

## Installation (2 Minutes)

### Smart Brightness Control

1. Click this button to import:

   [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FMPFGlaser%2Fha-blueprints%2Fblob%2Fmain%2Fscripts%2Fsmart_brightness_control.yaml)

2. Confirm the import in Home Assistant

### Smart Light Toggle

1. Click this button to import:

   [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FMPFGlaser%2Fha-blueprints%2Fblob%2Fmain%2Fscripts%2Fsmart_light_toggle.yaml)

2. Confirm the import in Home Assistant

### Verify Installation

1. Go to **Settings** > **Automations & Scenes** > **Scripts**
2. You should see both scripts listed!

## Basic Usage

These scripts can be called from any automation, script, button, or dashboard.

### With Hue Dimmer Blueprint

**On Button** (Toggle lights that are on):
```yaml
service: script.smart_light_toggle
data:
  target:
    area_id: living_room
```

**Up Button** (Increase brightness):
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: up
  step: 10
  transition: 0.5
```

**Down Button** (Decrease brightness):
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: down
  step: 10
  transition: 0.5
```

### From Automations

```yaml
automation:
  - trigger:
      - platform: state
        entity_id: binary_sensor.motion
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

```yaml
type: button
tap_action:
  action: call-service
  service: script.smart_light_toggle
  data:
    target:
      area_id: living_room
```

## Quick Examples

### Control Multiple Areas
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id:
      - living_room
      - kitchen
  action: up
  step: 15
```

### Control Entire Floor
```yaml
service: script.smart_light_toggle
data:
  target:
    floor_id: ground_floor
```

### Control Specific Lights
```yaml
service: script.smart_brightness_control
data:
  target:
    entity_id:
      - light.ceiling_1
      - light.floor_lamp
  action: down
  step: 20
  transition: 1
```

### Smooth Transitions
```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: bedroom
  action: down
  step: 40
  transition: 3  # Dim slowly over 3 seconds
```

## Why This Solves Your Problem

### Before (Standard Controls)
- Brightness Up: Turns on ALL lights in the room, even if they were off
- Brightness Down: Can turn lights completely off (0%)
- Toggle: Turns on lights that were off and turns off lights that were on

### After (Smart Controls)
- Brightness Up: Only affects lights that are ALREADY on ✓
- Brightness Down: Keeps lights at minimum 1%, never fully off ✓
- Toggle: Only turns off lights that were on, leaves off lights alone ✓
- All work with areas/floors: No need to list individual lights ✓

## Common Scenarios

### Scenario 1: Living Room with Multiple Light Zones
You have ceiling lights, floor lamps, and accent lights. You only want to control the ones you've already turned on:

```yaml
# On Button: Toggle only active lights
service: script.smart_light_toggle
data:
  target:
    area_id: living_room

# Up/Down: Adjust only active lights
service: script.smart_brightness_control
data:
  target:
    area_id: living_room
  action: up  # or 'down'
  step: 10
```

### Scenario 2: Bedroom Nighttime Control
You want to dim lights but never turn them completely off (night light):

```yaml
service: script.smart_brightness_control
data:
  target:
    area_id: bedroom
  action: down
  step: 5  # Small steps for fine control
```

The lights will stop at 1% brightness instead of turning off!

### Scenario 3: Whole Floor Control
Control all lights on a floor, but only the ones that are on:

```yaml
service: script.smart_brightness_control
data:
  target:
    floor_id: ground_floor
  action: up
  step: 20
```

## Troubleshooting

### Scripts don't appear after restart
- Check that files are in the correct location
- Verify `configuration.yaml` has the script include
- Check **Settings** > **System** > **Logs** for errors

### Scripts exist but don't do anything
- Verify lights are assigned to the correct areas in Home Assistant
- Check that area/floor names match exactly (case-sensitive)
- Try with specific `entity_id` first to test

### Brightness still turns lights completely off
- Make sure you're calling `script.smart_brightness_control`, not `light.turn_on`
- Check the script is properly loaded in Developer Tools > Services

## Next Steps

1. **Read the full documentation**: Check `scripts/README.md` for detailed usage
2. **See complete examples**: Look at `scripts/example-configuration.yaml`
3. **Understand the design**: Read `scripts/DESIGN.md` for technical details
4. **Customize**: Adjust the `step` parameter to your preference (5-30 works well)

## Support

For issues or questions, open an issue on the [GitHub repository](https://github.com/MPFGlaser/ha-blueprints).

## Summary

You now have:
- ✅ Smart brightness control that only affects lights that are on
- ✅ Minimum 1% brightness (never fully off)
- ✅ Smart toggle that only turns off lights that were on
- ✅ Full support for areas, floors, and multiple targets
- ✅ Reusable across all your automations

Enjoy your intelligent dimmer switch! 🎉
