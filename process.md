# Process
process is a running program, an abstraction of cpu

# Program
program is a object code (PL code pre-compile compile assemble and link by compiler)

# CPU resources
1. 32 regs (stack pointer included)
2. PC program counter
3. MODE user mode or supervisor mode, supervisor mode (1) can r/w control register(SATP STVEC SEPC ) , (2) can use PTE without PTE_U flag set(arbitrary address)
4. SATP supervisor address translation and protection register )page table)
5. STVEC handle traps address
6. SEPC saving pc during trap
7. SSCRATCH
