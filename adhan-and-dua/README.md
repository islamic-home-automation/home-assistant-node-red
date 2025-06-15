
# Adhan/Azan + after Adhan Dua + Apple TV pause & Play (Node-RED + Home Assistant)

This Node-RED usages our dua-adhkar-with-repeat flow with some additional features. 

## Each entry in the playlist specifies:
- A folder path
- A filename pattern (e.g. `Fajr Azan`, `Nas`)
- A repeat count

## The flow:
1. Triggers during each Adhan time.
2. Checks if the Adhan is Fajr or other.
3. Finds matching audio files.
4. Picks one randomly.
5. Plays it via Home Assistant.
6. Pause Apple TV
7. Waits for the exact duration using `ffprobe`.
8. Repeats if needed, then continues to the next entry(Adhan Dua).
9. Resumes the Apple TV

---
