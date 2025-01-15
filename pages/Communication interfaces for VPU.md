### Communication Interfaces
	- #### IPC
- ### Command List
	- command queue is pointer that points to a command in the command lists.
	- A command list represents a sequence of commands for execution that is pointed to by an entry in the command queue. The command list will be built by the application based on commands created by the compiler(s) and UMD.
	- | Initiator | Command | Target Engine | Details/Links | Description |
	  | ---- | ---- | ---- |
	  | L0 application | initialize (DEVICE_COMMAND_TYPE_OV_BLOB_INITIALIZE) | compute | zeCommandListAppendGraphInitialize | perform any blob initialization before first inference |
	  | L0 application | execute (DEVICE_COMMAND_TYPE_OV_BLOB_EXECUTE) | compute | zeCommandListAppendGraphExecute | ==dispatch graph-> send to LeonRT job scheduler== |
	  | L0 application | copy system to local, copy local to system(DEVICE_COMMAND_TYPE_COPY_SYSTEM_TO_LOCAL, DEVICE_COMMAND_TYPE_COPY_LOCAL_TO_SYSTEM) | copy | [zecommandlistappendmemorycopy](https://one-api.gitlab-pages.devtools.intel.com/level_zero/core/api.html#zecommandlistappendmemorycopy) | copy job -> send to PCIe DMA |
	  | L0 application | event(DEVICE_COMMAND_TYPE_FENCE_WAIT, DEVICE_COMMAND_TYPE_FENCE_SIGNAL) | copy and compute | [signal event](https://one-api.gitlab-pages.devtools.intel.com/level_zero/core/api.html#zecommandlistappendsignalevent) | signal event within VPU/KMB between copy and compute jobs |
	  | L0 application | fence wait, fence signal(DEVICE_COMMAND_TYPE_FENCE_WAIT, DEVICE_COMMAND_TYPE_FENCE_SIGNAL) | handled by driver/OS | [fence](https://one-api.gitlab-pages.devtools.intel.com/level_zero/core/api.html#fence) | used to communicate to the host that command queue execution has completed |
	  | L0 application, DX DDI | time stamp (DEVICE_COMMAND_TYPE_TIMESTAMP) | copy and compute | [zecommandlistappendwriteglobaltimestamp](https://one-api.gitlab-pages.devtools.intel.com/level_zero/core/api.html#zecommandlistappendwriteglobaltimestamp) | timestamp -> management by inference manager to measure time stamps for each command sent to LeonRT or ARM copy job dispatcher. |
	  | DXGK scheduler | monitored fence (handled by OS scheduler) | copy and compute |  | used to communicate to the host that command queue execution has completed |
	  | DXGK scheduler | memory fill(DEVICE_COMMAND_TYPE_MEMORY_FILL) | copy |  | clear memory on the device before use |
	  | L0 application | barrier(DEVICE_COMMAND_TYPE_BARRIER) | copy and compute | [zecommandlistappendbarrier](https://one-api.gitlab-pages.devtools.intel.com/level_zero/core/api.html#zecommandlistappendbarrier) | to be used especially in the case of timestamps, or chaining models together where pre-processing is not required on the intermediate activations |
	-