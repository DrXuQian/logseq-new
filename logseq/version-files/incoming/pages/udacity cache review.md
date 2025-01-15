- Locality principle
	- spatial locality and temporal locality
	- Memory preferences
		- ![image.png](../assets/image_1713790955654_0.png){:height 269, :width 959}
	- Temporal locality quiz #card
	  ![image.png](../assets/image_1713791085908_0.png){:height 356, :width 589}
		- ![image.png](../assets/image_1713791103948_0.png){:height 147, :width 166}
		- elements of array have spatial locality
- Cache performance
	- Average memory access time (AMAT)
	- ![image.png](../assets/image_1713792653547_0.png){:height 331, :width 842}
	- Miss time == miss penalty + hit time
		- check if there is a hit, fail that, then add the miss penalty. or you can replace the miss rate with 1 and get the same equation
		- ![image.png](../assets/image_1713792767155_0.png){:height 100, :width 631}
- Cache size in real processors
	- L1 cache 16KB ~ 64 KB
		- ![image.png](../assets/image_1713832954929_0.png){:height 263, :width 590}
		- Large enough to get 90% hit rate
		- small enough to hit in 1~3 cycles
- Cache organization
	- L1 cache line size
		- If too small, can't make good use of spatial locality since the LD/ST is only for one B. Also word access need to access multiple lines
		- If too large, most of the data won't get accessed which would be a waste of precious L1 cache space
		- A moderate number of block size like 32-128 Bytes would be reasonable for one entry
		- ![image.png](../assets/image_1713840527508_0.png){:height 462, :width 799}
- Block size Quiz #card
  ![image.png](../assets/image_1713840756572_0.png){:height 399, :width 840}
	- 512 (not 500...)
- Cache block start address
	- the cache line start address need to be block size aligned
	- ![image.png](../assets/image_1713841424448_0.png){:height 323, :width 816}
- Blocks in cache and memory
	- ![image.png](../assets/image_1713841857596_0.png){:height 440, :width 653}
	- Cache line size quiz #card
	  ![image.png](../assets/image_1713841985340_0.png){:height 388, :width 639}
		- 1B, 48B, 1KB
		- For 48B, we need to divide our address by 48B, which is way too complicated for a quick look up
		- For 32B, the last five bit tell us where the byte we are looking for inside the cache line and the upper bits are the block number or index
- Block offset and block number
	- ![image.png](../assets/image_1713842291398_0.png){:height 462, :width 773}
		- ![image.png](../assets/image_1713842432513_0.png){:height 322, :width 722}
- Cache tags
	- The real question is how to map addr to the index of cache line
	- ![image.png](../assets/image_1713842585152_0.png){:height 398, :width 787}
	- Cache tag quiz #card
	  ![image.png](../assets/image_1713842824327_0.png){:height 271, :width 768}
		- c
- Valid bit
	- Add another valid bit to tell if the tag is valid or not:
	- ![image.png](../assets/image_1713842981414_0.png){:height 288, :width 816}
- Types of caches
	- Fully associated caches: any block can be in any line. We need to check all lines for any block we are looking for
	- set-associated cache: N lines where a block can be. N is 1, it turned into direct-mapped cache. and N == number of lines in the cache, it turned into fully associated cache. Typically, N == 2, 4, 8...
	- direct-mapped cache: a block can go into 1 line
- Direct mapped cache
	- No need to include index in tag since we already know that by looking at the index and route to the corresponding cache line. So only need to compare the tag without the index.
	- Index is associated with the number of cache lines, in this case, 4 == 2^2, so index length is 2
	- ![image.png](../assets/image_1713843785836_0.png){:height 429, :width 718}
	- Upside
		- ![image.png](../assets/image_1713843985337_0.png){:height 138, :width 279}
		- Look in only one place, which is really fast
		- Only one comparison, so cheaper
		- energy efficient
	- Downside
		- Block must go in one place
		- If we get two address frequently accessed but in the same index. The two addresses will get hit and miss all the time
			- ![image.png](../assets/image_1713843969780_0.png){:height 113, :width 717}
	- Direct mapped cache quiz #card
	  ![image.png](../assets/image_1713844211279_0.png){:height 435, :width 767}
		- ![image.png](../assets/image_1713844223055_0.png){:height 241, :width 258}
	- Direct mapped cache quiz 2 #card
	  ![image.png](../assets/image_1713844391572_0.png){:height 392, :width 612}
		- ![image.png](../assets/image_1713844661530_0.png){:height 132, :width 245}
- Set associative caches
	- N-way set associative
		- A block can be in one of N-lines in cache
		- ==Note that N is not the number of sets, rather the number of cache lines within a set==
		- ![image.png](../assets/image_1713844763075_0.png){:height 469, :width 738}
	- Offset, index, tag for set associative
		- ![image.png](../assets/image_1713844901933_0.png){:height 346, :width 686}
	- 2-way set associative cache quiz #card
	  32B cache line
	  ![image.png](../assets/image_1713844941689_0.png){:height 436, :width 673}
		- ![image.png](../assets/image_1713845104571_0.png){:height 387, :width 323}
- Fully associative cache
	- Is just set-associative cache with only 1 set
	- The tag is more than what we have for set-associative, the n of more bits is log2(set)
- Direct mapped and fully associative
	- ![image.png](../assets/image_1713845328237_0.png){:height 368, :width 779}
- Cache replacement
	- ![image.png](../assets/image_1713845457997_0.png){:height 333, :width 703}
	- Implementing LRU
		- for numbers higher than the originally value, decrement by 1
		- change the number accessed to highest LRU count
		- ![image.png](../assets/image_1713847468799_0.png){:height 397, :width 771}
	- LRU quiz #card
	  ![image.png](../assets/image_1713847666616_0.png){:height 423, :width 524}
		- ![image.png](../assets/image_1713847676983_0.png){:height 348, :width 203}
- Write policy
	- write back, would only write to cache when the cache line get replaced, so we need to write the data back to memory for consistency.
	- if we have a write miss, we want to write further data to cache so in write back caches, we would also prefer write allocate so that we can avoid visiting memory all the time for the same logical reason.
	- ![image.png](../assets/image_1713847969980_0.png){:height 403, :width 800}
- Write back caches
	- When we overwrite some block, how do we know if we need to evict that block back to memory
	- For some read-only cachelines, we don't need to write that back to memory since that is not modified.
	- Add a dirty bit to represent if the cache is touched or not:
		- ![image.png](../assets/image_1713848373379_0.png){:height 405, :width 729}
	- Write back cache example:
		- {{video https://www.youtube.com/watch?v=xU0ICkgTLTo}}
	- Write back cache quiz #card
	  ![image.png](../assets/image_1713848802756_0.png){:height 455, :width 763}
		- ![image.png](../assets/image_1713848792210_0.png){:height 450, :width 760}
- Cache summary
  id:: 662741f2-9e13-4620-afe6-0a603daa0647
	- ![image.png](../assets/image_1713849139002_0.png){:height 340, :width 737}
	  id:: 66274329-45de-41d7-9550-803aea7eb664
	- cache summary quiz #card
	  ![image.png](../assets/image_1713849315945_0.png){:height 322, :width 715}
		- ![image.png](../assets/image_1713849410699_0.png){:height 201, :width 660}
	- cache summary quiz #card
	  ![image.png](../assets/image_1713849600452_0.png){:height 370, :width 845}
		- 2, 0
		- Two lines of same set. So two misses
		- Only write when there is a overwritten of cacheline, no such event happened