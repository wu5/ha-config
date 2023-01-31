### 3D printer cards

<img src="img/3dprinter_cam.png" align="center">

<details><summary>Main card code</summary>

```yaml
type: custom:vertical-stack-in-card
horizontal: false
cards:
  - type: custom:mushroom-entity-card
    name: 3D принтер
    icon: mdi:power
    icon_color: green
    entity: switch.bear_power
    tap_action:
      action: toggle
    secondary_info: state
  - type: picture-entity
    entity: camera.printer
    camera_image: camera.printer
    hide_if_unavailable: true
    show_state: false
    show_name: false
    camera_view: live
  - type: grid
    square: false
    columns: 3
    cards:
      - type: custom:mushroom-template-card
        entity: sensor.bear_power_energy_total
        primary: Прогреть
        secondary: null
        tap_action:
          action: call-service
          service: mqtt.publish
          service_data:
            topic: octoprint/hassControl/commands
            payload: '["M104 S230"]'
        icon: mdi:printer-3d-nozzle-heat
        icon_color: |2-
                    {% set s=states('sensor.3d_printer_print_status') %}
                    {% if s=='unavailable' or s=='Printing' %}
                    grey
                    {% else %}
                    orange
                    {% endif %}
      - type: custom:mushroom-template-card
        primary: Остудить
        secondary: null
        tap_action:
          action: call-service
          service: mqtt.publish
          service_data:
            topic: octoprint/hassControl/commands
            payload: '["M104 S0","M140 S0","M84"]'
        icon: mdi:printer-3d-nozzle-heat-outline
        icon_color: |2-
                    {% set s=states('sensor.3d_printer_print_status') %}
                    {% if s=='unavailable' or s=='Printing' %}
                    grey
                    {% else %}
                    blue
                    {% endif %}
      - type: custom:mushroom-template-card
        entity: button.3d_printer_cancel_print
        primary: Стоп
        secondary: null
        tap_action:
          action: toggle
        icon: mdi:stop
        icon_color: |2-
                    {% set s=states('sensor.3d_printer_print_status') %}
                    {% if s=='unavailable' or s=='Operational' %}
                    grey
                    {% else %}
                    red
                    {% endif %}
                    
```

</details>

<img src="img/3dprinter_thumb.png" align="center">

<details><summary>Thumbnail card code</summary>

```yaml
type: custom:vertical-stack-in-card
cards:
  - type: custom:mushroom-template-card
    entity: sensor.3d_printer_print_file
    tap_action:
      action: more-info
    icon: mdi:file
    icon_color: |2-
                {% if is_state(entity, 'unavailable') %}
                grey
                {% else %}
                blue-grey
                {% endif %}
    primary: Файл
    secondary: |2-
               {% set s=states(entity) %}
               {% if s=='unavailable' or s=='' %}
               Не выбран
               {% else %}
               {{ states(entity) }}
               {% endif %}
  - type: picture-entity
    entity: camera.3d_printer_gcode_thumbnail
    show_state: false
    show_name: false
    camera_view: auto
  - type: grid
    square: false
    columns: 2
    cards:
      - type: custom:mushroom-template-card
        entity: sensor.3d_printer_print_progress
        tap_action:
          action: more-info
        icon: mdi:timer-sand
        icon_color: |2-
                    {% if is_state('binary_sensor.3d_printer_connected', 'unavailable') %}
                    grey
                    {% else %}
                    blue-grey
                    {% endif %}
        primary: |2-
                 {% set s=states('sensor.3d_printer_print_status') %}
                 {% set t=states('sensor.3d_printer_print_time') %}
                 {% if s=='Printing' and t=='None' %}
                 Подготовка
                 {% elif s=='Printing' and t!='None' %}
                 Печать {{ states(entity) | float | round(0) }}%
                 {% elif s=='Operational' %}
                 Ожидание
                 {% else %}
                 Выключен
                 {% endif %}
        secondary: |2-
                   {% set t=states('sensor.3d_printer_print_time') %}
                   {% if t=='unavailable' or t=='None' %}
                   {% else %}
                   {{ states("sensor.3d_printer_print_time")[0:-3] }} / 
                   {{ states("sensor.3d_printer_print_time_left")[0:-3] }} 
                   {% endif %}
        card_mod:
          style:
            mushroom-shape-icon$: |
              .shape {
                background: radial-gradient(var(--card-background-color) 60%, transparent calc(60% + 1px)), conic-gradient(rgb(var(--rgb-light-blue)) {{ (states(config.entity) |float) | round(0) }}% 0%, var(--card-background-color) 0% 100%);
              }
              .shape:after {
                content: "";
                height: 100%;
                width: 100%;
                position: absolute;
                border-radius: 50%;
                background: rgba(var(--rgb-blue-grey), 0.2);
              }
      - type: custom:mushroom-template-card
        entity: sensor.3d_printer_approximate_completion_time
        primary: Завершится
        secondary: |2-
                   {% set s=states(entity) %}
                   {% if s=='unavailable' or s=='None' %}
                   Неизвестно
                   {% else %}
                   {{ states(entity)[0:-3] }}
                   {% endif %}
        tap_action:
          action: more-info
        icon: mdi:timer-sand-complete
        icon_color: |2-
                    {% if is_state('binary_sensor.3d_printer_connected', 'unavailable') %}
                    grey
                    {% else %}
                    blue-grey
                    {% endif %}
```
</details>

