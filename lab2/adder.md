首先分析程式行為
```
root@kali:~/re-ctf-202109/basic/adder_ctf# ./adder 
Enter three numbers!
1
4
6
nope.
```
輸入三個數字，然後判斷  
執行radare2
```
root@kali:~/re-ctf-202109/basic/adder_ctf# r2 adder 
[0x00400860]> aa
[x] Analyze all flags starting with sym. and entry0 (aa)
[0x00400860]> afl
0x00400778    3 26           sym._init
0x004007b0    1 6            loc.imp.__gmon_start
0x004007c0    1 6            sym.imp.puts
0x004007d0    1 6            sym.imp.putchar
0x004007e0    1 6            sym.std::ios_base::Init::Init
0x004007f0    1 6            sym.imp.malloc
0x00400800    1 6            sym.imp.__libc_start_main
0x00400810    1 6            sym.imp.__cxa_atexit
0x00400820    1 6            sym.std::ios_base::Init::_Init
0x00400830    1 6            sym.std::basic_ostream_char_std::char_traits_char___std::operator___std::char_traits_char___std::basic_ostream_char_std::char_traits_char____charconst
0x00400840    1 6            sym.imp.free
0x00400850    1 6            sym.std::istream::operator___int
0x00400860    1 41           entry0
0x00400890    4 41           sym.deregister_tm_clones
0x004008c0    4 57           sym.register_tm_clones
0x00400900    3 28           sym.__do_global_dtors_aux
0x00400920    4 45   -> 42   entry.init0
0x0040094d    1 406          sym.gen_int
0x00400ae3    4 59           sym.print_ptr_char
0x00400b1e    4 208          main
0x00400bee    4 61           sym.__static_initialization_and_destruction_0_int_int
0x00400c2b    1 21           sym._GLOBAL__sub_I__Z3geni
0x00400c40    4 101          sym.__libc_csu_init
0x00400cb0    1 2            sym.__libc_csu_fini
0x00400cb4    1 9            sym._fini
[0x00400860]> s main
[0x00400b1e]> VV
```
```
| mov edx, dword [local_ch]                                                                                                                          
| mov eax, dword [local_10h]                                                                                                                         
| add edx, eax                                                                                                                                       
| mov eax, dword [local_14h]                                                                                                                         
| add eax, edx                                                                                                                                       
| mov edi, eax                                                                                                                                       
| call sym.gen_int;[gc]                                                                                                                              
| mov qword [local_8h], rax                                                                                                                          
| mov edx, dword [local_ch]                                                                                                                          
| mov eax, dword [local_10h]                                                                                                                         
| add edx, eax                                                                                                                                       
| mov eax, dword [local_14h]                                                                                                                         
| add eax, edx                                                                                                                                       
| ; 1337                                                                                                                                             
| cmp eax, 0x539                                                                                                                                     
| jne 0x400bcc;[gd]                                                                                                                                  
```
此jne如果false會跳到flag，而我們可以明顯看出，它是將三個數字相加後與1337(0x539)比較，因此輸入和為1337即可得到flag。

```
root@kali:~/re-ctf-202109/basic/adder_ctf# ./adder 
Enter three numbers!
1335
1
1
easyctf{y0u_added_thr33_nums!}
```
