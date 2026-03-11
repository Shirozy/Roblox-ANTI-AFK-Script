# Roblox Anti AFK Script

A simple script that prevents a Roblox player from being marked idle by simulating input when the `Idled` event fires.

This script is intended as a learning example for:
- Roblox `LocalScript` behavior
- `Players.LocalPlayer`
- event connections with `:Connect()`
- using `VirtualUser`
- basic client-side scripting structure

## Script

```lua
local Players = game:GetService("Players")
local player = Players.LocalPlayer

local VirtualUser = game:GetService("VirtualUser")

local currentCamera = game.Workspace.CurrentCamera

player.Idled:Connect(function()
	VirtualUser:Button2Down(Vector2.zero, currentCamera.CFrame)
	task.wait(1)
	VirtualUser:Button2Up(Vector2.zero, currentCamera.CFrame)
	
	print("Player Successfully UnIdled.")
end)

print("Anti AFK Active")
````

---

## How it works

When the local player becomes idle, Roblox fires the `Idled` event.

This script listens for that event and then:

1. simulates a right mouse button press with `Button2Down`
2. waits for 1 second
3. simulates releasing the button with `Button2Up`

That simulated activity is used to interrupt the idle state.

---

## Breakdown

### 1. Get the `Players` service

```lua
local Players = game:GetService("Players")
```

This gets the Roblox `Players` service, which is used to access player objects.

---

### 2. Get the local player

```lua
local player = Players.LocalPlayer
```

`LocalPlayer` refers to the player running the game on their own client.

This only works inside a **LocalScript**.

---

### 3. Get `VirtualUser`

```lua
local VirtualUser = game:GetService("VirtualUser")
```

`VirtualUser` is the service used to simulate user input.

In this script, it is used to simulate mouse button activity.

---

### 4. Get the current camera

```lua
local currentCamera = game.Workspace.CurrentCamera
```

The script stores the current camera so it can pass the camera `CFrame` into the `VirtualUser` methods.

---

### 5. Listen for the player becoming idle

```lua
player.Idled:Connect(function()
```

This connects a function to the player's `Idled` event.

When the player has been inactive long enough, this function runs automatically.

---

### 6. Simulate activity

```lua
VirtualUser:Button2Down(Vector2.zero, currentCamera.CFrame)
task.wait(1)
VirtualUser:Button2Up(Vector2.zero, currentCamera.CFrame)
```

This simulates:

* right mouse button down
* short delay
* right mouse button up

The purpose is to create enough input activity to interrupt the idle state.

---

### 7. Debug output

```lua
print("Player Successfully UnIdled.")
print("Anti AFK Active")
```

These `print()` calls are useful during testing so you can see when:

* the script starts
* the anti-idle logic actually runs

---

## Better version

The current script works as a simple example, but here is a slightly safer and cleaner version:

```lua
local Players = game:GetService("Players")
local VirtualUser = game:GetService("VirtualUser")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer

player.Idled:Connect(function()
	local currentCamera = Workspace.CurrentCamera
	if not currentCamera then
		warn("CurrentCamera not found.")
		return
	end

	VirtualUser:Button2Down(Vector2.zero, currentCamera.CFrame)
	task.wait(1)
	VirtualUser:Button2Up(Vector2.zero, currentCamera.CFrame)

	print("Player successfully un-idled.")
end)

print("Anti AFK active.")
```

### Why this version is better

It improves a few things:

* gets `CurrentCamera` when needed instead of storing it once
* checks that the camera exists before using it
* keeps naming and formatting more consistent

---

### Use events instead of loops

Using:

```lua
player.Idled:Connect(function()
```

is cleaner than running a loop that constantly checks for idle state.

### Keep the script small and focused

This script does one job:
detect idle state and simulate activity.

That makes it easy to understand and explain.

---

## Things to improve

For production-quality scripting, you could improve this further by:

* wrapping behavior in functions
* checking for nil camera references
* storing event connections if you want enable/disable support
* separating UI code from script logic if you build a GUI version

---

## Example with functions

```lua
local Players = game:GetService("Players")
local VirtualUser = game:GetService("VirtualUser")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer

local function simulateActivity()
	local camera = Workspace.CurrentCamera
	if not camera then
		return
	end

	VirtualUser:Button2Down(Vector2.zero, camera.CFrame)
	task.wait(1)
	VirtualUser:Button2Up(Vector2.zero, camera.CFrame)
end

player.Idled:Connect(function()
	simulateActivity()
	print("Player successfully un-idled.")
end)

print("Anti AFK active.")
```

This version is easier to maintain because the input simulation logic is separated into its own function.
