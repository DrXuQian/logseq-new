---
title: cache and register
---
- related notes: [[register in x86-64 instruction set]]
- [[code::dive conference 2014 - Scott Meyers: Cpu Caches and Why You Care ]]
- [[Why software developers should care about CPU caches]]
- Cache hit latency quick refence
	 - Core i7 Xeon 5500 Series Data Source Latency (approximate)
		 - > local  L1 CACHE hit,                                                     ~4 cycles (   2.1 -  1.2 ns )
local  L2 CACHE hit,                                                        ~10 cycles (   5.3 -  3.0 ns )
local  L3 CACHE hit, line unshared                                ~40 cycles (  21.4 - 12.0 ns )
local  L3 CACHE hit, shared line in another core          ~65 cycles (  34.8 - 19.5 ns )
local  L3 CACHE hit, modified in another core              ~75 cycles (  40.2 - 22.5 ns )

remote L3 CACHE (Ref: Fig.1 [Pg. 5])                            ~100-300 cycles ( 160.7 - 30.0 ns )

local  DRAM                                                                     ~60 ns
remote DRAM                                                                  ~100 ns
	 - "NOTE: THESE VALUES ARE ROUGH APPROXIMATIONS. **THEY DEPEND ON CORE AND UNCORE FREQUENCIES, MEMORY SPEEDS, BIOS SETTINGS, NUMBERS OF DIMMS**, ETC,ETC..**YOUR MILEAGE MAY VARY.**"