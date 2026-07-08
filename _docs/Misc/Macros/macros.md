---
title: Lua snippets for WoW macros 
parent: Macros
nav_order: 40
---

# Iterate through all bag slots , search for item by name 

```lua
/run for b=0,4 do for s=1,GetContainerNumSlots(b,s) do local n=GetContainerItemLink(b,s) if n and string.find(n,"Something") then <something>  end; end; end
```


# Resources 

[WoW Addon Resources](https://github.com/NSO73/wow-vanilla-addon-resources)
[WoW Vanilla API](https://vanilla-wow-archive.fandom.com/wiki/World_of_Warcraft_API)
