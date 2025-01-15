- Questions:
	- how can modern CPU execute more than 1 instructions per cycle?
- Stages for a pipeline
  collapsed:: true
	- `Fetch`
	- `Read`
	- `ALU`
	- `MEM`
	- `WR`
- Branch in a pipeline
  collapsed:: true
	- `BEQ R1, R2, Label`
		- If R1 == R2, will add the immediate operand (label) to the program counter, `PC++`
		- If R1 != R2, just increment the program counter by the size of the branch instruction so that the program counter can point to the very next instruction, `PC = PC +4 + Immediate`
	- From pipeline perspective:
		- ![image.png](../assets/image_1713665233363_0.png){:height 335, :width 690}
		- 1. Won't able to know the next instruction program counter until ALU finishes executing.
			- If mis-predict, 2 cycles penalty.
		- 2. Always prefetch instructions, sometimes we can predict correctly, then free money
- Branch prediction requirements
  collapsed:: true
	- If we successfully predict, we need:
		- ![image.png](../assets/image_1713665450657_0.png){:height 143, :width 543}
		- 困难的部分是，1. 我们在取指阶段并不知道这个instruction的code，2. 也不知道到底成功率多少，3. 也不知道不成立的时候另外一个branch的PC
- Branch prediction accuracy
  collapsed:: true
	- CPI calculation with respect to branching:
		- ![image.png](../assets/image_1713665595067_0.png){:height 158, :width 398}
	- Example:
		- 20% of all instructions are branches
		- ![image.png](../assets/image_1713665979434_0.png){:height 283, :width 565}
	- [Branch prediction benefit Quiz](https://learn.udacity.com/courses/ud007/lessons/25a31029-cd2d-4ac7-b6f5-035df495d8b6/concepts/a5881153-7ac8-465a-a682-87fb6ed84992/instructions) #card
	  ![image.png](../assets/image_1713666281274_0.png){:height 373, :width 745}
		- ![image.png](../assets/image_1713666369276_0.png){:height 405, :width 689}
		- We don't know the add is not a branch code until we fetch and decode, so if we fetch nothing until we are sure, we are going to end up taken 2 cycles for every add instruction. Same for branch instruction, we don't know the next PC until ALU stage, so it creates two bubbles for the fetch and decode stage. So 3 cycles.
- Performance with Not taken prediction
  collapsed:: true
	- The performance benefit of not taken prediction, cheap prediction, just always increment the PC
		- ![image.png](../assets/image_1713666551734_0.png){:height 261, :width 692}
	- Multiple Predictions Quiz #card
	  ![image.png](../assets/image_1713666710216_0.png){:height 412, :width 728}
		- ![image.png](../assets/image_1713666870998_0.png){:height 390, :width 691}
- Prediction Not taken
  collapsed:: true
	- Increment PC
	- Rule of thumb
		- 20% of instructions are branches
		- 60% of branches are taken
		- ![image.png](../assets/image_1713666988395_0.png){:height 174, :width 513}
- Why we need better prediction
  collapsed:: true
	- Comparison of different predictor accuracy
		- Deeper the pipeline, better the speedup
		- Less the ideal CPI, better the speedup
		- ![image.png](../assets/image_1713667458106_0.png){:height 230, :width 460}
	- Predictor Impact quiz #card
	  ![image.png](../assets/image_1713667543857_0.png)
		- 0.56
- Why we need better prediction part 2
  collapsed:: true
	- ![image.png](../assets/image_1713667914908_0.png){:height 231, :width 722}
	- For 4 instruction/cycle case, the next instructions fetched for the next stage is 4
- Better prediction - How?
  collapsed:: true
	- The next program counter can be considered as a function of the current program counter and the history program counter
		- ![image.png](../assets/image_1713668677136_0.png){:height 234, :width 491}
- Branch target buffer
	- Practice:
		- BTB is a look up table, for each $$PC_{now}$$, we can use that as key to look up for the best guess of  $$PC_{new}$$.
		- Update the BTB table if we end up with a mis prediction and have a entry of $$PC_{now}: PC_{new}$$
	- Problem, how big is the BTB:
		- The BTB can be as large as the program itself, this is because we are storing the program counter, which is 8-Byte for every entry.
		- ![image.png](../assets/image_1713669035282_0.png){:height 296, :width 730}
	- Realistic BTB:
	  collapsed:: true
		- How to make BTB small?
			- Example, suppose we only have 1024 entries can be accessed. How to map each PC to an entry?
			- Take least significant 10 bits to index the new PC.
			- ![image.png](../assets/image_1713669385541_0.png){:height 279, :width 611}
		- BTB quiz #card
		  ![image.png](../assets/image_1713669668073_0.png){:height 299, :width 686}
			- 0x2C3
			- Because the instructions are 4-byte aligned, so the address for the last 10-bit is really sparse for the instructions.
			- So we need to ignore the last 0x00 and re-calculate the instruction entry to make better use of the 1024 entries.
			- In this case, 0xB0C --> ignore the last 0b00, is 0x2C3 (0b101100001100)
- Direction Predictor
	- Build Branch history table (BHT) to store if the branch is taken branch or not taken.
	- Since only need one bit to store if it is taken or not, the table can be quite large compare to BTB
	- We can use BHT to check if that branch is taken, then use BTB to check the target PC only for branches in the code
	- ![image.png](../assets/image_1713670137604_0.png){:height 249, :width 614}
	- BTB and BHT Quiz #card 
	  collapsed:: true
	  :LOGBOOK:
	  CLOCK: [2024-04-21 Sun 11:29:51]--[2024-04-21 Sun 11:29:52] =>  00:00:01
	  :END:
	  ![image.png](../assets/image_1713670287029_0.png){:height 424, :width 717}
		- ![image.png](../assets/image_1713670603819_0.png){:height 339, :width 598}
	- BTB and BHT Quiz 2 #card
	  collapsed:: true
	  ![image.png](../assets/image_1713670678370_0.png){:height 369, :width 654}
		- 0, 1, 2, 3, 4, 5, 6, 7, 8
		- Since only 16 entry, so it is address >> 4 & 16
	- BTB and BHT Quiz 3 #card
	  ![image.png](../assets/image_1713670931324_0.png){:height 390, :width 711}
		- ![image.png](../assets/image_1713671004353_0.png){:height 390, :width 727}
	- BTB and BHT Quiz 4 #card
	  ![image.png](../assets/image_1713671170847_0.png){:height 398, :width 733}
		- 2 for `0xC008`
		- 3 for `0xC01C`
	- BTB and BHT Quiz 5 #card
	  ![image.png](../assets/image_1713671565967_0.png){:height 386, :width 697}
		- once for `0xc008` and once for `0xc01c`
- Problems with 1 bit predictions
	- Mis predict twice when there is anomaly in the predictions, so this will end up being pretty bad predictor for cases where taken is equal to Not taken
	- ![image.png](../assets/image_1713672035506_0.png){:height 369, :width 705}
- 2-Bit predictor (2BP, 2BC)
	- Need two Taken to go from 0x00 to 0x10 and two not taken to go from 0x11 to 0x01
	- ![image.png](../assets/image_1713672613466_0.png){:height 370, :width 716}
- 2-Bit predictor intialization
	- Every predictor have a scenario where the prediction is 100% wrong
	- Doesn't matter that much in the long run, might as well start from 0x00 since that is more easy to initialze
	- ![image.png](../assets/image_1713672926558_0.png){:height 450, :width 754}
	- 2BP quiz #card
	  ![image.png](../assets/image_1713673111208_0.png){:height 371, :width 842}
		- T T N T N
- 1BP, 2BP
	- Stay with 2BP, maybe go to 3BP...
	- ![image.png](../assets/image_1713677124872_0.png){:height 191, :width 671}
- History based predictors
	- Some patterns should be easily predicted by human based on the patterns
	- How to design a predictor that can successfully predict such instructions branching
	- ![image.png](../assets/image_1713677651515_0.png){:height 432, :width 789}
	- 1 bit history with 2 2-bit count
	  collapsed:: true
		- The practice is the same: Take some part of the PC and index into the BHT
		- But instead of one-bit counter or 2-bit counter for that entry, we use single bit history and 2 of the bit-counters
		- For the following example, this predictor can learn the pattern very quickly
		- ![image.png](../assets/image_1713677946318_0.png){:height 385, :width 715}
		- 1-bit history Quiz #card
		  ![image.png](../assets/image_1713678132536_0.png){:height 328, :width 391}
			- 100
	- 2-bit history predictor
	  collapsed:: true
		- Can predict the example from previous quiz, *(NNT)
			- ![image.png](../assets/image_1713680073768_0.png){:height 401, :width 725}
		- Basically, N-bit history predictor can predict patterns of length <= N + 1, but the entry size is going to be very large
			- ![image.png](../assets/image_1713680693521_0.png){:height 403, :width 729}
		- N-bit history predictor Quiz #card
		  ![image.png](../assets/image_1713680922831_0.png){:height 342, :width 632}
			- ![image.png](../assets/image_1713681106792_0.png){:height 314, :width 698}
		- History Predictor Quiz #card
		  ![image.png](../assets/image_1713681529919_0.png){:height 413, :width 724}
			- ![image.png](../assets/image_1713681829827_0.png){:height 233, :width 561}
			- pattern (NNNNNNNNT)*
			- 256 --> 2 ^ 8
	- History with shared counters
	  collapsed:: true
		- Only part of the counters are used for N-bit history
			- ![image.png](../assets/image_1713683166357_0.png){:height 225, :width 703}
		- Add one extra level of pattern history table
			- pattern history table: Keep the history bits alone for that branch
			- So if we have 11-bit history, the table is going to be 11-bit each entry
				- ![image.png](../assets/image_1713683592263_0.png){:height 357, :width 702}
			- Cost for the solution:
				- Suppose take the lower 11-bit from the PC, then we need $$2^{11}$$ entries in the PHT, each PHT would have 11-bit so that we can xor that to the lower 11-bit from PC. On top of that is the 2-bit counters for the BHT. That combined would result in:
					- ![image.png](../assets/image_1713683826123_0.png){:height 126, :width 171}
		- Example:
		  collapsed:: true
			- Look up the PC in the PHT, if the history is relatively simple, like all Takens or all not takens. The BHT only need 1 counter to store the possible transitions. For cases where the history is more complicated, we can use more counters for those added histories.
			- Potential harm is that we might map to the same BHT entry for different program counter xor PHT history table. But the cases will be very rare if the BHT is large.
			- ![image.png](../assets/image_1713684784260_0.png){:height 393, :width 692}
		- PShare vs GShare
		  collapsed:: true
			- ![image.png](../assets/image_1713685034971_0.png){:height 386, :width 728}
			- PShare vs GShare quiz #card
			  ![image.png](../assets/image_1713685135152_0.png){:height 371, :width 730}
				- Note that there are three branch instructions in the code
				- ![image.png](../assets/image_1713685601912_0.png){:height 97, :width 321}
			- PShare or GShare
			  collapsed:: true
				- We need both!
		- Tournament predictor
		  collapsed:: true
			- Use one meta-predictor to determine which of the two predictor is better
			- The meta-predictor counts up when the prediction of PShare is better and counts down if the GShare is better. So for each PC, we would have one meta predictor to select which predictor to use.
				- ![image.png](../assets/image_1713686599371_0.png){:height 378, :width 671}
		- Hierarchical predictor
		  collapsed:: true
			- ![image.png](../assets/image_1713686866849_0.png){:height 245, :width 756}
			- Example
				- ![image.png](../assets/image_1713687101251_0.png){:height 316, :width 629}
		- Multi-predictor quiz #card
		  collapsed:: true
		  ![image.png](../assets/image_1713687280754_0.png){:height 349, :width 659}
			- E, A, D, B, C
			- Use tournament predictor because the PShare and GShare doesn't have much in common. No one is superior than the other.
	- Return Address Stack
	  collapsed:: true
		- hard to determine where the function is called.
			- ![image.png](../assets/image_1713687624902_0.png){:height 405, :width 645}
		- RAS: hardware component that keep track of the return address of the function call
			- ![image.png](../assets/image_1713687780625_0.png){:height 345, :width 653}
			- ![image.png](../assets/image_1713688096496_0.png){:height 314, :width 657}
			- ![image.png](../assets/image_1713688370850_0.png){:height 375, :width 657}
		-
-