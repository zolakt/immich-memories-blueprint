# Immich Memories Blueprint
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Blueprint-blue)

---

## Description
**Immich Memories** is a Home Assistant automation blueprint that sends a daily push notification featuring a memory from your Immich photo server that occurred on the same calendar day in past years. The automation allows full customization of the message, title, and notification icon, memory selection mode and timing.

---

## Features
- Sends daily push notifications at a configurable time
- Supports selecting memories:
  - Random
  - Newest
  - Oldest
- Supports selecting a single asset within a memory:
  - Random
  - Newest
  - Oldest
- Customizable message and title templates with placeholders:
  - `{{years_ago}}` — Number of years since the memory  
  - `{{years_ago_label}}` — "year" or "years"  
  - `{{memory_year}}` — Year of the memory
- Customizable icon
- If no memory exists for the current date, no notification will be sent.

---

## Prerequisites

1. **Immich Server** accessible via API.  
2. **Immich API Key** with permissions:
   - `memory.read`  
   - `asset.view`  
3. **rest_command** for fetching memories from Immich

---

## Installation

### 1. Add REST Command

Add the following snippet to your `configuration.yaml`:

```yaml
rest_command:
  immich_get_memories:
    url: "{{ api_url }}/api/memories?for={{ now().strftime('%Y-%m-%dT00:00:00.000Z') }}"
    method: GET
    headers:
      Accept: "application/json"
      x-api-key: "{{ api_key }}"
```

Restart Home Assistant after saving the changes.

### 2. Import the Blueprint

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fzolakt%2Fimmich-memories-blueprint%2Fblob%2Fmain%2Fimmich-memories.yaml)

Alternatively, if the above button doesn't work:
1. Go to Settings → Automations & Scenes → Blueprints.
2. Click Import Blueprint
3. Paste the URL of the YAML file from this repository

### 3. Create automation from blueprint

---

## Configuration
| Input                     | Description                                                                 |
|---------------------------|-----------------------------------------------------------------------------|
| **Immich API URL**        | Base URL of your Immich server (e.g., `http://10.0.0.5:2283`)               |
| **Immich API Key**        | API key with proper permissions                                             |
| **Immich UI URL**         | Optional URL for UI links. Defaults to API URL if left blank.               |
| **Notify Target**         | Mobile device to receive notifications (e.g., `notify.mobile_app_myphone`) |
| **Time of Day**           | Time to send the daily notification (default: `09:00:00`)                   |
| **Include Thumbnail**     | Whether to include the memory image (default: true)                         |
| **Memory Selection Mode** | `random`, `newest`, or `oldest`                                            |
| **Asset Selection Mode**  | `random`, `newest`, or `oldest`                                            |
| **Message Template**      | Custom notification message. Supports placeholders                          |
| **Title Template**        | Optional custom title. Supports placeholders                                |
| **Notification Icon**     | Optional icon (e.g., `mdi:camera`)                                          |

### Placeholders
The following placeholders are available:
- `{{years_ago}}` — Number of years since the memory  
- `{{years_ago_label}}` — "year" or "years"  
- `{{memory_year}}` — Year of the memory  

### Example
Message template:
```
On this day {{years_ago}} {{years_ago_label}} ago
```
Title template:
```
Memory from {{memory_year}}
```

This would produce a notification like:
> Memory from 2020  
> On this day 5 years ago

## Known limitations
1. Immich hasn't yet implemented deep links for the mobile app. Therefore, when you click on the notification, it can't take you directly to the Immich app and open that memory. The issue is tracked here: https://github.com/immich-app/immich/discussions/2881. Once this is implemented, you can replace your **Immich UI URL** with something like `immich://` and it should work. Currently, clicking the notification will open the memory in the browser (not the app)
2. There might be some issues in passing requests to the Immich API through a reverse proxy. Therefore it is recommened to use ip:port for **Immich API URL**, e.g. `http://10.0.0.5:2283`. It is fine to use the reverse proxy address for **Immich UI URL**, e.g. `https://immich.home.lan`, so it opens the UI with the cleaner URL
3. Immich HA integration doesn't expose an action for fetching memories. Therefore, you need to add the **rest_command** manually to get around this. If they do add this action, I'll remove the **rest_command** and modify the logic so it uses the integration action
