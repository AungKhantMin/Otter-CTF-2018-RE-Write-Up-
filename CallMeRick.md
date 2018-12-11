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

```assembly
.text:0045D239                 call    good_boy  ;our input must change the program like this
.text:0045D23E                 nop
.text:0045D23F                 nop
.text:0045D240                 mov     ebx, 8C3h
.text:0045D245                 mov     eax, [ebp+var_24]
.text:0045D248                 add     eax, [ebp+var_24]
.text:0045D24B                 mov     [ebp+var_24], eax
```

We can only change ***8 Bytes and Only 4Bytes integers are use as op code.*** The Flag Is Like ***CTF{Sum of two four bytes integer in upper case hex}*** . If you are familiar with IDA you will know how to find the input numbers.
