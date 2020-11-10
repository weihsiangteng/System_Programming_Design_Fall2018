---
title: 自己寫第一個作業系統
published: true
---

1. 利用 virtual box 建立一個虛擬機器，類型選Other，版本為DOS，記憶體大小為32MB，硬碟選VDI，500MB，即初始化完成。按開機會顯示
```
FATAL: No bootable medium found! System halted.
```
因為沒有OS!

2. 先建立一個HelloWorld.asm的檔案。
```
[BITS 16]                             ; We will start with 16 bits bootable program (一開始無法直接跑64bit的程式)
[ORG 0x7C00]                          ; The program starts at 0x7c00 (X86 processor已定義好從0x7C00開始拿程式來跑)

main:
		mov si, msg                   ; move the message to be printed to SI register
		jmp printstr				  ; Jump to the function to print the string
printstr:
		lodsb                         ; Copy the byte prointed by DS:SI
		or al, al                     ; Set ZF to zero if we reach end of the string
		jz hang                       ; If reach the end of string jump to hang to terminate printstr
		mov ah, 0x0e                  ; Move the instruction code to AH
		int 10h                       ; Generate an interrupt to trigger video services, specified in AH
		jmp printstr                  ; Jump to printstr to print next character
hang:
		jmp hang
		
		msg db "Hello to the class of SP 2017!!", 0                 ; db (double byte), 0 (字串結束)
end:
		times 510 - ($ - $$) db 0                                   ; Fill the space with zero (中間不能是亂碼)
		dw 0xAA55                     ; bootable medium should end with 0xAA55 for x86 processor

```
3. 將HelloWorld.asm組譯成bin檔，在將其轉為映像檔。
```bash=
nasm -f bin -o HelloWorld.bin HelloWorld.asm
dd if=HelloWorld.bin of=HelloWorld.img
```
4. 在剛剛建立的virtual box DOS上選設定值->存放裝置->Floppy加入HelloWorld.img。
5. 啟動之後，即會秀出你要印的字串!

如果你有qemu也可以使用
```
qemu-system-i386 HelloWorld.img
```
