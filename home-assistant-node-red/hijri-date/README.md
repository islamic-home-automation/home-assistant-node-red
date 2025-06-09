# Hijri Date Flow

This flow automatically calculates and updates the Hijri (Islamic) date in Home Assistant at Maghrib (sunset) each day, with support for manual adjustments to align with local moon-sighting observations.

---

## Folder Contents

- `flow.json` – Exported Node-RED flow JSON  
- `README.md` – This instruction file  
- `screenshot.png` – Screenshot of the flow in the Node-RED workspace

---

## Prerequisites

1. **Node-RED** installed and connected to your Home Assistant instance.  
2. **Home Assistant** server configured in Node-RED (`server` credentials in HA nodes).  
3. **Entities in Home Assistant**:  
   - `sensor.maghrib_prayer` – A sensor providing today’s Maghrib time (e.g., from `sun.sun` or an Islamic prayer integration).  
   - `input_number.adjust_hijri_days` – An integer input for manual day adjustments (default 0).  
   - `sensor.hijri_date` – A sensor to receive the updated Hijri date state.

---

## Installation & Configuration

1. **Import the flow**  
   - In Node-RED, go to **Menu → Import → Clipboard**.  
   - Paste the contents of `flow.json`.  
   - Click **Import** and position the flow in your workspace.

2. **Configure Home Assistant server nodes**  
   - Open any HA node (e.g., *Hijri Date Adjustment Listener* or *Update HA Sensor*).  
   - Ensure the **Server** dropdown points to your Home Assistant instance.

3. **Entity IDs**  
   - Verify or adjust the following entity IDs to match your HA setup:  
     - **Maghrib trigger** (`sensor.maghrib_prayer`)  
     - **Adjustment listener** (`input_number.adjust_hijri_days`)  
     - **Sensor update** (`sensor.hijri_date`)

4. **Deploy** the flow.

---

## How It Works

1. **Sunset Trigger** – The `ha-time` node fires at each day’s Maghrib time.  
2. **Midnight Reset** – A cron inject at `00:00` resets the `next_day` flag to 0, ensuring the same Hijri date until sunset.  
3. **Adjustment Listener** – Changes in `input_number.adjust_hijri_days` update the `hijri_adjustment` in flow context.  
4. **Date & URL Builder** – A Function node:  
   - Reads `hijri_adjustment` (days offset).  
   - Applies it to the current Gregorian date.  
   - Formats `DD-MM-YYYY` and sets  
     ```js
     msg.url = `https://api.aladhan.com/v1/gToH?date=${dateStr}`
     ```  
5. **API Request** – An HTTP Request node calls the Aladhan API to convert the adjusted Gregorian date to Hijri.  
6. **Parse & Store** – A Function node extracts `day`, `month`, and `year`, builds a display string, and stores values in global context if needed.  
7. **HA Sensor Update** – A `ha-api` node updates `sensor.hijri_date` in Home Assistant with the formatted date.

---

## Customization

- **Adjustment Range**: You can manually adjust by turning the `input_number.adjust_hijri_days` up/down to align with local sighting.  
- **Fallback Logic**: Add a Catch node on the HTTP Request to notify you if the API call fails.  
- **Language / Format**: Modify the parsing node to output Arabic month names or add an `AH` suffix if desired.

---

## Troubleshooting

- **No update at Maghrib**: Check that your `sensor.maghrib_prayer` is reporting correct times and that the `ha-time` node’s timezone matches your system.  
- **API errors**: Enable the debug node on the HTTP Request to inspect `msg.payload` and error messages.  
- **Sensor not updating**: Ensure the `ha-api` node path (`states/sensor.hijri_date`) matches an existing sensor or create a new one in HA.

---

> _Screenshot of the flow is in `screenshot.png`._
