# Design Decisions & Technical Details

## Overview
This document explains the technical decisions made when implementing the smart light control scripts for Home Assistant.

## Problem Statement
Users of the Hue Dimmer Switch blueprint needed:
1. Brightness controls that only affect lights already on (not turning on off lights)
2. A minimum brightness of 1% to prevent lights from turning completely off
3. Toggle functionality that only turns off lights that were on
4. Support for areas, floors, and multiple targets

## Solution: Script Templates vs. Template Entities

We chose to implement this as **script templates** rather than template entities for several reasons:

### Why Script Templates?

1. **Action-Oriented**: Scripts are designed for performing actions, which matches our use case perfectly
2. **Parameter Support**: Scripts can accept parameters (target, action, step), making them flexible and reusable
3. **Integration**: Scripts integrate seamlessly with blueprints and automations
4. **Parallel Execution**: Using `mode: parallel` allows multiple calls to run simultaneously
5. **No Polling**: Scripts execute on-demand, unlike sensors that need updates
6. **Reusability**: Can be called from any automation, not just the dimmer blueprint

## Key Technical Features

### Smart Brightness Control

**Problem**: Standard brightness controls affect all lights in a target, turning on lights that were off.

**Solution**:
```yaml
# 1. Get all light entities from the target (area/floor/device/entity)
# 2. Filter to only lights that are currently ON
# 3. Calculate new brightness with bounds checking
# 4. Apply brightness individually to each light
```

**Key Features**:
- Minimum brightness of 1% (prevents complete off)
- Maximum brightness of 100% (prevents overflow)
- Individual light control (respects each light's current state)
- Handles mixed light types gracefully

### Smart Light Toggle

**Problem**: Standard toggle turns on lights that were off and turns off lights that were on.

**Solution**:
```yaml
# 1. Get all light entities from the target
# 2. Filter to only lights that are currently ON
# 3. Turn off only those lights
```

**Key Features**:
- Preserves the state of off lights
- Useful for "all off" scenarios where you don't want to turn lights on first
- Matches expected dimmer switch behavior

## Target Handling

The scripts support all Home Assistant target types:
- `area_id`: Single or multiple areas
- `floor_id`: Single or multiple floors  
- `device_id`: Single or multiple devices
- `entity_id`: Single or multiple entities

**Implementation**:
```jinja2
# Template code uses namespace to collect entities
{% set ns = namespace(entities=[]) %}

# Handle each target type
{% if target.area_id is defined %}
  # Process areas
{% endif %}
{% if target.floor_id is defined %}
  # Process floors
{% endif %}
# ... etc
```

This approach allows mixing target types in a single call, e.g.:
```yaml
target:
  area_id: living_room
  entity_id: light.hallway
```

## Brightness Calculation

The brightness calculation ensures smooth transitions and proper bounds:

```jinja2
# Current brightness as percentage
current_brightness_pct: "{{ state_attr(light, 'brightness') / 255 * 100 }}"

# New brightness with bounds
new_brightness_pct: "{{ [1, [100, current + adjustment] | min] | max }}"

# Convert back to 0-255 scale
new_brightness_value: "{{ (new_brightness_pct * 255 / 100) | int }}"
```

**Why these bounds?**
- Minimum of 1%: Prevents lights from turning off
- Maximum of 100%: Prevents brightness overflow
- Individual calculation: Each light maintains its own brightness curve

## Error Handling

### Empty Target
Both scripts check if there are any lights to control:
```yaml
- condition: template
  value_template: "{{ lights_on | length > 0 }}"
```

This prevents errors when:
- No lights are on (brightness control)
- No lights in target area
- Invalid target specified

### Invalid Brightness
The brightness bounds checking handles:
- Lights at 100% (can't increase further)
- Lights near 1% (won't go below minimum)
- Large step values (won't overflow)

## Performance Considerations

### Parallel Mode
```yaml
mode: parallel
max: 10
```

Allows multiple button presses to be processed simultaneously without blocking.

### Individual Light Control
Each light is controlled individually in a `repeat` loop, which:
- Respects each light's current state
- Handles different light types (dimmable, non-dimmable)
- Allows per-light error handling

### Template Optimization
- Entity lists are calculated once and cached in variables
- Filtering happens in memory (fast)
- No unnecessary service calls

## Integration with Hue Dimmer Blueprint

The scripts are designed to work seamlessly with the existing blueprint:
- No modifications to the blueprint required
- Users configure actions in the blueprint UI
- Scripts handle the complex logic

**Example Integration**:
```yaml
up_single_press_action:
  - service: script.smart_brightness_control
    data:
      target:
        area_id: living_room
      action: up
      step: 10
```

## Future Enhancements

Potential improvements for future versions:
1. **Transition time parameter**: Smooth brightness changes
2. **Color temperature support**: Adjust warmth/coolness
3. **Scene restoration**: Remember and restore previous states
4. **Grouped behavior**: Keep relative brightness between lights
5. **Adaptive brightness**: Consider time of day or ambient light

## Testing Recommendations

When testing these scripts:
1. Test with single light
2. Test with multiple lights in area
3. Test with some lights on, some off
4. Test with lights at various brightness levels
5. Test rapid button presses (parallel mode)
6. Test edge cases (all lights on, all lights off, 1% brightness, 100% brightness)

## Compatibility

**Minimum Home Assistant Version**: 2024.6.0
- Uses modern template syntax
- Requires area_entities() and floor_entities() functions
- Uses target selector syntax

**Light Support**:
- Works with any light entity
- Gracefully handles non-dimmable lights (they'll turn on/off but ignore brightness)
- Supports light groups and areas

## License & Sharing

These scripts are designed to be:
- Shareable (can be published to blueprint exchanges)
- Modifiable (users can customize behavior)
- Reusable (not tied to specific hardware)
- Well-documented (extensive comments and examples)
