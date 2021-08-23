%nume player si varsta
player(ionel, 15).
player(marcel, 13).
player(costel, 8).
player(gigel, 11).

%varsta minima pentru a putea juca
poate_juca(X, Y) :- player(X, Y), Y > 9.

%campioni posibili - attack power - life - mana - home - in momentul in care se vor
%lupta
campion(mage, 55, 100, 100, iceLand).
campion(elementalist, 35, 100, 100, waterLand).
campion(human, 46, 110, 100, earthLand).
campion(orc, 70, 70, 100, fireLand).

%tipul de dmg
dmgType(mage, abilityPower).
dmgType(elementalist, abilityPower).
dmgType(human, attackDmg).
dmgType(orc, attackDmg).

%nevoie de armura daca viata campionului < 100
needs_armour(X) :- campion(X, _, Y, _, _), Y < 100.
%nevoie de dmg daca dmg ul campionului < 50
needs_dmg(X) :- campion(X, Y, _, _, _), Y < 50.

%copiii care baga bani in jocuri
buysSkins(ionel).
buysSkins(costel).
buysSkins(gigel).

%campion ales - cu afisare daca poate juca jocul
%sau nu
campionAles(gigel, mage) :- 
    (	poate_juca(gigel, _)
    ->  write('Poate juca')
    ;   write('Nu poate juca')
    ).
campionAles(ionel, elementalist) :- 
    (	poate_juca(ionel, _)
    ->  write('Poate juca')
    ;   write('Nu poate juca')
    ).
campionAles(costel, orc) :- 
    (	poate_juca(costel, _)
    ->  write('Poate juca')
    ;   write('Nu poate juca')
    ).
campionAles(marcel, human) :- 
    (	poate_juca(marcel, _)
    ->  write('Poate juca')
    ;   write('Nu poate juca')
    ).

%Tot campionul ales - dar fara afisare daca poate juca jocul.
%Le folosesc in alte query`uri, in care nu vreau sa afisez si
%daca un copil poate juca sau nu jocul - cele cu afisare le-am folosit
%doar sa vad cum se afiseaza un mesaj
campionAlesWithoutWriting(gigel, mage) :- poate_juca(gigel, _).
campionAlesWithoutWriting(ionel, elementalist) :- poate_juca(ionel, _).
campionAlesWithoutWriting(costel, orc) :- poate_juca(costel, _).
campionAlesWithoutWriting(marcel, human) :- poate_juca(marcel, _).


%locuri unde pot exista luptele
% --- mage ---  vs --- elementalist ---
lupta(mage, elementalist, iceLand). 
lupta(elementalist, mage, iceLand).

lupta(mage, elementalist, fireLand). 
lupta(elementalist, mage, fireLand).

lupta(mage, elementalist, waterLand). 
lupta(elementalist, mage, waterLand). 

lupta(mage, elementalist, earthLand). 
lupta(elementalist, mage, earthLand). 

% --- mage ---  vs --- human ---
lupta(mage, human, iceLand). 
lupta(human, mage, iceLand).

lupta(mage, human, fireLand). 
lupta(human, mage, fireLand).

lupta(mage, human, waterLand). 
lupta(human, mage, waterLand). 

lupta(mage, human, earthLand). 
lupta(human, mage, earthLand). 

% --- mage ---  vs --- orc ---
lupta(mage, orc, iceLand). 
lupta(orc, mage, iceLand).

lupta(mage, orc, fireLand). 
lupta(orc, mage, fireLand).

lupta(mage, orc, waterLand). 
lupta(orc, mage, waterLand). 

lupta(mage, orc, earthLand). 
lupta(orc, mage, earthLand).


% --- human ---  vs --- orc ---
lupta(human, orc, iceLand). 
lupta(orc, human, iceLand).

lupta(human, orc, fireLand). 
lupta(orc, human, fireLand).

lupta(human, orc, waterLand). 
lupta(orc, human, waterLand). 

lupta(human, orc, earthLand). 
lupta(orc, human, earthLand).


% --- elementalist ---  vs --- orc ---
lupta(elementalist, orc, iceLand). 
lupta(orc, elementalist, iceLand).

lupta(elementalist, orc, fireLand). 
lupta(orc, elementalist, fireLand).

lupta(elementalist, orc, waterLand). 
lupta(orc, elementalist, waterLand). 

lupta(elementalist, orc, earthLand). 
lupta(orc, elementalist, earthLand).


% --- elementalist ---  vs --- human ---
lupta(elementalist, human, iceLand). 
lupta(human, elementalist, iceLand).

lupta(elementalist, human, fireLand). 
lupta(human, elementalist, fireLand).

lupta(elementalist, human, waterLand). 
lupta(human, elementalist, waterLand). 

lupta(elementalist, human, earthLand). 
lupta(human, elementalist, earthLand).

%puteri ale campionilor
power(human, shield).
power(elementalist, heal).
power(orc, reflect).
power(mage, dmg).

%care dintre campioni sunt pe defence si cu ce putere fac def
defence(X, Y) :- power(X, Y), Y = shield;
    		    power(X, Y), Y = heal;
				power(X, Y), Y = reflect.

%care dintre campioni sunt pe atack
attack(X, Y) :- power(X, Y), Y = dmg.

%se verifica carui player ii place atacul sau defense`ul in functie de 
%ce campion joaca - se afiseaza player + campion
likesDefence(X, Y) :- defence(Y, _), campionAlesWithoutWriting(X, K2), Y = K2.
likesAttack(X, Y) :- attack(Y, _),  campionAlesWithoutWriting(X, K2), Y = K2.

%bonusuri si superputeri - N = campion, SP = superputerea(shiled, heal, reflect, dmg), Z = locul unde are
%superputerea - diferenta fata de power e ca aflam si unde poate sa foloseasca superputerea
superputere(N, SP, Z) :- power(N, SP), campion(N, _, _, _,Z), N = human, Z = earthLand;
						 power(N, SP), campion(N, _, _, _, Z), N = elementalist, Z = waterLand;
 						 power(N, SP), campion(N, _, _, _, Z), N = orc, Z = fireLand;
						 power(N, SP), campion(N, _, _, _, Z), N = mage, Z = iceLand.


%ma ajuta sa vad cine o sa castige luptele
humanCantWinAgainst(X, Z) :- X = elementalist, Z = waterLand;
    					X = orc, Z = fireLand;
    					X = mage, Z = iceLand.

orcCantWinAgainst(X, Z) :- X = elementalist, Z = waterLand;
    						    X = mage, Z = iceLand;
    							X = human, Z = earthLand.

elementalistCantWinAgainst(X, Z) :- X = mage, Z = iceLand;
    										      X = human, Z = earthLand;
    											  X = orc, Z = fireLand.

mageCantWinAgainst(X, Z) :- X = elementalist, Z = waterLand;
    										      X = human, Z = earthLand;
    											  X = orc, Z = fireLand.
    
    				 
%aici e ceva
%winnuri - contra cine castiga si unde castiga
%mereu un erou castiga acasa sau pe teren neutru daca 
%diferenta viataAdversar - dmgDat = viataSa - dmgAdversar sau daca adversarul nu are 100
%la mana - ca nu poate da destule spells :))

%daca nu are mana pierde si acasa ca nu isi activeaza powers
win(human, Y, Z) :- lupta(human, Y, Z), Z = earthLand;
    				lupta(human, Y, Z), campion(Y, _, _, M1, _), Z \= earthLand, M1 \= 100;
    			    lupta(human, Y, Z), campion(human, D, L, M, _), campion(Y, D1, L1, M1, _), L - D1 > L1 - D, Z \= earthLand, M = 100, M1 = 100, not(humanCantWinAgainst(Y, Z)).

win(orc, Y, Z) :- lupta(orc, Y, Z), Z = fireLand;
    			  lupta(orc, Y, Z), campion(Y, _, _, M1, _), Z \= fireLand, M1 \= 100;
    			  lupta(orc, Y, Z), campion(orc, D, L, M, _), campion(Y, D1, L1, M1, _), L - D1 > L1 - D, Z \= fireLand, M = 100, M1 = 100, not(orcCantWinAgainst(Y, Z)). 

win(elementalist, Y, Z) :-  lupta(elementalist, Y, Z), Z = waterLand;
    						lupta(elementalist, Y, Z), campion(Y, _, _, M1, _), Z \= waterLand, M1 \= 100;
    			 			lupta(elementalist, Y, Z), campion(elementalist, D, L, M, _), campion(Y, D1, L1, M1, _), L - D1 > L1 - D, Z \= waterLand, M = 100, M1 = 100, not(elementalistCantWinAgainst(Y, Z)). 


win(mage, Y, Z) :- lupta(mage, Y, Z), Z = iceLand;
    			 lupta(mage, Y, Z), campion(Y, _, _, M1, _), Z \= iceLand, M1 \= 100;
    			 lupta(mage, Y, Z), campion(mage, D, L, M, _), campion(Y, D1, L1, M1, _), L - D1 > L1 - D, Z \= iceLand, M = 100, M1 = 100, not(mageCantWinAgainst(Y, Z)). 

%daca mana nu este 100 - are nevoie pt ca ar pierde
needsManaPotions(X) :- campion(X, _, _, M, _), M < 100.
%abilityPower type consuma mana
consumesMana(X) :- dmgType(X, abilityPower). 
%attackDmg type consuma energie
consumesEnergy(X) :- dmgType(X, attackDmg).
%daca player`ul cumpara skins si poate juca jocul
insertsMoney(X) :- player(X, Y), poate_juca(X, Y), buysSkins(X).

%numarul de jucatori
nr_jucatori([], 0).
nr_jucatori([_|X],N) :- nr_jucatori(X, N1), N is N1 + 1.
numar_jucatori(N) :- nr_jucatori([ionel, marcel, costel, gigel], N).

%cauta daca un jucatorul dat se afla printre toti jucatorii
este_jucator(X, [X|_]) :- !.
este_jucator(X, [_|Y]) :- este_jucator(X,Y).
exista_jucatorul(X) :- este_jucator(X, [ionel, marcel, costel, gigel]).

             