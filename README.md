# 16bit-cpu-vhdl
16-Bit single-bus custom CPU architecture in Logisim featuring a 256x16 RAM, 6 registers, 8-to-1 common bus, 7-op ALU, and hardwired RTL control unit executing custom assembly.
# 16-Bit Custom CPU Architecture & RTL Control Unit (Logisim)

A complete 16-bit single-bus processor architecture designed and simulated at the gate and register-transfer level (RTL) in **Logisim**. The processor features a 256x16-bit Random Access Memory (RAM), 6 architectural registers connected across an 8-to-1 common data bus, a 7-operation Arithmetic Logic Unit (ALU), and a hardwired combinational Control Unit executing a custom 7-instruction assembly ISA (`LDA`, `STA`, `ISZ`, `BUN`, `ADD`, `DIV`, `XOR`).

## Instruction Set Architecture (ISA) & RTL Cycles

### Fetch & Decode Cycle
- `T0`: `AR <- PC`
- `T1`: `IR <- M[AR], PC <- PC + 1`
- `T2`: `AR <- IR[11:4]`

### Execution Cycles (`D0..D6`)
- **`LDA` (`D0`):** `T3: DR <- M[AR]` | `T4: AC <- DR, SC <- 0`
- **`STA` (`D1`):** `T3: M[AR] <- AC, SC <- 0`
- **`ISZ` (`D2`):** `T3: DR <- M[AR]` | `T4: DR <- DR + 1` | `T5: M[AR] <- DR, if (DR = 0) then PC <- PC + 1, SC <- 0`
- **`BUN` (`D3`):** `T3: PC <- AR, SC <- 0`
- **`ADD` (`D4`):** `T3: DR <- M[AR]` | `T4: AC <- AC + DR, SC <- 0`
- **`DIV` (`D5`):** `T3: DR <- M[AR]` | `T4: AC <- AC / DR, SC <- 0`
- **`XOR` (`D6`):** `T3: DR <- M[AR]` | `T4: AC <- AC XOR DR, SC <- 0`

## Hardwired Control Unit Boolean Equations

- **Register Load Signals:**
  - `LD_AR = T0 + T2`
  - `LD_IR = T1`
  - `LD_DR = T3 * (D0 + D2 + D4 + D5 + D6)`
  - `LD_AC = T4 * (D0 + D4 + D5 + D6)`
  - `LD_PC = T3 * D3`
  - `LD_TR = 0`
- **Increment & Clear Signals:**
  - `INC_PC = DR_Zero * D2 * T6 + T1`
  - `INC_DR = D2 * T4`
  - `CLR_SC = D2 * T5 + (D0 + D4 + D5 + D6) * T4 + (D1 + D3) * T3`
- **Memory, ALU (`C2..C0`), & Common Bus (`S2..S0`) Select Lines:**
  - `READ = (D0 + D2 + D4 + D5 + D6) * T3 + T1`
  - `WRITE = D1 * T3 + D2 * T5`
  - `C2 = (D5 + D6) * T4`, `C1 = (D4 + D6) * T4`, `C0 = (D0 + D5 + D6) * T4`
  - `S2 = D1 * T3 + D2 * T5 + T2`
  - `S1 = D3 * T3 + T0 + T2`
  - `S0 = (D0 + D1 + D2 + D4 + D5 + D6) * T3 + T0 + T1`
