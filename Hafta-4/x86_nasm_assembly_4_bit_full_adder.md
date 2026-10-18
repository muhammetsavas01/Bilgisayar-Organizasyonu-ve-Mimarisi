; ====================================================================
; Proje: 4-Bit Tam Toplayıcı (x86_64 NASM Assembly)
;
; Girdiler:
;   AL = A Sayısı (0-3. bitler)
;   BL = B Sayısı (0-3. bitler)
;   CL = Giriş Eldesi / Cin (0 veya 1)
;
; Çıktılar:
;   AL = 4-Bit Toplam (0-3. bitler)
;   AH = Çıkış Eldesi / Cout (0 veya 1)
; ====================================================================

section .text
global toplayici_4bit_isle

toplayici_4bit_isle:
    ; Kullanılan yazmaçları korumaya al
    push rbx
    push rcx
    push rdx
    push rsi
    push rdi

    ; Girdilerin sadece ilk 4/1 bitini alacak şekilde maskeleme
    and al, 0x0F        ; A girdisi
    and bl, 0x0F        ; B girdisi
    and cl, 0x01        ; Cin girdisi

    mov dil, al         ; DIL = A
    mov sil, bl         ; SIL = B
    mov dl, cl          ; DL  = Aktif Elde (Cin)

    xor r8b, r8b        ; R8B = Birikimli Toplam (4-bit)
    xor rcx, rcx        ; Döngü indeksi (0, 1, 2, 3)

bit_isleme_dondur:
    cmp rcx, 4
    je  islem_tamam

    ; A'nın i. bitini ayrıştır
    mov al, dil
    shr al, cl
    and al, 1           ; AL = a_i

    ; B'nin i. bitini ayrıştır
    mov bl, sil
    shr bl, cl
    and bl, 1           ; BL = b_i

    ; -------------------------------------------------------------
    ; Mantık Kapıları İle Toplama Hesaplaması
    ; sum_i  = a_i ^ b_i ^ cin
    ; cout_i = (a_i & b_i) | (cin & (a_i ^ b_i))
    ; -------------------------------------------------------------
    mov r9b, al
    xor r9b, bl         ; R9B = a_i ^ b_i

    mov r10b, r9b
    xor r10b, dl        ; R10B = sum_i

    mov r11b, al
    and r11b, bl        ; R11B = a_i & b_i

    and r9b, dl         ; R9B = cin & (a_i ^ b_i)
    or  r11b, r9b       ; R11B = yeni cout_i

    ; Bulunan sum_i bitini ilgili basamağa kaydırıp sonuca ekle
    shl r10b, cl
    or  r8b, r10b

    ; Sonraki bit için elde değerini güncelle
    mov dl, r11b

    inc rcx
    jmp bit_isleme_dondur

islem_tamam:
    mov al, r8b         ; AL = Toplam sonucu
    mov ah, dl          ; AH = Elde çıkışı (Cout)

    ; Yazmaçları eski haline getir
    pop rdi
    pop rsi
    pop rdx
    pop rcx
    pop rbx
    ret