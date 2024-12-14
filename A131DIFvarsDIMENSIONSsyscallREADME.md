### **Explicație pentru studenti: Cum funcționează acest cod în ASM**

Acest program este scris în limbaj de asamblare pentru platforma **x86** și rulează într-un emulator precum **EMU8086**. El afișează valorile a trei variabile (BYTE, WORD și DWORD) în format hexazecimal. Programul este organizat în mai multe părți: inițializarea segmentului de date, afișarea variabilelor și subrutine pentru conversie și afișare.

---

### **1. Noțiuni teoretice necesare**

- **BYTE, WORD, DWORD**:
  - **BYTE**: 8 biți (1 octet) — înregistrat în registrul **AL**.
  - **WORD**: 16 biți (2 octeți) — înregistrat în registrul **AX**.
  - **DWORD**: 32 biți (4 octeți) — reprezentat ca două **WORD**-uri consecutive.

- **Adresarea segmentelor**:
  - Segmentul de date (**DS**) este configurat astfel încât să acceseze variabilele din secțiunea `data segment`.

- **Afișarea în DOS**:
  - Funcția DOS **int 21h, ah=02h**: Afișează un singur caracter.
  - Funcția DOS **int 21h, ah=4Ch**: Încheie execuția programului.

- **Hexazecimal**:
  - Formatul hexazecimal folosește cifrele `0-9` și literele `A-F` pentru a reprezenta valori pe 4 biți (1 nibble).

---

### **2. Inițializarea programului**

#### **Linia `org 100h`**
- Specifică faptul că programul este un fișier **.COM** (program mic, monosegment). Execuția începe de la adresa **100h**.

#### **Segmentul de date**
```asm
data segment
byte_var db 25h          ; Variabilă de tip BYTE (25h = 37 zecimal)
word_var dw 0ABCDh       ; Variabilă de tip WORD (ABCD hex = 43981 zecimal)
dword_var dd 12345678h   ; Variabilă de tip DWORD (12345678 hex)
data ends
```
- Definim trei variabile:
  1. **`byte_var`**: O valoare pe 8 biți.
  2. **`word_var`**: O valoare pe 16 biți.
  3. **`dword_var`**: O valoare pe 32 de biți (stocată ca două **WORD**-uri consecutive).

---

### **3. Inițializarea segmentului de date**

```asm
mov ax, @data  ; Încarcă adresa segmentului de date în AX
mov ds, ax     ; Setează DS (Data Segment) pentru a accesa variabilele
```
- Aici configurăm segmentul **DS** pentru a putea accesa variabilele definite în `data segment`.

---

### **4. Afișarea variabilelor**

#### **Afișarea unei variabile BYTE**
```asm
mov al, byte_var    ; Încărcăm valoarea variabilei BYTE în AL
call print_hex_byte ; Apelăm subrutina pentru afișarea valorii în format hex
```
- Valoarea **BYTE** (8 biți) este încărcată în registrul **AL** și transmisă subrutinei `print_hex_byte`.

---

#### **Afișarea unei variabile WORD**
```asm
mov ax, word_var    ; Încărcăm valoarea variabilei WORD în AX
call print_hex_word ; Apelăm subrutina pentru afișarea valorii în format hex
```
- Valoarea **WORD** (16 biți) este încărcată în registrul **AX** și transmisă subrutinei `print_hex_word`.

---

#### **Afișarea unei variabile DWORD**
```asm
mov ax, word ptr dword_var ; Încărcăm partea inferioară a DWORD în AX
call print_hex_word        ; Afișăm partea inferioară
mov ax, word ptr dword_var+2 ; Încărcăm partea superioară a DWORD în AX
call print_hex_word        ; Afișăm partea superioară
```
- Valoarea **DWORD** (32 biți) este afișată în două părți:
  1. **Partea inferioară (primii 16 biți)** este încărcată în **AX**.
  2. **Partea superioară (următorii 16 biți)** este încărcată în **AX**.

---

#### **Afișarea unui newline**
```asm
call print_newline
```
- Apelăm subrutina `print_newline` pentru a afișa un newline (`CRLF`).

---

### **5. Terminarea programului**
```asm
mov ah, 4Ch         ; Funcția DOS pentru terminare
int 21h             ; Terminăm programul
```
- Programul se încheie folosind funcția DOS **4Ch**.

---

### **6. Subrutine**

#### **Subrutina `print_hex_byte`**
```asm
print_hex_byte:
    push ax         ; Salvăm valoarea originală
    mov ah, 0       ; Pregătim AH pentru conversie
    and al, 0Fh     ; Extragem nibble-ul inferior
    call nibble_to_ascii
    mov dl, al      ; Punem ASCII-ul în DL
    mov ah, 02h     ; Funcția DOS pentru afișare caracter
    int 21h         ; Afișăm nibble-ul inferior

    pop ax          ; Restaurăm valoarea originală
    shr al, 4       ; Deplasăm nibble-ul superior în partea inferioară
    call nibble_to_ascii
    mov dl, al      ; Punem ASCII-ul în DL
    mov ah, 02h     ; Funcția DOS pentru afișare caracter
    int 21h         ; Afișăm nibble-ul superior
    ret
```
- **Funcție**: Afișează un BYTE (8 biți) în format hex:
  - Extrage nibble-ul inferior și îl convertește în ASCII.
  - Afișează nibble-ul superior după ce îl deplasează.

---

#### **Subrutina `print_hex_word`**
```asm
print_hex_word:
    push ax         ; Salvăm valoarea originală

    ; Afișăm octetul superior
    mov al, ah      ; Mutăm octetul superior în AL
    call print_hex_byte

    ; Afișăm octetul inferior
    pop ax          ; Restaurăm valoarea originală
    call print_hex_byte
    ret
```
- **Funcție**: Afișează un WORD (16 biți) în format hex:
  - Împarte valoarea în două **BYTE**-uri (octet superior și inferior) și le afișează.

---

#### **Subrutina `nibble_to_ascii`**
```asm
nibble_to_ascii:
    add al, '0'     ; Convertim nibble-ul în ASCII
    cmp al, '9'     ; Dacă este mai mare decât 9...
    jbe nibble_done ; ...sărim peste conversia în litere
    add al, 7       ; Convertim în litere hex (A-F)
nibble_done:
    ret
```
- **Funcție**: Convertește un nibble (4 biți) într-un caracter ASCII.

---

#### **Subrutina `print_newline`**
```asm
print_newline:
    mov ah, 02h     ; Funcția DOS pentru afișare caracter
    mov dl, 0Dh     ; Carriage return
    int 21h
    mov dl, 0Ah     ; Line feed
    int 21h
    ret
```
- **Funcție**: Afișează un newline folosind caracterele `CR` și `LF`.

---

### **7. Rezultatul programului**

Pe ecran vor apărea valorile variabilelor, fiecare pe o linie nouă:

```
19
ABCD
5678
1234
```

---

### **Explicații suplimentare pentru începători**
1. **Cum funcționează `int 21h`**:
   - Este o întrerupere folosită pentru a interacționa cu DOS. Depinde de valoarea din **AH**:
     - **AH=02h**: Afișează un caracter.
     - **AH=4Ch**: Termină programul.

2. **De ce împărțim DWORD-ul**:
   - Un DWORD are 32 biți (4 octeți), dar registrele **AX** și **AL** pot gestiona doar 16 și respectiv 8 biți.

3. **De ce folosim `push` și `pop`**:
   - Pentru a salva valorile din registre care ar putea fi suprascrise în timpul executării subrutinei.
