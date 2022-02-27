---
title: golang是怎么实现函数返回多个值的
categories: golang
---

```go
package main

import "fmt"

func test(i, j int) (int, int) {
	a := i + j
	b := i - j
	return a, b
}

func main() {
	a, b := test(2, 1)
	fmt.Println(a, b)
}
```

例子很简单，主要是说明golang多值返回的过程，下面编译成汇编

```
go tool compile -S test.go > test.s
```

```
"".test STEXT size=16 args=0x10 locals=0x0 funcid=0x0 align=0x0 leaf
	0x0000 00000 (test.go:5)	TEXT	"".test(SB), LEAF|NOFRAME|ABIInternal, $0-16
	0x0000 00000 (test.go:5)	FUNCDATA	ZR, gclocals·33cdeccccebe80329f1fdbee7f5874cb(SB)
	0x0000 00000 (test.go:5)	FUNCDATA	$1, gclocals·33cdeccccebe80329f1fdbee7f5874cb(SB)
	0x0000 00000 (test.go:5)	FUNCDATA	$5, "".test.arginfo1(SB)
	0x0000 00000 (test.go:5)	FUNCDATA	$6, "".test.argliveinfo(SB)
	0x0000 00000 (test.go:5)	PCDATA	$3, $1
	0x0000 00000 (test.go:6)	ADD	R1, R0, R2
	0x0004 00004 (test.go:7)	SUB	R1, R0, R1
	0x0008 00008 (test.go:8)	MOVD	R2, R0
	0x000c 00012 (test.go:8)	RET	(R30)
	0x0000 02 00 01 8b 01 00 01 cb e0 03 02 aa c0 03 5f d6  .............._.
"".main STEXT size=144 args=0x0 locals=0x58 funcid=0x0 align=0x0
	0x0000 00000 (test.go:11)	TEXT	"".main(SB), ABIInternal, $96-0
	0x0000 00000 (test.go:11)	MOVD	16(g), R16
	0x0004 00004 (test.go:11)	PCDATA	$0, $-2
	0x0004 00004 (test.go:11)	MOVD	RSP, R17
	0x0008 00008 (test.go:11)	CMP	R16, R17
	0x000c 00012 (test.go:11)	BLS	132
	0x0010 00016 (test.go:11)	PCDATA	$0, $-1
	0x0010 00016 (test.go:11)	MOVD.W	R30, -96(RSP)
	0x0014 00020 (test.go:11)	MOVD	R29, -8(RSP)
	0x0018 00024 (test.go:11)	SUB	$8, RSP, R29
	0x001c 00028 (test.go:11)	FUNCDATA	ZR, gclocals·69c1753bd5f81501d95132d08af04464(SB)
	0x001c 00028 (test.go:11)	FUNCDATA	$1, gclocals·ef901d0ae51b5399f7d4b5dfa3bc0b42(SB)
	0x001c 00028 (test.go:11)	FUNCDATA	$2, "".main.stkobj(SB)
	0x001c 00028 (<unknown line number>)	NOP
	0x001c 00028 (test.go:12)	STP	(ZR, ZR), ""..autotmp_16-32(SP)
	0x0020 00032 (test.go:13)	STP	(ZR, ZR), ""..autotmp_16-16(SP)
	0x0024 00036 (test.go:13)	MOVD	$3, R0
	0x0028 00040 (test.go:13)	PCDATA	$1, $1
	0x0028 00040 (test.go:13)	CALL	runtime.convT64(SB)
	0x002c 00044 (test.go:13)	MOVD	$type.int(SB), R1
	0x0034 00052 (test.go:13)	MOVD	R1, ""..autotmp_16-32(SP)
	0x0038 00056 (test.go:13)	MOVD	R0, ""..autotmp_16-24(SP)
	0x003c 00060 (test.go:13)	MOVD	$1, R0
	0x0040 00064 (test.go:13)	CALL	runtime.convT64(SB)
	0x0044 00068 (test.go:13)	MOVD	$type.int(SB), R1
	0x004c 00076 (test.go:13)	MOVD	R1, ""..autotmp_16-16(SP)
	0x0050 00080 (test.go:13)	MOVD	R0, ""..autotmp_16-8(SP)
	0x0054 00084 (<unknown line number>)	NOP
	0x0054 00084 (<unknown line number>)	PCDATA	$0, $-3
	0x0054 00084 ($GOROOT/src/fmt/print.go:274)	MOVD	os.Stdout(SB), R1
	0x0060 00096 ($GOROOT/src/fmt/print.go:274)	PCDATA	$0, $-1
	0x0060 00096 ($GOROOT/src/fmt/print.go:274)	MOVD	$go.itab.*os.File,io.Writer(SB), R0
	0x0068 00104 ($GOROOT/src/fmt/print.go:274)	MOVD	$""..autotmp_16-32(SP), R2
	0x006c 00108 ($GOROOT/src/fmt/print.go:274)	MOVD	$2, R3
	0x0070 00112 ($GOROOT/src/fmt/print.go:274)	MOVD	R3, R4
	0x0074 00116 ($GOROOT/src/fmt/print.go:274)	PCDATA	$1, ZR
	0x0074 00116 ($GOROOT/src/fmt/print.go:274)	CALL	fmt.Fprintln(SB)
	0x0078 00120 (test.go:14)	MOVD	-8(RSP), R29
	0x007c 00124 (test.go:14)	MOVD.P	96(RSP), R30
	0x0080 00128 (test.go:14)	RET	(R30)
	0x0084 00132 (test.go:14)	NOP
	0x0084 00132 (test.go:11)	PCDATA	$1, $-1
	0x0084 00132 (test.go:11)	PCDATA	$0, $-2
	0x0084 00132 (test.go:11)	MOVD	R30, R3
	0x0088 00136 (test.go:11)	CALL	runtime.morestack_noctxt(SB)
	0x008c 00140 (test.go:11)	PCDATA	$0, $-1
	0x008c 00140 (test.go:11)	JMP	0

```

