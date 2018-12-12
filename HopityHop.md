<h1>Otter CTF 2018</h1>

<h2> Hopity Hop {Reverse Engineering} Write Up By Hades.AKM</h2> 

When you read description of this challenge you will figure out that the program is doing wrong because of signed interger. When I look and trace the program therse is a routine which print wire letter actually which should print the flag.

```assembly

.text:00007FF719601070                 mov     [rsp+arg_0], rbx
.text:00007FF719601075                 mov     [rsp+arg_8], rsi
.text:00007FF71960107A                 push    rdi
.text:00007FF71960107B                 sub     rsp, 710h
.text:00007FF719601082                 xor     ebx, ebx
.text:00007FF719601084                 lea     rcx, [rsp+718h+Dst] ; Dst
.text:00007FF719601089                 lea     rdx, aFuooltjvxzvohf ; "fuOoLtJVxzVohfuwStKPGeYjeaeENjBUtrNFCQS"...
.text:00007FF719601090                 mov     r8d, 6C4h       ; Size
.text:00007FF719601096                 call    memcpy
.text:00007FF71960109B                 movdqa  xmm0, cs:number_use_in_printing
.text:00007FF7196010A3                 lea     rdi, [rsp+718h+var_6F8]
.text:00007FF7196010A8                 movdqu  [rsp+718h+var_6F8], xmm0
.text:00007FF7196010AE                 mov     [rsp+718h+var_6E8], 2697h
.text:00007FF7196010B5                 mov     sil, 94h
.text:00007FF7196010B8                 mov     [rsp+718h+var_6E6], bl
.text:00007FF7196010BC                 nop     dword ptr [rax+00h]
.text:00007FF7196010C0
.text:00007FF7196010C0 loc_7FF7196010C0:                       ; CODE XREF: wired_printfunc+76↓j
.text:00007FF7196010C0                 movsxd  rax, ebx
.text:00007FF7196010C3                 lea     rcx, unk_7FF719602210
.text:00007FF7196010CA                 movsx   edx, [rsp+rax+718h+Dst]
.text:00007FF7196010CF                 call    vprintf_sys
.text:00007FF7196010D4                 movsx   eax, sil        ; This Instruction change The Value Of Eax To Negative
.text:00007FF7196010D8                 lea     rdi, [rdi+1]
.text:00007FF7196010DC                 movzx   esi, byte ptr [rdi]
.text:00007FF7196010DF                 inc     ebx
.text:00007FF7196010E1                 add     ebx, eax
.text:00007FF7196010E3                 test    sil, sil
.text:00007FF7196010E6                 jnz     short loc_7FF7196010C0
.text:00007FF7196010E8                 lea     r11, [rsp+718h+var_8]
.text:00007FF7196010F0                 xor     eax, eax
.text:00007FF7196010F2                 mov     rbx, [r11+10h]
.text:00007FF7196010F6                 mov     rsi, [r11+18h]
.text:00007FF7196010FA                 mov     rsp, r11
.text:00007FF7196010FD                 pop     rdi
```

In above code you will see that we load an address of huge string to rdx and copy to memory at address ```00007FF719601096``` . At ```loc_7FF7196010C0``` this routine generate the flag accroding to the value of rax. 


```assembly
.text:00007FF7196010D4                 movsx   eax, sil        ; This Instruction change The Value Of Eax To Negative
```

In some case at that instruction the value of eax will change to negative. So I set a break point on that instruction and check if the value of eax is negitive which is like ``` If eax = 0x80, After that instruction eax will change to 0xffffff80, and we need to change back to eax = 0x80``` and the program will print the flag correctly .


Flag format is like flag{****}.
