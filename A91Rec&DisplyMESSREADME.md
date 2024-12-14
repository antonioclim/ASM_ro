### **Explicație pas cu pas**

#### **1. ORG 100h**
- Setăm punctul de început al programului la adresa **0x100**, conform convenției pentru fișiere **.COM**.

#### **2. Inițializarea segmentului de date**
```asm
mov ax, @data
mov ds, ax
```
- **`@data`** este adresa segmentului de date. Copiem această adresă în registrul **DS** (Data Segment) pentru a accesa datele definite în secțiunea `data`.

---

#### **3. Afișarea mesajului**
```asm
mov ah, 09h    ; Funcția DOS pentru afișarea unui șir terminat cu '$'
lea dx, message ; Încarcă adresa de început a șirului în DX
int 21h        ; Apelează întreruperea DOS pentru afișare
```
- **`mov ah, 09h`**: Setăm funcția DOS pentru afișarea unui șir de caractere.
- **`lea dx, message`**: Încărcăm adresa de început a mesajului în registrul **DX**.
- **`int 21h`**: Apelează funcția DOS specificată (09h) pentru a afișa mesajul.

> **Notă**: Mesajul trebuie să fie terminat cu caracterul `$`, care semnalizează sfârșitul șirului.

---

#### **4. Terminarea programului**
```asm
mov ah, 4Ch
int 21h
```
- **`mov ah, 4Ch`**: Funcția DOS pentru terminarea programului.
- **`int 21h`**: Apelează întreruperea DOS pentru a încheia execuția.

---

#### **5. Segmentul de date**
```asm
data segment
message db 'Salut! Acesta este un mesaj.', '$' ; Șirul terminat cu '$'
data ends
```
- **`message`**: Este șirul de caractere care va fi afișat. Este terminat cu `$`, conform cerinței funcției DOS **09h**.

---

### **Cum funcționează programul**

1. Segmentul de date este inițializat, permițând accesarea mesajului definit în secțiunea `data`.
2. Mesajul `Salut! Acesta este un mesaj.` este afișat pe ecran, folosind funcția DOS **09h**.
3. Programul se încheie utilizând funcția DOS **4Ch**.

---

### **Rezultatul final**

După rularea programului, pe ecran se afișează:
```
Salut! Acesta este un mesaj.
```

---

### **Extensii posibile**

1. **Afișarea unui alt mesaj**:
   - Modificați șirul de caractere în secțiunea `data`. De exemplu:
     ```asm
     message db 'Bine ați venit la programarea în ASM!', '$'
     ```

2. **Afișarea mai multor mesaje**:
   - Adăugați mai multe șiruri în secțiunea `data` și folosiți mai multe apeluri `int 21h`.

3. **Citirea unui mesaj de la tastatură**:
   - Combinați cu funcția DOS **01h** pentru a citi un caracter sau **0Ah** pentru a citi un șir.
