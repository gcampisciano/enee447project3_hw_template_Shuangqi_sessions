# ENEE447 Project 3


## Environment setup
- SD card: format it to be FAT32 if you haven't done so
- 32-bit cross-compiler:
	follow the setup in [project 1](https://github.com/sklaw/enee447project1_hw_template) if you haven't done so
  
## You're supposed to modify the following files
- [trap_handler.c](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/trap_handler.c)
- [syscalls.c](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/syscalls.c)
- [io.c](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/io.c)

## Readings
- [p3.pdf](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/p3.pdf)

## What's happening in this kernel
- When our kernel runs, it'll first hit the first instruction in [`1_boot.s`](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/1_boot.s#L3) since we let `1_boot.o` become the entry point of our kernel [by doing this](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/memmap#L10)
- Then it'll jump to `res_handler `. 
	- Since every core of the CPU will execute the same code, we will do some core ID testing [here](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/1_boot.s#L30-L38) and let only core 0 to move on (other cores will hit `hang` and stop).
- Core 0 then will execute `init_kernel` [from here](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/1_boot.s#L70),
	- Then it will execute `init_io` [from here](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/kernel.c#L24)
		- Then it will iterate over an array of devices and call their init functions [here](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/io.c#L118-L120)
			- NOTE: Basically one device is abstracted as a [`struct dev`](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/io.h#L6-L12) and they're all set up [here](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/io.c#L66-L109)

- Core 0 then will execute `run_shell` from [here](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/1_boot.s#L75)
	- `run_shell` basically reads/writes some data form/to devices via the following system calls
		- syscall_write_stream 
		- syscall_write_word
		- syscall_read_stream  
		- syscall_read_word 
	- The choice of making which of the four function calls is dependent on
		- whether you want to read or write
		- whether the target device is communicated through word or stream (this detail is documented in [p3.pdf](https://github.com/sklaw/enee447project3_hw_template_Shuangqi_sessions/blob/master/p3.pdf))

## About making a system call
