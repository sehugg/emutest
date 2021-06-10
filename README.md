# emutest

A simple test framework for libretro cores.

Features:

 * Lua scriptable
 * Load core using dlopen
 * Load games
 * Run core
 * Dump video framebuffer
 * Dump video geometry and pitch
 * Dump SRAM
 * Load SRAM
 * Dump Savestate
 * Load Savestate
 * Set core options
 * Record and filter core logs
 * Screenshots

## Setup

```
go install github.com/kivutar/emutest@latest
```

## Usage:

Example:

```
emutest test_coproc.lua
```

With a test file testcoproc.lua:

```
set_options_string("mesen-s_hle_coprocessor = \"enabled\"")
load_core("../mesens/Libretro/mesens_libretro.dylib")
load_game("../roms/Nintendo - Super Nintendo Entertainment System/Super Mario Kart (Europe).zip")

local logs = get_logs()

if not string.find(logs, "Coprocessor: DSP1B") then
	error("missing dsp log")
end

for i=1,20 do run() end
local _, _, _, frame1 = get_video()
for i=1,500 do run() end
local _, _, _, frame2 = get_video()
if frame1 == frame2 then
	error("hle coproc not working")
end

local state = get_state()
if string.len(state) ~= 542720 then
	error("wrong savestate size")
end

screenshot("../mkart.png")
```
