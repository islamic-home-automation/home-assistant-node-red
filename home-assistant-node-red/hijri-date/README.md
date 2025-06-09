# Hijri Date Flow
-
This flow automatically calculates and updates the Hijri (Islamic) date in Home Assistant at Maghrib (sunset) each day, with support for manual adjustments to align with local moon-sighting observations.
This flow will also create a date entry in home assistant.
---

## Folder Contents

- `flow.json` – Exported Node-RED flow JSON  
- `README.md` – This instruction file  
- `screenshot.png` – Screenshot of the flow in the Node-RED workspace

---

## Prerequisites

1. **Node-RED** installed and connected to your Home Assistant instance.
2. **Home Assistant** server configured in Node-RED (`server` credentials in HA nodes).
3. **Helper in Home Assistant**: Create an **Input Number** helper (`input_number.adjust_hijri_days`), with a range of **-5** to **5**, to allow manual day adjustments.
4. **Islamic Prayer Times integration** installed and configured:  
   https://www.home-assistant.io/integrations/islamic_prayer_times
5. **Entities in Home Assistant**:
   - `sensor.maghrib_prayer` – Provides today’s Maghrib (sunset) time.  
   - `sensor.hijri_date` – Receives the updated Hijri date state.

---

## Installation & Configuration

1. **Add the Input Number helper in Home Assistant**  
   - Go to **Settings → Devices & Services → Helpers**.  
   - Click **Add Helper → Number**.  
   - Name it **Adjust Hijri Days**, set **Minimum** to `-5`, **Maximum** to `5`, and **Step** to `1`.  
   - Note the Entity ID (e.g., `input_number.adjust_hijri_days`).

2. **Import the flow**  
   - In Node-RED, go to **Menu → Import → Clipboard**.  
   - Paste the contents of `flow.json`.  
   - Click **Import** and position the flow in your workspace.

3. **Configure Home Assistant server nodes**  
   - Open any HA node (e.g., *Hijri Date Adjustment Listener* or *Update HA Sensor*).  
   - Ensure the **Server** dropdown points to your Home Assistant instance.

4. **Entity IDs**  
   - Verify or adjust these to match your HA setup:  
     - **Maghrib trigger** (`sensor.maghrib_prayer`)  
     - **Adjustment listener** (`input_number.adjust_hijri_days`)  
     - **Sensor update** (`sensor.hijri_date`)

5. **Deploy** the flow.

---

## How It Works

1. **Sunset Trigger at Maghrib**  
   The flow fires at local Maghrib time, sets the `next_day` flag to `1` because the Islamic day begins at sunset, not midnight. Each new Hijri date should align with the start of the Islamic day.

2. **Midnight Reset**  
   As the gregorian calendar advances a day at midnight, a cron inject at `00:00` resets the `next_day` flag to `0` so that the hijri date does not advance again until the next sunset. This ensures the Hijri date remains constant throughout the Gregorian day until Maghrib.

3. **Day Adjustment Slider**  
   The **Adjust Hijri Days** input number lets you manually fine-tune the Hijri date by ±5 days. This accounts for local moon-sighting variations that algorithms or APIs may not reflect.

4. **Date Calculation & API Call**  
   - Reads the manual adjustment from `input_number.adjust_hijri_days`.  
   - Applies that offset to the current Gregorian date.  
   - Formats the adjusted date as `DD-MM-YYYY` and calls the Aladhan API to convert it to Hijri.

5. **Parsing & Updating**  
   A Function node extracts the `day`, `month`, and `year` from the API response, builds a display string, and updates the `sensor.hijri_date` state in Home Assistant.

---

## Customization & Troubleshooting

- **Fallback Logic**: Add a Catch node on the HTTP Request to notify you if the API call fails.  
- **Language / Format**: Modify the parsing node to output Arabic month names or include the `AH` suffix.  
- **No update at Maghrib**: Verify your `sensor.maghrib_prayer` and Node-RED timezone settings.  
- **Sensor not updating**: Ensure the `ha-api` node path (`states/sensor.hijri_date`) matches an existing sensor or create a new one in HA.

---

> _Screenshot of the flow is in `hijri-date-flow.png`._
> _Screenshot of the helper setting is in `add_helper_in_homeassistant.png`._
