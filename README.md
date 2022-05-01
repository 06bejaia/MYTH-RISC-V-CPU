# MYTH-RISC-V-CPU

Intoduction to RISC-V CPU architecture:

![Example_RISCV__Block_Diagram](https://user-images.githubusercontent.com/67355283/166131604-b10fe61a-f482-45f0-8288-068861b0b56f.jpg)


RISC-V is an ISA, so it doesn't talk about how to implement the actual design. CPU designers take the ISA as their input and decide how to implement hardware (almost always) in RTL. In this day a single cycle design of the ISA will be implemented named MYTH. MYTH will become pipeline in the next day. Here is a schematic of the design


Fetch

The basic operation of a computer is called the ‘fetch-execute’ cycle. The CPU is designed to understand a set of instructions - the instruction set. It fetches the instructions from the main memory and executes them. This is done repeatedly from when the computer is booted up to when it is shut down.


![fetch](https://user-images.githubusercontent.com/67355283/166131725-ea1524fd-28b7-4d83-a38f-ef69cb9996dd.PNG)


Decode

Decode stage The decoding process allows the CPU to determine what instruction is to be performed so that the CPU can tell how many operands it needs to fetch in order to perform the instruction. The opcode fetched from the memory is decoded for the next steps and moved to the appropriate registers.

![decode](https://user-images.githubusercontent.com/67355283/166131635-c32666c5-0ad7-4347-88d2-3e89ef99e4f9.PNG)

Register File

A register file is a means of memory storage within a computer's central processing unit (CPU). The computer's register files contain bits of data and mapping locations. These locations specify certain addresses that are input components of a register file. Other inputs include data, a read and write function and execute function.


![file-register](https://user-images.githubusercontent.com/67355283/166131758-b137a9c6-7265-47f6-9937-9d411e3b3433.PNG)



Excute

The control unit of the CPU passes the decoded information as a sequence of control signals to the relevant functional units of the CPU to perform the actions required by the instruction, such as reading values from registers, passing them to the ALU to perform mathematical or logic functions on them


![ALU](https://user-images.githubusercontent.com/67355283/166132075-4caae9d3-b94b-4366-97eb-9ca7bc465170.PNG)



Write register File

A register file is an array of processor registers in a central processing unit (CPU). Register banking is the method of using a single name to access multiple different physical registers depending on the operating mode. Modern integrated circuit-based register files are usually implemented by way of fast static RAMs with multiple ports. Such RAMs are distinguished by having dedicated read and write ports, whereas ordinary multiported SRAMs will usually read and write through the same ports.

![5](https://user-images.githubusercontent.com/67355283/165024398-b1ee0117-b87a-42f2-85e5-89c93bcb8703.PNG)

Wrap up code fpr MYTH pipelined core



   // This code can be found in: https://github.com/stevehoover/RISC-V_MYTH_Workshop
  // executed by Nacer Chalane


   // External to function:
   m4_asm(ADD, r10, r0, r0)             // Initialize r10 (a0) to 0.
   // Function: 
   m4_asm(ADD, r14, r10, r0)            // Initialize sum register a4 with 0x0
   m4_asm(ADDI, r12, r10, 1010)         // Store count of 10 in register a2.
   m4_asm(ADD, r13, r10, r0)            // Initialize intermediate sum register a3 with 0
   // Loop:
   m4_asm(ADD, r14, r13, r14)           // Incremental addition
   m4_asm(ADDI, r13, r13, 1)            // Increment intermediate register by 1
   m4_asm(BLT, r13, r12, 1111111111000) // If a3 is less than a2, branch to label named <loop>
   m4_asm(ADD, r10, r14, r0)            // Store final result to register a0 so that it can be read by main program

   m4_asm(SW, r0, r10, 100)
   m4_asm(LW, r15, r0, 100)

   // Optional:
   m4_asm(JAL, r7, 00000000000000000000) // Done. Jump to itself (infinite loop). (Up to 20-bit signed immediate plus implicit 0 bit (unlike JALR) provides byte address; last immediate bit should also be 0)
   m4_define_hier(['M4_IMEM'], M4_NUM_INSTRS)

   |cpu
      @0
         $reset = *reset;



      @0
         $imem_rd_en = !$reset;
         $imem_rd_addr[M4_IMEM_INDEX_CNT+1:0] = $pc[M4_IMEM_INDEX_CNT+1:2];

         $pc[31:0] =
            >>1$reset                     ? 32'd0           :
            >>3$valid_taken_br            ? >>3$br_tgt_pc   :
            >>3$valid_load                ? >>3$inc_pc      :
            >>3$valid_jump && >>3$is_jal  ? >>3$br_tgt_pc   :
            >>3$valid_jump && >>3$is_jalr ? >>3$jalr_tgt_pc : >>1$inc_pc;

      @1
         $inc_pc[31:0] = $pc + 32'd4;

         $instr[31:0] = $imem_rd_data;

         $is_i_instr =
            $instr[6:2] == 5'b00000 ||
            $instr[6:2] == 5'b00001 ||
            $instr[6:2] == 5'b00100 ||
            $instr[6:2] == 5'b00110 ||
            $instr[6:2] == 5'b11001 ||
            $instr[6:2] == 5'b11100;
         $is_r_instr =
            $instr[6:2] == 5'b01011 ||
            $instr[6:2] == 5'b01100 ||
            $instr[6:2] == 5'b01110 ||
            $instr[6:2] == 5'b10100;
         $is_s_instr =
            $instr[6:2] == 5'b01000 ||
            $instr[6:2] == 5'b01001;
         $is_b_instr =
            $instr[6:2] == 5'b11000;
         $is_j_instr =
            $instr[6:2] == 5'b11011;
         $is_u_instr =
            $instr[6:2] == 5'b00101 ||
            $instr[6:2] == 5'b01101;

         $rd_valid     = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
         $rs1_valid    = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         $rs2_valid    = $is_r_instr || $is_s_instr || $is_b_instr;
         $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         $funct7_valid = $is_r_instr;

         $opcode[6:0] = $instr[6:0];
         $imm[31:0] =
            $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
            $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
            $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25], $instr[11:8], {1'd0}} :
            $is_u_instr ? {$instr[31:12], {12'd0}} :
            $is_j_instr ? {{12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], {1'd0}} : 32'd0;
         ?$rd_valid
            $rd[5:0]     = $instr[11:7];
         ?$rs1_valid
            $rs1[4:0]    = $instr[19:15];
         ?$rs2_valid
            $rs2[4:0]    = $instr[24:20];
         ?$funct3_valid
            $funct3[2:0] = $instr[14:12];
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25];

         $dec_bits[10:0] = {$funct7[5], $funct3, $opcode};
         //I Types:
         $is_addi  = $dec_bits ==? 11'bx_000_0010011;
         $is_slli  = $dec_bits ==? 11'bx_001_0010011;
         $is_slti  = $dec_bits ==? 11'bx_010_0010011;
         $is_sltiu = $dec_bits ==? 11'bx_011_0010011;
         $is_xori  = $dec_bits ==? 11'bx_100_0010011;
         $is_srli  = $dec_bits ==? 11'b0_101_0010011;
         $is_srai  = $dec_bits ==? 11'b1_101_0010011;
         $is_ori   = $dec_bits ==? 11'bx_110_0010011;
         $is_andi  = $dec_bits ==? 11'bx_111_0010011;
         //R Types:
         $is_add   = $dec_bits ==? 11'b0_000_0110011;
         $is_sub   = $dec_bits ==? 11'b1_000_0110011;
         $is_sll   = $dec_bits ==? 11'bx_001_0110011;
         $is_slt   = $dec_bits ==? 11'bx_010_0110011;
         $is_sltu  = $dec_bits ==? 11'bx_011_0110011;
         $is_xor   = $dec_bits ==? 11'bx_100_0110011;
         $is_srl   = $dec_bits ==? 11'b0_101_0110011;
         $is_sra   = $dec_bits ==? 11'b1_101_0110011;
         $is_or    = $dec_bits ==? 11'bx_110_0110011;
         $is_and   = $dec_bits ==? 11'bx_111_0110011;
         //B Types:
         $is_beq   = $dec_bits ==? 11'bx_000_1100011;
         $is_bne   = $dec_bits ==? 11'bx_001_1100011;
         $is_blt   = $dec_bits ==? 11'bx_100_1100011;
         $is_bge   = $dec_bits ==? 11'bx_101_1100011;
         $is_bltu  = $dec_bits ==? 11'bx_110_1100011;
         $is_bgeu  = $dec_bits ==? 11'bx_111_1100011;
         //J Types:
         $is_jal   = $dec_bits ==? 11'bx_xxx_1101111;
         $is_jalr  = $dec_bits ==? 11'bx_xxx_1100111;
         //Other Types:
         $is_store = $dec_bits ==? 11'bx_xxx_0100011;
         $is_load  = $dec_bits ==? 11'bx_xxx_0000011;
         $is_lui   = $dec_bits ==? 11'bx_xxx_0110111;
         $is_auipc = $dec_bits ==? 11'bx_xxx_0010111;

      @2
         $br_tgt_pc[31:0]   = $pc + $imm;
         $jalr_tgt_pc[31:0] = $src1_value + $imm;

         $rf_rd_en1         = $rs1_valid;
         $rf_rd_index1[4:0] = $rs1;
         $src1_value[31:0]  =
            >>1$rf_wr_index == $rf_rd_index1 && >>1$rf_wr_en ? >>1$result : $rf_rd_data1;
         $rf_rd_en2         = $rs2_valid;
         $rf_rd_index2[4:0] = $rs2;
         $src2_value[31:0]  =
            >>1$rf_wr_index == $rf_rd_index2 && >>1$rf_wr_en ? >>1$result : $rf_rd_data2;

      @3
         $taken_br =
            ($is_beq  && ($src1_value == $src2_value)                                       ) ||
            ($is_bne  && ($src1_value != $src2_value)                                       ) ||
            ($is_blt  && ($src1_value <  $src2_value) ^ ($src1_value[31] != $src2_value[31])) ||
            ($is_bge  && ($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])) ||
            ($is_bltu && ($src1_value <  $src2_value)                                       ) ||
            ($is_bgeu && ($src1_value >= $src2_value)                                       );
         $valid_taken_br = $valid && $taken_br;

         $valid_load = $valid && $is_load;

         $is_jump = $is_jal || $is_jalr;

         $valid_jump = $valid && $is_jump;

         $valid = !(
            >>1$valid_taken_br || >>2$valid_taken_br ||
            >>1$valid_load     || >>2$valid_load     ||
            >>1$valid_jump     || >>2$valid_jump);

      @3
         $rf_wr_en          = ($rd_valid && $rd != 5'd0 && $valid) || >>2$valid_load;
         $rf_wr_index[4:0]  = >>2$valid_load ? >>2$rd : $rd;
         $rf_wr_data[31:0]  = >>2$valid_load ? >>2$ld_data : $result;

         $sltu_rslt[31:0]  = $src1_value < $src2_value;
         $sltiu_rslt[31:0] = $src1_value < $imm;
         $result[31:0]     =
            ($is_addi || $is_load || $is_store) ? $src1_value + $imm :
            $is_slli  ? $src1_value << $imm[5:0]        :
            $is_slti  ? ($src1_value[31] == $src2_value[31]) ? $sltu_rslt : {31'd0, $src1_value[31]} :
            $is_sltiu ? $src1_value < $imm              :
            $is_xori  ? $src1_value ^ $imm              :
            $is_srli  ? $src1_value >> $imm[5:0]        :
            $is_srai  ? {{32{$src1_value[31]}}, $src1_value} >> $imm[4:0] :
            $is_ori   ? $src1_value | $imm              :
            $is_andi  ? $src1_value & $imm              :
            $is_add   ? $src1_value + $src2_value       :
            $is_sub   ? $src1_value - $src2_value       :
            $is_sll   ? $src1_value << $src2_value[4:0] :
            $is_slt   ? ($src1_value[31] == $imm[31]) ? $sltiu_rslt : {31'd0, $src1_value[31]} :
            $is_sltu  ? $src1_value < $src2_value       :
            $is_xor   ? $src1_value ^ $src2_value       :
            $is_srl   ? $src1_value >> $src2_value[4:0] :
            $is_sra   ? {{32{$src1_value[31]}}, $src1_value} >> $src2_value[4:0] :
            $is_or    ? $src1_value | $src2_value       :
            $is_and   ? $src1_value & $src2_value       :
            $is_jal   ? $pc + 32'd4                     :
            $is_jalr  ? $pc + 32'd4                     :
            $is_lui   ? {$imm[31:12], 12'd0}            :
            $is_auipc ? $pc + $imm                      : 32'dx;

      @4
         $dmem_wr_en         = $is_store;
         $dmem_addr[3:0]     = $result[5:2];
         $dmem_wr_data[31:0] = $src2_value;
         $dmem_rd_en         = $is_load;

      @5
         $ld_data[31:0] = $dmem_rd_data[31:0];



  


