---
title: "errno 到底是个啥"
date: 2023-07-08T21:54:35+08:00
tags: ["c", "linux", "errno"]
draft: false
---

我们都知道 errno，是存放系统调用错误的地方，其位于 `<errno.h>` 中，但是
它到底是怎么来的？系统是在什么时候设置的这个值的？毕竟系统调用 `syscall` 只会返回 -1 来表示当前系统调用出错……

实际上，在使用系统调用的时候，我们通过一系列 syscall 获取的返回值是已经被 `libc` 
覆写过的了

<!--more-->

## Show me the code

*https://codebrowser.dev/glibc/glibc/sysdeps/unix/sysv/linux/riscv/syscall.c.html#30*

这里我们以 `risc-v` 架构上的代码为例，我们可以看到 syscall 的具体实现:

```c
long int
syscall (long int syscall_number, long int arg1, long int arg2, long int arg3,
	 long int arg4, long int arg5, long int arg6, long int arg7)
{
  long int ret;
  ret = INTERNAL_SYSCALL_NCS_CALL (syscall_number, arg1, arg2, arg3, arg4,
				   arg5, arg6, arg7);
  if (INTERNAL_SYSCALL_ERROR_P (ret))
    return __syscall_error (ret);
  return ret;
}
```

这里有个宏 `INTERNAL_SYSCALL_ERROR_P`，其展开后是
`((unsigned long int) (val) > -4096UL)`

这里 -4096UL 的十六进制是 `FFFF FFFF FFFF F000`

我们可以看到实际内核态执行的 syscall 的返回值就是 errno，只不过被 libc 包装了一下

将 errno 的值设置到了线程全局变量 `__libc_errno` 中，之后用户态的 `errno` 
实际是个读取 `__libc_errno` 的宏

## 总结

libc 检查系统调用是否失败，失败后会设置 errno，之后返回的值是 -1

可以看到 `__syscall_error` 的具体实现:

```asm
ENTRY (__syscall_error)
	mv t0, ra
	/* Fall through to __syscall_set_errno.  */
END (__syscall_error)
/* Non-standard calling convention: argument in a0, return address in t0,
   and clobber only t1.  */
ENTRY (__syscall_set_errno)
	/* We got here because a0 < 0, but only codes in the range [-4095, -1]
	  represent errors.  Otherwise, just return the result normally.  */
	li t1, -4096
	bleu a0, t1, 1f
	neg a0, a0
#if RTLD_PRIVATE_ERRNO
	sw a0, rtld_errno, t1
#elif defined(__PIC__)
	la.tls.ie t1, errno
	add t1, t1, tp
	sw a0, 0(t1)
#else
	lui t1, %tprel_hi(errno)
	add t1, t1, tp, %tprel_add(errno)
	sw a0, %tprel_lo(errno)(t1)
#endif
	li a0, -1
1:	jr t0
END (__syscall_set_errno)
```
