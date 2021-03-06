# LuaPreprocess

[![](https://img.shields.io/github/release/ReFreezed/LuaPreprocess.svg)](https://github.com/ReFreezed/LuaPreprocess/releases/latest)

A small and straightforward Lua preprocessor featuring a simple syntax.
Write embedded metaprograms to generate code using normal Lua inside your Lua files.

*LuaPreprocess* is written in pure Lua.
[The library](preprocess.lua) is a single file with no external dependencies.
[MIT license](LICENSE.txt).
A separate [command line program](preprocess-cl.lua) is available too.

- [Example program](#example-program)
- [Usage](#usage)
	- [Library](#preprocess-files-using-the-library)
	- [Command line](#preprocess-files-from-the-command-line)



## Example Program
The exclamation mark (*!*) is used to indicate what code is part of the metaprogram. ([Screenshot](example.png))

```lua
-- Normal Lua.
local n = 0
doSomething()

-- Preprocessor lines.
initGame()
!if IS_DEVELOPER then
	enableCheats()
!end

function doNetworkStuff()
	!for i = 1, 3 do
		local success = connectToServer()
		if success then  return "Connected!"  end
	!end
	return "Failed to connect after 3 tries"
end

-- Extended preprocessor line. (Lines are consumed until brackets
-- are balanced when the end of the line is reached.)
!defineClass{
	name  = "Entity",
	props = {x=0, y=0},
}

-- Preprocessor block.
!(
local hashLib = require("md5")
function getHash()
	return hashLib.calculate("Hello, world!")
end
)

-- Preprocessor inline block. (Expression that returns a value.)
local text = !("Precalculated hash: "..getHash())

-- Preprocessor inline block variant. (Expression that returns a Lua string.)
!!("myRandomGlobal"..math.random(9)) = "foo"
```

#### Output
```lua
-- Normal Lua.
local n = 0
doSomething()

-- Preprocessor lines.
initGame()
enableCheats()

function doNetworkStuff()
	local success = connectToServer()
	if success then  return "Connected!"  end
	local success = connectToServer()
	if success then  return "Connected!"  end
	local success = connectToServer()
	if success then  return "Connected!"  end

	return "Failed to connect after 3 tries"
end

-- Extended preprocessor line. (Lines are consumed until brackets
-- are balanced when the end of the line is reached.)
function newEntity() return {__name="Entity",x=0,y=0} end

-- Preprocessor block.

-- Preprocessor inline block. (Expression that returns a value.)
local text = "Precalculated hash: C62D9E6C179835E2D4D2F4613BA054B5948D00E7"

-- Preprocessor inline block variant. (Expression that returns a Lua string.)
myRandomGlobal4 = "foo"
```

See the [examples folder](examples) for more.



## Usage
First you of course need [Lua](https://www.lua.org/versions.html) installed on your system. (Binaries can be
downloaded from [LuaBinaries via SourceForge](https://sourceforge.net/projects/luabinaries/files/5.1.5/Tools%20Executables/)
if you don't want to, or can't, compile Lua from source. For Windows I can recommend installing
[LuaForWindows](https://github.com/rjpcomputing/luaforwindows) which is a "batteries included" Lua package.)


### Preprocess files using the library
```lua
local pp = require("preprocess")

local info, err = pp.processFile{
	pathIn   = "app.lua2p",    -- This is the file we want to process.
	pathMeta = "app.meta.lua", -- Temporary output file for the metaprogram.
	pathOut  = "app.lua",      -- The output path.
}

if not info then
	error(err)
end

print("Lines of code processed: "..info.lineCount)
```

See the top of [preprocess.lua](preprocess.lua) for documentation.


### Preprocess files from the command line

#### Windows
```batch
Preprocess.cmd [options] filepath1 [filepath2 ...]
```

#### Any System
```batch
lua preprocess-cl.lua [options] filepath1 [filepath2 ...]
```

If a filepath is, for example, `C:/MyApp/app.lua2p` then *LuaPreprocess* will write the processed file to `C:/MyApp/app.lua`.

See the top of [preprocess-cl.lua](preprocess-cl.lua) and [preprocess.lua](preprocess.lua) for the options and more documentation.


