.global main

// Entrées: Deux entiers signés a et b
//Sorties:
main:
    // Lire a                       //
	adr   x0, fmtEntree             //
	adr   x1, nombre                //
	bl    scanf                     // scanf(&fmtEntree, &nombre)
	adr   x23, nombre
	ldrsh w19,[x23]                  // a = nombre

	// Lire b
	adr   x0, fmtEntree             //
	adr   x1, nombre                //
	bl    scanf                     // scanf(»&fmtEntree, &nombre)
	adr   x23,nombre
	ldrsh w21,[x23]                  // b = nombre1

    add   w22,w19,w21               //add = a+b sur 32 bits

//Affichage addition add (a+b)
	adr   x0, fmtSortie
	mov   w1,w22
	bl    printf

//Vérification débordement positif
//2^15 = 32768 => Le plus grand nombre sur 16 bits
	mov   w23,32767
	cmp   w22,w23
	b.gt  deb                       //Si add > 32768 => débordement

//Vérification débordement négatif
//-2^15 = -32768 => Le plus petit nombre sur 16 bits
	mov   w23,-32768
	cmp   w22,w23
    b.lt  deb                       //Si add < -32768 => débordement

//Affichage sans débordement
	adr   x0, msgSansDebordement
	bl    printf
	b     mult

//Affichage en cas de débordement
deb:
	adr   x0, msgDebordement
	bl    printf

//Début multiplication
mult:
	mov   x28,2                  //Constante = 2
	mov   x23,0                  //Accumulateur
	mov   x24,32                 //Compteur nombre bits traités

boucle:
	cbz   x24,fin                //Si compteur = 0 sortir de la boucle
	tbz   x21,0,suiteMult        //Si dernier bit de b == 0 aller à suiteMult
	add   x23,x23,x19            //Acc+=a si dernier bit de b == 1
suiteMult:
	add   x19,x19,x19            //a*=2
	sdiv  x21,x21,x28            //b/=2
	sub   x24,x24,1              //Compteur--
	b     boucle

//Affichage résultat multiplication
fin:
	adr   x0, fmtSortie
	mov   x1,x23
	bl    printf
//Quitter
    mov     x0, 0
    bl      exit
.section ".bss"
	        .align  2
nombre:     .skip   2

.section ".rodata"
fmtEntree:          .asciz  "%hd"
fmtSortie:          .asciz  "%d\n"
msgDebordement:     .asciz  "débordement\n"
msgSansDebordement: .asciz  "sans débordement\n"
