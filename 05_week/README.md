Zadanie domowe z Tygodnia 4 kursu AZ-303: Microsoft Azure Architect Technologies�TYDZIE� 4: Implement storage accounts

Zadania:
#TYDZIE�5.1 �Dla ka�dego typu Storage, kt�rego si� nauczy�e� w czasie kursu (min. 4 typy) dobierz dwa dobre i jedno z�e zastosowanie. Chcia�bym by� zweryfikowa� r�ne mo�liwo�ci sk�adowania danych w Azure i opowiedzia�, kiedy i do czego te mo�liwo�ci mo�esz wykorzysta�.�
#TYDZIE�5.2
�Wymie� jeden dobry i jeden z�y przyk�ad wykorzystania StorSimple w swojej organizacji. Napisz, kiedy i w jakich scenariuszach si� sprawdzi, a kiedy nie.�
#TYDZIE�5.3
�Liczymy Koszty :). Um�wmy si�. Tw�j system backupu (nie ma znaczenia jaki) sk�aduje 1TB nowych danych ka�dego dnia. Wykorzystujesz oczywi�cie Azure do tej operacji i chcesz dane sk�adowa� jak najtaniej. Przez dwa lata nie kasujesz zebranych danych. Po dw�ch latach na pr�b� odtwarzasz dane z ostatniego dnia ka�dego roku. Po 3 roku kasujesz dane, zebrane w roku pierwszym.

 * Ile ��cznie wygenerujesz koszt�w w ramach tej us�ugi, je�li rozwa�ymy pe�ny, 6 letni okres jej dzia�ania.
 * Rozwa� r�ne aspekty i r�ne mo�liwo�ci us�ug i poka� jako algorytm liczenia przyj��e�.

#TYDZIE�5.1 - Odpowied� do zadania : 
Reprezentacj� us�ugi sk�adowania danych w Azure (Azure Storage) jest Azure Storage Account. Zapewnia unikatow� przestrze� nazewnicz� dla danych sk�adowanych w jej obr�bie by mog�y by� dost�pne za po�rednictwem protoko�u HTTP/S. 
W Storage Account mog� by� reprezentowane wszystkie obiekty sk�adowania danych w Azure tj. : 
                Blobs, Files, Queues, Tables oraz Disks.
Obecnie w Azure funkcjonuj� nast�puj�ce typy Storage Accounts : 
                GPv2, GPv1, BlockBlobStorage, FileStorage oraz BlobStorage
Opcje niezawodno�ciowe dla Storage Accounts to obecnie:
                Locally redundant storage (LRS), Zone-redundant storage (ZRS), Geo-redundant storage (GRS) z Read-Access GRS (RA-GRS) 
                oraz Geo-zone-redundant storage (GZRS) z Read-Access GZRS (RA-GZRS).

                
Typy Storage Account        |Wspierane us�ugi                                        |Dost�pne opcje niezawodno�ciowe       |Dost�pne Opcje Wydajno�ciowe   | Poziomy dost�pno�ci
----------------------------|--------------------------------------------------------|--------------------------------------|-------------------------------|---------------------------
General-purpose V2          |Blob, File, Queue, Table, Disk i Data Lake Gen2         |LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS3 |Premium, Standard              |Hot, Cool, Archive
General-purpose V1          |Blob, File, Queue, Table i Disk                         |LRS, GRS, RA-GRS                      |Premium, Standard              |brak
BlockBlobStorage            |Blob (tylko block blobs i append blobs)                 |LRS, ZRS3                             |Premium                        |brak
FileStorage	                |File                                                    |LRS, ZRS3                             |Premium                        |brak
BlobStorage	                |Blob (tylko block blobs i append blobs)                 |LRS, GRS, RA-GRS                      |Standard                       |Hot, Cool, Archive
                
Jak �atwo si� domy�lic w zadaniu nie chodzi o podanie typ�w Storage Accounts a raczej chodzi podzia� Azure Storage na jego us�ugi tak jak to zosta�o
zrobione na stronie : https://azure.microsoft.com/en-us/services/storage/
Strona ta wymienia nast�puj�ce produkty (us�ugi) Storage :
    - Blob storage
    - Azure Disk Storage
    - Azure Files
    - Azure Data Lake Storage
    - Azure Archive Storage
    - Azure HPC Cache

Przyk�ady dobrych i z�ych zastosowa� poszczeg�lnych 4 z 6 us�ug dost�pnych w ramach Storage Account General Purpose w wersji 2giej (tu. Blob, File, Queue oraz Table) :
   1. Kontenery/Blobs:
        - G��wne zastosowanie to : 
                a. przechowywanie plik�w multimedialnych (i udost�pnianie przez CDN), 
                b. hosting statycznych stron www, 
                c. dyski VHD, 
                d. pliki backup�w 
        - Niew�a�ciwe zastosowanie to : 
                a. hierarchiczne systemy plikowe z zagnie�dzonymi uprawnieniami jak ma to miejsce np. w korporacyjnych dyskach wsp�dzielonych
                b. dynamiczne nadpisywane pliki log�w (zw�aszcza przy w��czonym wersjonowaniu)
                c. analityka du�ych zbior�w dancyh (-> big data analitycs)
        Nale�y zwr�ci� uwag� i�, powy�sze przyk�ady nie s� jednak przyk�adami z�ego zastosowania Blobs Storage. S� tylko nieptymalnymi :) 
        Blobs jako us�uga fundamentalna mo�e zostac u�yta praktycznie do ka�dego celu ;) 
        to tylko kwestia odpowiedniego wykorzystania SDK i metadanych ;)
                
    2. Azure Files
        - G��wne zastosowanie to : 
                a. udost�pnianie dysk�w wsp�dzielonych dla system�w obs�uguj�cych SMB i NFS np. dyski sieciowe w Windows Serwer)
                b. wsz�dzie tam gdzie dostep realizowany przez API musi by� obs�ugiwany szybko, �atwo i powtarzalnie np. poprzez skrypty Powershell, Az Cli, Perl, Go
        - Nie nadaje si� do : 
                a. przetwarzania du�ych wolumen�w danych (ograniczenia do 5TB per udzia�) np. sk�adowanie plik�w backup�w 
                b. wysoko wydajne przetwarzanie danych (ogranczenie na IOPS 10k i transfery do 60MB/s) np. BigData
                c. trzymania danych, gdzie potrzeba jest hierarchicznego systemu uprawnien np. udzia�y NTFS 
                d. gdzie dane musz� by� backupowane a wersje plik�w sledzone np. obiekty dokumentuj�ce Compliance (nagrania, tracking, logi)

    3. Azure (Storage) Queue         
            W tym przypadku, nale�y raczej odpowiedzie� w jakich przypadkach stosowa� Azure Storage Queue a w jakich Azure Service Bus.
            I tak (z grubsza) Azure Storage Queue stosuje si� do kolejek, kt�re:
                a. maj� zak�adana pojemno�� wieksz� ni� 80GB
                b. musz� �ledzi� przetwarzanie wiadomo�ci 
                c. wymagane jest niezale�ne (od logowania worker�w/konsument�w) logowanie transakcji przy przetwarzaniu wiadomo�ci
            Jest to temat na tyle skomplikowany, �e nawet nie pokusz� si� o przyk�ady, gdzie mog�o by mie� zastosowanie wy��cznie Azure Storage Queue
            (za https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)
            
    4. Azure Table
            Podobnie jak z Azure Storage Queue, o tym czy do konkretnego zastosowania mo�na u�y� Azure Storage Table czy te� CosmosDB czy Azure SQL musi sobie odpowiedzie�
            sam architekt. Musi on jednak miec na wzgl�dzie nastepuj�ce ograniczenia:
                a. nie ma g�rnego ogranicznia na czas odpowied� (CosmosDB ma do kilku ms)
                b. limit na d�ugo�� wiersza (1MB)
                c. nie wspiera popularnych API NoSQL
                d. nie ma mozliwo�ci nadawania uprawnien granularnych
                e. nie wspiera budowy aplikacji o zasiegu �wiatowym 
                f. baza danych typu NoSQL
            i wiele innych. ..
            Azure Table jest to nic innego jak bardzo prosta baza danych NoSQL. Z wszystkimi jej wadami i zaletami :)



#TYDZIE�5.2 Odpowied� do zadania
�Wymie� jeden dobry i jeden z�y przyk�ad wykorzystania StorSimple w swojej organizacji. Napisz, kiedy i w jakich scenariuszach si� sprawdzi, a kiedy nie.�


Azure StorSimple to dedykowane rozwi�zanie, kt�ra ma za zadanie u�atwi� zarz�dzanie danymi. Ze wzgl�du na to jak zosta�a skonstruowane mo�na od razu zauwa�y�, i� nada si� ws�edzie tam gdzie:
    - jest potrzeba skonsolidowania du�ych zasob�w r�nych danych 
    - jest potrzeba funkcjonowania centralnych repozytori�w np. Backup�w, nagra� monitoringu, zapisu log�w, wytworzonych build�w, aktualizowanej dokumentacji etc
    - polityka bezpiecze�stwa wskazuje �e backupy powinny by� off-sitowane poza siedzibe firmy (chmura)
    - gdzie plany DR przewiduj� prawie natychmiastowe odzyskiwanie danych w zapasowym centrum firmy
    - tranzycja do zasob�w chmurowych musi obywa� si� etapowo i w d�ugim okresie czasu
    - potrzebna jest niedroga macierz o ponadprzeci�tnej pojemno�ci :)
Trzeba jednak pamieta�, i� mimo StorSimple jest r�wnie� macierz� dyskow� (lub mo�e byc tak traktowana) to nie zaleca si� jej korzysta� w przypadku:
    - s�abego lub przeci��onego �acza internetowego do chmury Azure
    - sk�adowania plik�w o du�ej rotacji zawarto�ci jak np. dyski wirtualne, pliki DB etc.
    - szybkiej migracji do chmury Azure du�ych wolumen�w danych 

W naszej organizacji Azure StorSimple napewno sprawdzi� by si� jako repozytorium kodu oraz build�w. Miejsca pod to zawsze brakuje do tego stopnia �e trzeba ograncza� ilo�� build�w :)
Na pewno bym go nie uzy� jako macirzy do HyperV lub ESXi. Tam dyski VHD musz� by� dost�pne ca�y czas i drobnymi rozmiarami raczej nie grzesz� :)
    
#TYDZIE�5.3 Odpowied�:
 
Polityka backupu:
- backup ka�dego dnia (+1TB)
- odtwarzanie ka�dego roku backupu rocznego, pierwszy raz po dw�ch latach
- usuwanie backup�w starszych ni� 1095 dni (3 lata) - (zak�adam, ze w tym przypadku chodzi�o o polise tranzycji danych niz samodzielne usuwanie duzych zbior�w danych)

Wyliczenia w scenariuszu PAYG (Pay-As-You-Go):
  Przyjeto :
            rejon               : North Europe (obecnie najta�szy w Europie)
            us�uga              : Blob Stroage
            poziom dost�pno�ci  : Archive
            1TB                 : 1024GB

obliczenia dla taryfikatora wed�ug p�atno�ci PAYG:
CX - "srednia" cena z cennika za 1GB w wybranej us�udze sk�adowania przez jeden miesiac dla wybranego regionu oraz typu us�ugi. 
    Uwaga: Za "sredni" kalkulator koszt�w przyjmuje okres 30 dni.
    CX = 0,00099$
DXG - koszt przechowywania 1GB przez 1 dzie� (CX/30)
    DXG = 0,000033$
DXT - koszt przechowywania 1TB przez 1 dzie� (1024xDXG)
    DXT = 0,033792$
TXY - koszt przechowywania 1TB danych przez rok (365xDXT). 
    TXY = 12,3341$
RX1 - koszt przechowywania 100TB danych przez 1 rok (100xTXY)
    RX1 = 1233,408$
RXY - koszt przechowywania 365TB danych przez 1 rok (365xTXY)
    RXY = 4501,9465$
AX1 - koszt gromadzenia 100TB danych (po 1TB przez 100 dni) + koszt ich przechowywania w danym roku. 
        Koszt zosta� policzony wg wzoru na sum� ci�gu (kazdy dodany 1TB jest skladowany o dzien krocej od poprzednika):
    AX1 = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(265/365)
    AX1 = 1066,14$
AXY - koszt gromadzenia 365TB danych (po 1TB przez 365 dni) + koszt ich przechowywania w danym roku. 
        Koszt zosta� policzony wg wzoru na sum� ci�gu (kazdy nastepny 1TB jest skladowany o dzien krocej w roku):
    AXY = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(1/365)
    AXY = 2257,14$
GX - koszt gromadzenia i sk�adowania 1095TB danych przez trzy pierwsze lata. 
    GX = AXY + (AXY+RXY) + (AXY+2xRXY) = 3xAXY + 3xRXY = 6769,17$ + 13505,79$
    GX = 20274,96$

HXY - koszt sk�adowania 1095TB danych przez rok (1095xTXY)
    HXY = 13505,84$
HX3 - koszt sk�adowania 1095TB (3x365) danych przez 3 lata (3xHXY)
    HX3 = 40517,52$
WX - koszt ca�kowity gromadzenia i sk�adowania 1095TB danych przez 6lat
    WX = HX3 + GX = 60792,48$
    
Ca�kowity koszt sk�adowania za�o�onej ilo�ci danych zamyka si� w kwocie 60792,48$. 

Aby uzyska� oszczedno�ci z tej kwoty nale�y przeprowadzi� symulacje koszt�w w przedzialach czasowych oraz estymowanych kosztach rezerwacji.
Dodatkowo aby miec por�nanie do rezerwacji na 1PB nale�a�o oblyiczy� koszt gromadzenie 1PB danych.
GX1 - koszt gromadzenia i sk�adowania 1PB danych w ciagu pierwszych trzech lat.  
    AX4 = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(71/365) = 2173,17$  (gdy� 1095-1024=71)
    GX1 = AXY + (AXY+RXY) + (AX4+2xRXY) = 2xAXY + 3xRXY + AX4
    GX1 = 4514,28$ + 13505,8395$ + 2173,17$
    GX1 = 20193,29$

r�znica miedzy zebraniem 1PB a 1095TB (GX-GX1) wynosi 81,67$

                                        |PAYG 1-year    | 1-year FU reservation |RO�NICA
----------------------------------------|---------------|-----------------------|----------
koszt gromadzenia 100TB                 | 1066,14$      | 1083,00$              |  -16,86$
Koszt sk�adowania 100TB                 | 1223,33$      | 1083,00$              |  140,00$
Koszt gromadzenia 200TB                 | 1794,36$      | 2166,00$              | -371,64$
Koszt gromadzenia 300TB                 | 2186,85$      | 3249,00$              |-1062,15$
Koszt gromadzenia 365TB                 | 2257,14$      | 4332,00$              |-2074,86$
koszt gromadzenia 1PB                   |N/A            |10589,00$              |N/A
Koszt sk�adowania 1PB                   |12630,09$      |10589,00$              | 2041,09$


                                        |PAYG 3-year    | 3-year FU reservation |RO�NICA
----------------------------------------|---------------|-----------------------|---------
koszt gromadzenia (pierwszych) 100TB    | 3512,80$      | 2993,00$              |  519,80$
koszt gromadzenia (pierwszych) 200TB    | 6687,68$      | 5986,00$              |  701,86$
koszt gromadzenia (pierwszych) 300TB    | 9526,83$      | 8979,00$              |  547,83$
koszt gromadzenia (pierwszych) 400TB    |11692,71$      |11972,00$              | -279,28$ 
koszt gromadzenia 1PB                   |20193,29$      |29150,00$              |-8956,71$
Koszt sk�adowania 100TB                 | 3670,23$      | 2993,00$              |  677,33$
Koszt sk�adowania 200TB                 | 7340,46$      | 5986,00$              | 1354,46$
Koszt sk�adowania 300TB                 |11010,69$      | 8979,00$              | 2031,69$
Koszt sk�adowania 1PB                   |37890,65$      |29150,00$              | 8740,65$

koszt skladowania 1095TB w PAYG przez 71 dni
2627,16$
koszt sk�adowania 71TB w PAYG przez 3 lata
2605,69$
granica oplacalnosci zakladania rezerwacji FU na 100TB na 1 rok
88TB
granica oplacalnosci zakladania rezerwacji FU na 100TB na 3lata
81TB

Jak wida� najwieksze r�znice uzyskujemy przy rezerwacjach trzyletnich. Natomiast, w przypadku pierwszych trzech lat sensowne wydaje sie by� zalo�enie 
rezerwacji trzyletnich na pierwsze 200TB danych i nastepnie na pojawiaj�ce si� pe�ne 100TB (pe�ne w sensie roku kalendarzowego) zak�adanie 
rezerwacji 1-dno rocznych. 
Moja propozycja planu wykupu rezerwacji:
 1. przed rozpocz�ciem, wykupienie rezerwacji FU 3 letnich na 2x100TB w ten spos�b odzyskamy z og�lnego rachunku     :  701,86$
 2. po 1-szym roku wykupienie 1 rezerwacji FU 1 rocznych na 100TB w ten spos�b odzyskamy z og�lnego rachunku         :  140,00$
 3. po 2-�ch latach wykupienie 5 rezerewacji FU 1 rocznych na 100TB w ten spos�b odzyskamy z og�lnego rachunku       :  700,00$
 4. po trzech latach wykupienie 1 rezerwacji FU na 3 lata na 1PB w ten spos�b odzyskamy z og�lnego rachunku          : 8740,65
 5. r�nica wynikaj�ca 1PB i 1095TB (71TB) musi byc op�acona w trybie PAYG, 
    gdy� jest pod progiem op�acalno�ci wykupu rezerwacji (81TB)
 
Sumarycznie z rachunku PAYG odzyskujemy 10282,51$
koszt sk�adowania naszych danych zamyka si� w tym przypadku w kwocie 50�509,97$.

Ponadto zadanie wspomina o cyklicznym testowaniu backup�w rocznych, kt�re rozpoczyna si� po pierwszych dw�ch latach.
Wynika z tego, ze w okresie 6 lat odbeda si� co najmniej 4 takie akcje.
Koszt jednej bedzie g��wnie kosztem sci�gniecia 1TB z chmury Azure czyli 22,53$. 
Sumarycznie za wszystkie akcje wyjdzie 90,12$