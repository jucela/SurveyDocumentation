# FLUJO - CAP500 2026

Total: 84

| Año | Pregunta / Opción / Cuadro | Condición | Acción | Destino | Instruccion |
| --- | --- | --- | --- | --- | --- |
| 2026 | P500-501 | código = 1 | Pase a | P500-502 | — |
| 2026 | P500-501 | código = 2 | Continúa con | P500-501A | — |
| 2026 | P500-501A | código = 1 \|\| 2 | Pase a | P500-505 | — |
| 2026 | P500-502 | código = 1 | Pase a | P500-504A1 | — |
| 2026 | P500-502 | código = 2 | Continúa con | P500-503 | — |
| 2026 | P500-503 | código = 1 | Pase a | P500-504A1 | — |
| 2026 | P500-503 | código = 2 | Pase a | P500-504 | — |
| 2026 | A | P500-504-1 = 2 && P500-504-2 = 2 && P500-504-3 = 2 && P500-504-4 = 2 && P500-504-5 = 2 && P500-504-6 | Pase a | P500-545A | Se ejecuta despues de responder todas las subpreguntas de P500-504 |
| 2026 | A |  P500-504-1 = 1 \|\| P500-504-2 = 1 \|\| P500-504-3 = 1 \|\| P500-504-4 = 1 \|\| P500-504-5 = 1 \|\| | Continúa con | P500-505 | Se ejecuta despues de responder todas las subpreguntas de P500-504 |
| 2026 | P500-504A1 | código = 1 \|\| 2 \|\| 3 \|\| 4 | Pase a | P500-505 | — |
| 2026 | P500-504A1 | código = 5 \|\| 6 \|\| 7 \|\| 8 \|\| 9 \|\| 10 \|\| 11 \|\| 12 | Continúa con | P500-504B1 | — |
| 2026 | P500-504B1 | código = 1 | Pase a | P500-505 | — |
| 2026 | P500-504B1 | código = 2 | Continúa con | P500-504C | — |
| 2026 | P500-507 | código = 1 \|\| 2 | Continúa con | P500-508 | — |
| 2026 | P500-507 | código = 3 \|\| 4 \|\| 10 \|\| 11 | Pase a | P500-510 | — |
| 2026 | P500-507 | código = 8 \|\| 9 \|\| 12 \|\| 13 | Pase a | P500-510A1 | — |
| 2026 | P500-507 | código = 6 | Pase a | P500-511 | — |
| 2026 | A2 | P506A = 1 \|\| 2 | Pase a | P500-509A | Se ejecuta despues de responder P500-509 |
| 2026 | A2 | P506A = 3 | Pase a | P500-510A1 | Se ejecuta despues de responder P500-509 |
| 2026 | P509A | código = 1 \|\| 2 \|\| 3 \|\| 4 | Pase a | P500-510A1 | — |
| 2026 | P500-510 | código = 1 | Pase a | P500-513 | — |
| 2026 | P500-510 | código = 2 \|\| 3 | Pase a | P500-511 | — |
| 2026 | P500-510 | código = 5 \|\| 6 \|\| 7  | Continúa con | P500-510A1 | — |
| 2026 | P500-511 | código = 1 \|\| 2 \|\| 3 \|\| 4 \|\| 5 \|\| 6 \|\| 7 \|\| 8 \|\| 10 \|\| 11 \|\| 12 | Continúa con | P500-511A1 | — |
| 2026 | P500-511 | código = 7 \|\| 8 | Pase a | P500-512 | — |
| 2026 | P500-511A | código = 1  | Pase a | P500-512 | — |
| 2026 | P500-511A | código = 2 \|\| 4 \|\| 5 \|\| 6 \|\| 7 \|\| 8 | Pase a | P500-511B | — |
| 2026 | P500-514 | código = 1  | Pase a | P500-516 | — |
| 2026 | P500-514 | código = 2 | Continúa con | P500-515 | — |
| 2026 | C |  P500-515-1 = 1 \|\| P500-515-2 = 1 \|\| P500-515-3 = 1 \|\| P500-515-4 = 1 \|\| P500-515-5 = 1 \|\| | Pase a | P500-516 | Se ejecuta despues de responder todas las subpreguntas de P500-515 |
| 2026 | C | P500-515-1 = 2 && P500-515-2 = 2 && P500-515-3 = 2 && P500-515-4 = 2 && P500-515-5 = 2 && P500-515-6 | Pase a | D | Se ejecuta despues de responder todas las subpreguntas de P500-515.  el cuadro D indica que debemos  |
| 2026 | P500-517 | código = 1 \|\| 2 | Pase a | P500-517B1 | — |
| 2026 | P500-517 | código = 3 \|\| 4 \|\| 10 \|\| 11 | Continúa con | P500-517A | — |
| 2026 | P500-517 | código = 8 \|\| 9 \|\| 12 \|\| 13 | Pase a | P500-517B1 | — |
| 2026 | P500-517 | código = 6 | Pase a | P500-517D | — |
| 2026 | P500-517A | código = 1 | Pase a | P500-518 | — |
| 2026 | P500-517A | código = 2 \|\| 3 | Pase a | P500-517D | — |
| 2026 | P500-517A | código = 5 \|\| 6 \|\| 7 | Continúa con | P500-517B1 | — |
| 2026 | P500-519 | código = 1  | Pase a | P500-521 | — |
| 2026 | P500-519 | código = 2 | Continúa con | P500-520 | — |
| 2026 | P500-521 | código = 1 | Continúa con | P500-521A | — |
| 2026 | P500-521 | código = 2 | Pase a | D1 | El cuadro D1 se ubica despues de P500-521A |
| 2026 | D1 | P500-507 = 1 \|\| 2 \|\| 3 \|\| 4 \|\| 6 \|\|10 \|\| 11 | Continúa con | P500-521C | — |
| 2026 | D1 | P500-507 = 8 \|\| 9 \|\| 12 \|\| 13 | Pase a | G | El cuadro G se ubica despues de P500-544 |
| 2026 | P500-521C | código = 1  | Continúa con | P500-521D | — |
| 2026 | P500-521C | código = 2 | Pase a | E | El cuadro E se ubica despues de P500-521D |
| 2026 | E | P500-507 = 1 \|\| 2 | Pase a | P500-530 | — |
| 2026 | E | P500-507 = 3 \|\| 4 \|\| 6 \|\| 11    &&  P500-511 = 10 | Pase a | P500-528 | — |
| 2026 | E | P500-507 = 3 \|\| 4 \|\| 6 \|\| 11    && P500-511 = 12 | Pase a | P500-556 | — |
| 2026 | E | P500-507 = 3 \|\| 4 \|\| 6 \|\| 11    && P500-511 = 10 && (1 \|\| 2 \|\| 3 \|\|4 \|\| 5 \|\| 6 \|\|7 | Pase a | P500-523 | — |
| 2026 | P500-528 | código = 1  | Continúa con | P500-529 | — |
| 2026 | P500-528 | código = 2 | Pase a | F | El cuadro F se ubica despues de P500-536 |
|  | P500-529 | Se Respondio al menos una subpregunta | Pase a | F | El cuadro F se ubica despues de P500-536 |
| 2026 | P500-535 | código = 1  | Continúa con | P500-536 | — |
| 2026 | P500-535 | código = 2 \|\| 3 | Pase a | F | El cuadro F se ubica despues de P500-536 |
| 2026 | F | P500-517 = 1 \|\| 2 \|\| 3 \|\| 4 \|\| 6 \|\|8 \|\| 9 \|\|10 \|\| 11 \|\|12 \|\| 13 | Continúa con | P500-537 | — |
| 2026 | F | P500-517 = Empty  \|\| Null  | Pase a | P500-544 | — |
| 2026 | P500-537 | código = 1 \|\| 2 \|\| 3 \|\| 4 \|\| 5 \|\| 11 | Pase a | P500-538 | — |
| 2026 | P500-537 | código = 7 \|\| 8  | Pase a | P500-541 | — |
| 2026 | P500-537 | código =  9 \|\| 12 | Pase a | P500-544 | — |
| 2026 | P500-537 | código = 6  && P500-517 = 3 \|\| 4 | Continúa con | P500-538 | — |
| 2026 | P500-537 | código = 6  && P500-517 = 1 \|\| 2 | Pase a | P500-541 | — |
| 2026 | P500-537 | código = 10 && P500-517 = 3 \|\| 4 \|\| 6 | Pase a | P500-539 | — |
| 2026 | P500-537 | código = 10 && P500-517 = 1 \|\| 2 | Pase a | P500-541 | — |
| 2026 | P500-539 | código = 1  | Continúa con | P500-540 | — |
| 2026 | P500-539 | código = 2 | Pase a | P500-541 | — |
| 2026 | P500-542 | código = 1 | Continúa con | P500-543 | — |
| 2026 | P500-542 | código = 2 \|\| 3 | Pase a |  G | El cuadro G se ubica despues de P500-544 |
| 2026 | G | P500-507 = 1 \|\| 2 \|\| 3 \|\| 4 \|\| 6 \|\|10 \|\| 11 | Pase a | P500-556 | — |
| 2026 | G | P500-507 = 8 \|\| 9 \|\| 12 \|\| 13 | Continúa con | G1 | El cuadro G1 despues de G |
| 2026 | G1 | P500-504B1=2 && P500-504C=2\|\|3 | Continúa con | P500-545A | — |
| 2026 | G1 | (P500-504B1=2 && P500-504C=2\|\|3) <>true | pase a | P500-552 | — |
| 2026 | P500-545A | código = 1 | Pase a | P500-545D | — |
| 2026 | P500-545A | código = 2 | Continúa con | P500-545B | — |
| 2026 | P500-545B | código = 1 \|\| 2 \|\| 3  | Pase a | P500-552 | — |
| 2026 | P500-545B | código = 4 \|\| 5 \|\| 6 \|\| 7 \|\| 8 \|\| 9\|\| 10 \|\|11 \|\| 12\|\|13 | Continúa con | P500-545 | — |
| 2026 | P500-549 | código = 1 \|\| 2 \|\| 3 \|\| 4 \|\| 5 \|\| 6 \|\| 7 \|\| 8 \|\| 9\|\| 12 | Pase a | P500-552 | — |
| 2026 | P500-549 | código = 10 \|\|11  | Pase a | P500-551 | — |
| 2026 | P500-550 | código = 1 \|\| 2 \|\| 3 \|\| 4 \|\| 5 \|\| 6  | Continúa con | P500-551 | — |
| 2026 | P500-550 | código = 7 | Pase a | P500-552 | — |
| 2026 | P500-552 | código = 1 | Pase a | P500-554 | — |
| 2026 | P500-552 | código = 2 | Pase a | P500-556 | — |
| 2026 | P500-558A | código = 1 \|\| 2 \|\| 3 \|\| 4  | Pase a | P500-558B | — |
| 2026 | P500-558A | código = 5 | Pase a | P500-558C | — |
