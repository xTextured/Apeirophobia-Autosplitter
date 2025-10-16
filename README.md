# Apeirophobia  Autosplitter 

This script automatically splits your **LiveSplit** timer whenever your in-game level changes.

---

##  Requirements

- A **Roblox executor** that supports the `WebSocket` library. Use https://whatexpsare.online/ to figure out what executors are currently online  and working.

- **LiveSplit** with the **LiveSplit Server** component, and splits for the specific category you are running.

---

## LiveSplit Setup

1. Open **LiveSplit**  
2. Right-click → **Control → Start WebSocket Server**   
3. (Optional) Confirm the port by going to  
   **Edit Layout → LiveSplit Server → Settings → Server Port**  
   Default: `16834`  
4. Leave LiveSplit running while playing  

---

## How It Works

The script connects to the running LiveSplit WebSocket server at:

ws://127.0.0.1:16834/livesplit

Each time your `currentLevel` value increases, it automatically sends a `split` command to LiveSplit. Once you reach the end of your run, the AutoSplitter will pause.

You can customize it to send other commands such as `pause`, `start`, or `reset` depending on your category.

---

## Script

```lua
--[[
	LiveSplit Autosplitter for Apeirophobia, made by textured.

	Execute once in-game (preferably during loading screens).
	This requires an executor that supports the WebSocket library.
]]

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local levelValue = player:WaitForChild("data"):WaitForChild("currentLevel")

-- Customize your split endpoints by category
local PRE_CLASSIC_END = 5
local CLASSIC_END = 10
local MODERN_END = 16
local CHAPTER1_END = 16
local CHAPTER2_END = 23

-- 16834 is the default LiveSplit WebSocket port.
-- You can change it in LiveSplit → Edit Layout → LiveSplit Server → Settings, just make sure to apply that change here too.
local ws = WebSocket.connect("ws://127.0.0.1:16834/livesplit")

ws.OnClose:Connect(function()
    print("Disconnected from LiveSplit")
end)

print("Connected to LiveSplit!")

local lastLevel = levelValue.Value

levelValue:GetPropertyChangedSignal("Value"):Connect(function()
    local newLevel = levelValue.Value
    if newLevel > lastLevel then
        ws:Send("split")
        
        -- Switch according to the category you are running
        if newLevel == PRE_CLASSIC_END then
            ws:Send("pause")
        end

        lastLevel = newLevel
    end
end)
```
