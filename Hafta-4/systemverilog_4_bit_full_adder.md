// ====================================================================
// Proje: 4-Bit Tam Toplayıcı (Ripple Carry Adder)
// Dil: SystemVerilog
// ====================================================================

// 1-Bitlik Temel Tam Toplayıcı Modülü
module tam_toplayici_1bit (
    input  logic operand_a,
    input  logic operand_b,
    input  logic carry_in,
    output logic sum_out,
    output logic carry_out
);
    // Görseldeki mantık kapısı denklemleri
    assign sum_out   = operand_a ^ operand_b ^ carry_in;
    assign carry_out = (operand_a & operand_b) | (carry_in & (operand_a ^ operand_b));
endmodule

// 4-Bitlik Bütünleşik Tam Toplayıcı Modülü
module tam_toplayici_4bit (
    input  logic [3:0] vec_a,
    input  logic [3:0] vec_b,
    input  logic       cin,
    output logic [3:0] total_sum,
    output logic       cout
);
    // Ara elde (carry) bağlama hatları
    logic [2:0] internal_carry;

    // 1-Bitlik toplayıcı bloklarının zincirleme bağlantısı
    tam_toplayici_1bit cell_0 (
        .operand_a (vec_a[0]),
        .operand_b (vec_b[0]),
        .carry_in  (cin),
        .sum_out   (total_sum[0]),
        .carry_out (internal_carry[0])
    );

    tam_toplayici_1bit cell_1 (
        .operand_a (vec_a[1]),
        .operand_b (vec_b[1]),
        .carry_in  (internal_carry[0]),
        .sum_out   (total_sum[1]),
        .carry_out (internal_carry[1])
    );

    tam_toplayici_1bit cell_2 (
        .operand_a (vec_a[2]),
        .operand_b (vec_b[2]),
        .carry_in  (internal_carry[1]),
        .sum_out   (total_sum[2]),
        .carry_out (internal_carry[2])
    );

    tam_toplayici_1bit cell_3 (
        .operand_a (vec_a[3]),
        .operand_b (vec_b[3]),
        .carry_in  (internal_carry[2]),
        .sum_out   (total_sum[3]),
        .carry_out (cout)
    );

endmodule