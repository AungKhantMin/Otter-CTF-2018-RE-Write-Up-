<h1>Otter CTF 2018</h1>

<h2> Call Me Rick {Reverse Engineering} Write Up By Hades.AKM</h2> 


I am not going to tell in detail, except the important part of this crack me. This program modify the code it self with our input number. When we enter two interger value it change to hash and use it as **op code**. And Accroding to the Challenge name we can guess that we should ***call a function and exist properly***. 

```assembly
.text:0045D214                 call    ds:VirtualProtect
.text:0045D21A                 cmp     esi, esp
.text:0045D21C                 call    sub_4576EE
.text:0045D221                 mov     eax, [ebp+lpAddress]
.text:0045D224                 add     eax, 4
.text:0045D227                 push    eax
.text:0045D228                 mov     ecx, [ebp+lpAddress]
.text:0045D22B                 push    ecx
.text:0045D22C                 push    offset aDD      ; "%d %d"
.text:0045D231                 call    input_function
.text:0045D236                 add     esp, 0Ch
.text:0045D239
.text:0045D239 loc_45D239:                             ; DATA XREF: sub_45D1D0+2F↑o
.text:0045D239                 mov     [ebp+var_24], 6 ; 8 byte from this instructions will be replace by our input
.text:0045D240                 mov     ebx, 8C3h
.text:0045D245                 mov     eax, [ebp+var_24]
.text:0045D248                 add     eax, [ebp+var_24]
.text:0045D24B                 mov     [ebp+var_24], eax
.text:0045D24E                 mov     eax, [ebp+var_24]
```

When you look for ***String*** in IDA you will see there is a congratulations message use in a routine. So We must call the goodboy function and exit properly. 

```hex
0045D1F0  40 53 00 33 C5 89 45 FC  C7 45 F4 00 00 00 00 C7  @S.3Å‰EüÇEô....Ç
0045D200  45 E8 39 D2 45 00 8B F4  8D 45 F4 50 6A 40 6A 08  Eè9ÒE.‹ô.EôPj@j.
0045D210  8B 4D E8 51 FF 15 00 70  53 00 3B F4 E8 CD A4 FF  ‹MèQÿ..pS.;ôèÍ¤ÿ
0045D220  FF 8B 45 E8 83 C0 04 50  8B 4D E8 51 68 68 DE 50  ÿ‹EèƒÀ.P‹MèQhhÞP
0045D230  00 E8 1E C6 FF FF 83 C4  0C C7 45 DC 06 00 00 00  .è.ÆÿÿƒÄ.ÇEÜ....
0045D240  BB C3 08 00 00 8B 45 DC  03 45 DC 89 45 DC 8B 45  »Ã...‹EÜ.EÜ‰EÜ‹E
0045D250  DC 99 F7 7D DC 89 45 DC  33 C0 52 8B CD 50 8D 15  Ü™÷}Ü‰EÜ3ÀR‹ÍP..
0045D260  8C D2 45 00 E8 91 B6 FF  FF 58 5A 5F 5E 5B 8B 4D  ŒÒE.è‘¶ÿÿXZ_^[‹M
0045D270  FC 33 CD E8 90 B7 FF FF  81 C4 E8 00 00 00 3B EC  ü3Íè.·ÿÿ.Äè...;ì
0045D280  E8 69 A4 FF FF 8B E5 5D  C3 0F 1F 00 01 00 00 00  èi¤ÿÿ‹å]Ã.......
```


This is the original hex value of the program and the value from ```C7 45 DC ... BB``` will cahnge accroding to our input. I patch the program in IDA as below, so i can easily figure which is the right number.



```assembly
.text:0045D239                 call    good_boy  ;our input must change the program like this
.text:0045D23E                 nop
.text:0045D23F                 nop
.text:0045D240                 mov     ebx, 8C3h
.text:0045D245                 mov     eax, [ebp+var_24]
.text:0045D248                 add     eax, [ebp+var_24]
.text:0045D24B                 mov     [ebp+var_24], eax
```
After patching the program look at in the hex view and we known which number we should use. First number is ```0xFFFE72E8``` and second is  ```0xBB9090FF``` .

```hex
0045D1F0  40 53 00 33 C5 89 45 FC  C7 45 F4 00 00 00 00 C7  @S.3Å‰EüÇEô....Ç
0045D200  45 E8 39 D2 45 00 8B F4  8D 45 F4 50 6A 40 6A 08  Eè9ÒE.‹ô.EôPj@j.
0045D210  8B 4D E8 51 FF 15 00 70  53 00 3B F4 E8 CD A4 FF  ‹MèQÿ..pS.;ôèÍ¤ÿ
0045D220  FF 8B 45 E8 83 C0 04 50  8B 4D E8 51 68 68 DE 50  ÿ‹EèƒÀ.P‹MèQhhÞP
0045D230  00 E8 1E C6 FF FF 83 C4  0C E8 72 FE FF FF 90 90  .è.ÆÿÿƒÄ.èrþÿÿ..
0045D240  BB C3 08 00 00 8B 45 DC  03 45 DC 89 45 DC 8B 45  »Ã...‹EÜ.EÜ‰EÜ‹E
0045D250  DC 99 F7 7D DC 89 45 DC  33 C0 52 8B CD 50 8D 15  Ü™÷}Ü‰EÜ3ÀR‹ÍP..
0045D260  8C D2 45 00 E8 91 B6 FF  FF 58 5A 5F 5E 5B 8B 4D  ŒÒE.è‘¶ÿÿXZ_^[‹M
0045D270  FC 33 CD E8 90 B7 FF FF  81 C4 E8 00 00 00 3B EC  ü3Íè.·ÿÿ.Äè...;ì
0045D280  E8 69 A4 FF FF 8B E5 5D  C3 0F 1F 00 01 00 00 00  èi¤ÿÿ‹å]Ã.......
```

We can only change ***8 Bytes and Only 4Bytes integers are use as op code.*** 


The Flag Is ***CTF{Sum of two four bytes integer in upper case hex}*** . 
