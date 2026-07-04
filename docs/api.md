# API (exports)

For integrating Luman Weather with your own resources — seasons, photo modes,
quests, hunting scripts.

## Server

```lua
-- Read
local time     = exports['luman-weather']:getTime()      -- {day, hour, minute, second}
local weather  = exports['luman-weather']:getWeather()   -- "sunny"
local wind     = exports['luman-weather']:getWind()      -- {direction, speed}
local forecast = exports['luman-weather']:getForecast()  -- {{day, hour, ..., weather, wind}, ...}

-- Write (broadcast to all players immediately)
exports['luman-weather']:setWeather(weather, transitionSec, freeze, permanentSnow)
exports['luman-weather']:setTime(day, hour, minute, second, transitionMs, freeze)
exports['luman-weather']:setTimescale(scale)             -- 0 = real time
exports['luman-weather']:setWind(direction, speed, freeze)
exports['luman-weather']:setWeatherPattern(pattern)      -- same format as config

-- Reset to config defaults
exports['luman-weather']:resetWeather()
exports['luman-weather']:resetTime()
exports['luman-weather']:resetTimescale()
exports['luman-weather']:resetWind()
exports['luman-weather']:resetWeatherPattern()
```

### Example: winter season

```lua
exports['luman-weather']:setWeatherPattern({
    ["snowlight"] = { ["snowlight"] = 50, ["snow"] = 30, ["clouds"] = 20 },
    ["snow"]      = { ["snow"] = 40, ["snowlight"] = 40, ["blizzard"] = 20 },
    ["blizzard"]  = { ["snow"] = 70, ["blizzard"] = 30 },
    ["clouds"]    = { ["clouds"] = 40, ["snowlight"] = 60 }
})
exports['luman-weather']:setWeather("snow", 30.0, false, true)  -- true = snow on the ground
```

## Client

```lua
-- Is there snow on the ground? (for footprints, sounds, etc.)
local snowy = exports['luman-weather']:isSnowOnGround()

-- Personal time/weather for this player only (detaches from sync)
exports['luman-weather']:setMyTime(hour, minute, second, transitionMs)
exports['luman-weather']:setMyWeather(weather, transitionSec, permanentSnow)

-- Attach/detach from server sync
exports['luman-weather']:setSyncEnabled(true)
exports['luman-weather']:toggleSync()
```

### Example: photo mode

```lua
-- while the camera is open
exports['luman-weather']:setMyTime(19, 30, 0, 500)

-- when it closes
exports['luman-weather']:setSyncEnabled(true)
```

## Events

Everything above is also available as `luman-weather:*` server events
(`TriggerEvent("luman-weather:setWeather", ...)` from server code works like
the export). Events arriving **from clients** are checked against the same
ace permissions as the admin commands, so players can't abuse them.
