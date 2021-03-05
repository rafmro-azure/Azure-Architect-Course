Zadanie domowe z Tygodnia 4 kursu AZ-303: Microsoft Azure Architect Technologies›TYDZIEÑ 4: Implement storage accounts

Zadania:
#TYDZIEÑ5.1 „Dla ka¿dego typu Storage, którego siê nauczy³eœ w czasie kursu (min. 4 typy) dobierz dwa dobre i jedno z³e zastosowanie. Chcia³bym byœ zweryfikowa³ ró¿ne mo¿liwoœci sk³adowania danych w Azure i opowiedzia³, kiedy i do czego te mo¿liwoœci mo¿esz wykorzystaæ.”
#TYDZIEÑ5.2
„Wymieñ jeden dobry i jeden z³y przyk³ad wykorzystania StorSimple w swojej organizacji. Napisz, kiedy i w jakich scenariuszach siê sprawdzi, a kiedy nie.”
#TYDZIEÑ5.3
„Liczymy Koszty :). Umówmy siê. Twój system backupu (nie ma znaczenia jaki) sk³aduje 1TB nowych danych ka¿dego dnia. Wykorzystujesz oczywiœcie Azure do tej operacji i chcesz dane sk³adowaæ jak najtaniej. Przez dwa lata nie kasujesz zebranych danych. Po dwóch latach na próbê odtwarzasz dane z ostatniego dnia ka¿dego roku. Po 3 roku kasujesz dane, zebrane w roku pierwszym.

 * Ile ³¹cznie wygenerujesz kosztów w ramach tej us³ugi, jeœli rozwa¿ymy pe³ny, 6 letni okres jej dzia³ania.
 * Rozwa¿ ró¿ne aspekty i ró¿ne mo¿liwoœci us³ug i poka¿ jako algorytm liczenia przyj¹³eœ.

#TYDZIEÑ5.1 - OdpowiedŸ do zadania : 
Reprezentacj¹ us³ugi sk³adowania danych w Azure (Azure Storage) jest Azure Storage Account. Zapewnia unikatow¹ przestrzeñ nazewnicz¹ dla danych sk³adowanych w jej obrêbie by mog³y byæ dostêpne za poœrednictwem protoko³u HTTP/S. 
W Storage Account mog¹ byæ reprezentowane wszystkie obiekty sk³adowania danych w Azure tj. : 
                Blobs, Files, Queues, Tables oraz Disks.
Obecnie w Azure funkcjonuj¹ nastêpuj¹ce typy Storage Accounts : 
                GPv2, GPv1, BlockBlobStorage, FileStorage oraz BlobStorage
Opcje niezawodnoœciowe dla Storage Accounts to obecnie:
                Locally redundant storage (LRS), Zone-redundant storage (ZRS), Geo-redundant storage (GRS) z Read-Access GRS (RA-GRS) 
                oraz Geo-zone-redundant storage (GZRS) z Read-Access GZRS (RA-GZRS).

                
Typy Storage Account        |Wspierane us³ugi                                        |Dostêpne opcje niezawodnoœciowe       |Dostêpne Opcje Wydajnoœciowe   | Poziomy dostêpnoœci
----------------------------|--------------------------------------------------------|--------------------------------------|-------------------------------|---------------------------
General-purpose V2          |Blob, File, Queue, Table, Disk i Data Lake Gen2         |LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS3 |Premium, Standard              |Hot, Cool, Archive
General-purpose V1          |Blob, File, Queue, Table i Disk                         |LRS, GRS, RA-GRS                      |Premium, Standard              |brak
BlockBlobStorage            |Blob (tylko block blobs i append blobs)                 |LRS, ZRS3                             |Premium                        |brak
FileStorage	                |File                                                    |LRS, ZRS3                             |Premium                        |brak
BlobStorage	                |Blob (tylko block blobs i append blobs)                 |LRS, GRS, RA-GRS                      |Standard                       |Hot, Cool, Archive
                
Jak ³atwo siê domyœlic w zadaniu nie chodzi o podanie typów Storage Accounts a raczej chodzi podzia³ Azure Storage na jego us³ugi tak jak to zosta³o
zrobione na stronie : https://azure.microsoft.com/en-us/services/storage/
Strona ta wymienia nastêpuj¹ce produkty (us³ugi) Storage :
    - Blob storage
    - Azure Disk Storage
    - Azure Files
    - Azure Data Lake Storage
    - Azure Archive Storage
    - Azure HPC Cache

Przyk³ady dobrych i z³ych zastosowañ poszczególnych 4 z 6 us³ug dostêpnych w ramach Storage Account General Purpose w wersji 2giej (tu. Blob, File, Queue oraz Table) :
   1. Kontenery/Blobs:
        - G³ówne zastosowanie to : 
                a. przechowywanie plików multimedialnych (i udostêpnianie przez CDN), 
                b. hosting statycznych stron www, 
                c. dyski VHD, 
                d. pliki backupów 
        - Niew³aœciwe zastosowanie to : 
                a. hierarchiczne systemy plikowe z zagnie¿dzonymi uprawnieniami jak ma to miejsce np. w korporacyjnych dyskach wspó³dzielonych
                b. dynamiczne nadpisywane pliki logów (zw³aszcza przy w³¹czonym wersjonowaniu)
                c. analityka du¿ych zbiorów dancyh (-> big data analitycs)
        Nale¿y zwróciæ uwagê i¿, powy¿sze przyk³ady nie s¹ jednak przyk³adami z³ego zastosowania Blobs Storage. S¹ tylko nieptymalnymi :) 
        Blobs jako us³uga fundamentalna mo¿e zostac u¿yta praktycznie do ka¿dego celu ;) 
        to tylko kwestia odpowiedniego wykorzystania SDK i metadanych ;)
                
    2. Azure Files
        - G³ówne zastosowanie to : 
                a. udostêpnianie dysków wspó³dzielonych dla systemów obs³uguj¹cych SMB i NFS np. dyski sieciowe w Windows Serwer)
                b. wszêdzie tam gdzie dostep realizowany przez API musi byæ obs³ugiwany szybko, ³atwo i powtarzalnie np. poprzez skrypty Powershell, Az Cli, Perl, Go
        - Nie nadaje siê do : 
                a. przetwarzania du¿ych wolumenów danych (ograniczenia do 5TB per udzia³) np. sk³adowanie plików backupów 
                b. wysoko wydajne przetwarzanie danych (ogranczenie na IOPS 10k i transfery do 60MB/s) np. BigData
                c. trzymania danych, gdzie potrzeba jest hierarchicznego systemu uprawnien np. udzia³y NTFS 
                d. gdzie dane musz¹ byæ backupowane a wersje plików sledzone np. obiekty dokumentuj¹ce Compliance (nagrania, tracking, logi)

    3. Azure (Storage) Queue         
            W tym przypadku, nale¿y raczej odpowiedzieæ w jakich przypadkach stosowaæ Azure Storage Queue a w jakich Azure Service Bus.
            I tak (z grubsza) Azure Storage Queue stosuje siê do kolejek, które:
                a. maj¹ zak³adana pojemnoœæ wieksz¹ ni¿ 80GB
                b. musz¹ œledziæ przetwarzanie wiadomoœci 
                c. wymagane jest niezale¿ne (od logowania workerów/konsumentów) logowanie transakcji przy przetwarzaniu wiadomoœci
            Jest to temat na tyle skomplikowany, ¿e nawet nie pokuszê siê o przyk³ady, gdzie mog³o by mieæ zastosowanie wy³¹cznie Azure Storage Queue
            (za https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)
            
    4. Azure Table
            Podobnie jak z Azure Storage Queue, o tym czy do konkretnego zastosowania mo¿na u¿yæ Azure Storage Table czy te¿ CosmosDB czy Azure SQL musi sobie odpowiedzieæ
            sam architekt. Musi on jednak miec na wzglêdzie nastepuj¹ce ograniczenia:
                a. nie ma górnego ogranicznia na czas odpowiedŸ (CosmosDB ma do kilku ms)
                b. limit na d³ugoœæ wiersza (1MB)
                c. nie wspiera popularnych API NoSQL
                d. nie ma mozliwoœci nadawania uprawnien granularnych
                e. nie wspiera budowy aplikacji o zasiegu œwiatowym 
                f. baza danych typu NoSQL
            i wiele innych. ..
            Azure Table jest to nic innego jak bardzo prosta baza danych NoSQL. Z wszystkimi jej wadami i zaletami :)



#TYDZIEÑ5.2 OdpowiedŸ do zadania
„Wymieñ jeden dobry i jeden z³y przyk³ad wykorzystania StorSimple w swojej organizacji. Napisz, kiedy i w jakich scenariuszach siê sprawdzi, a kiedy nie.”


Azure StorSimple to dedykowane rozwi¹zanie, która ma za zadanie u³atwiæ zarz¹dzanie danymi. Ze wzglêdu na to jak zosta³a skonstruowane mo¿na od razu zauwa¿yæ, i¿ nada siê ws¿edzie tam gdzie:
    - jest potrzeba skonsolidowania du¿ych zasobów ró¿nych danych 
    - jest potrzeba funkcjonowania centralnych repozytoriów np. Backupów, nagrañ monitoringu, zapisu logów, wytworzonych buildów, aktualizowanej dokumentacji etc
    - polityka bezpieczeñstwa wskazuje ¿e backupy powinny byæ off-sitowane poza siedzibe firmy (chmura)
    - gdzie plany DR przewiduj¹ prawie natychmiastowe odzyskiwanie danych w zapasowym centrum firmy
    - tranzycja do zasobów chmurowych musi obywaæ siê etapowo i w d³ugim okresie czasu
    - potrzebna jest niedroga macierz o ponadprzeciêtnej pojemnoœci :)
Trzeba jednak pamietaæ, i¿ mimo StorSimple jest równie¿ macierz¹ dyskow¹ (lub mo¿e byc tak traktowana) to nie zaleca siê jej korzystaæ w przypadku:
    - s³abego lub przeci¹¿onego ³acza internetowego do chmury Azure
    - sk³adowania plików o du¿ej rotacji zawartoœci jak np. dyski wirtualne, pliki DB etc.
    - szybkiej migracji do chmury Azure du¿ych wolumenów danych 

W naszej organizacji Azure StorSimple napewno sprawdzi³ by siê jako repozytorium kodu oraz buildów. Miejsca pod to zawsze brakuje do tego stopnia ¿e trzeba ogranczaæ iloœæ buildów :)
Na pewno bym go nie uzy³ jako macirzy do HyperV lub ESXi. Tam dyski VHD musz¹ byæ dostêpne ca³y czas i drobnymi rozmiarami raczej nie grzesz¹ :)
    
#TYDZIEÑ5.3 OdpowiedŸ:
 
Polityka backupu:
- backup ka¿dego dnia (+1TB)
- odtwarzanie ka¿dego roku backupu rocznego, pierwszy raz po dwóch latach
- usuwanie backupów starszych ni¿ 1095 dni (3 lata) - (zak³adam, ze w tym przypadku chodzi³o o polise tranzycji danych niz samodzielne usuwanie duzych zbiorów danych)

Wyliczenia w scenariuszu PAYG (Pay-As-You-Go):
  Przyjeto :
            rejon               : North Europe (obecnie najtañszy w Europie)
            us³uga              : Blob Stroage
            poziom dostêpnoœci  : Archive
            1TB                 : 1024GB

obliczenia dla taryfikatora wed³ug p³atnoœci PAYG:
CX - "srednia" cena z cennika za 1GB w wybranej us³udze sk³adowania przez jeden miesiac dla wybranego regionu oraz typu us³ugi. 
    Uwaga: Za "sredni" kalkulator kosztów przyjmuje okres 30 dni.
    CX = 0,00099$
DXG - koszt przechowywania 1GB przez 1 dzieñ (CX/30)
    DXG = 0,000033$
DXT - koszt przechowywania 1TB przez 1 dzieñ (1024xDXG)
    DXT = 0,033792$
TXY - koszt przechowywania 1TB danych przez rok (365xDXT). 
    TXY = 12,3341$
RX1 - koszt przechowywania 100TB danych przez 1 rok (100xTXY)
    RX1 = 1233,408$
RXY - koszt przechowywania 365TB danych przez 1 rok (365xTXY)
    RXY = 4501,9465$
AX1 - koszt gromadzenia 100TB danych (po 1TB przez 100 dni) + koszt ich przechowywania w danym roku. 
        Koszt zosta³ policzony wg wzoru na sumê ci¹gu (kazdy dodany 1TB jest skladowany o dzien krocej od poprzednika):
    AX1 = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(265/365)
    AX1 = 1066,14$
AXY - koszt gromadzenia 365TB danych (po 1TB przez 365 dni) + koszt ich przechowywania w danym roku. 
        Koszt zosta³ policzony wg wzoru na sumê ci¹gu (kazdy nastepny 1TB jest skladowany o dzien krocej w roku):
    AXY = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(1/365)
    AXY = 2257,14$
GX - koszt gromadzenia i sk³adowania 1095TB danych przez trzy pierwsze lata. 
    GX = AXY + (AXY+RXY) + (AXY+2xRXY) = 3xAXY + 3xRXY = 6769,17$ + 13505,79$
    GX = 20274,96$

HXY - koszt sk³adowania 1095TB danych przez rok (1095xTXY)
    HXY = 13505,84$
HX3 - koszt sk³adowania 1095TB (3x365) danych przez 3 lata (3xHXY)
    HX3 = 40517,52$
WX - koszt ca³kowity gromadzenia i sk³adowania 1095TB danych przez 6lat
    WX = HX3 + GX = 60792,48$
    
Ca³kowity koszt sk³adowania za³o¿onej iloœci danych zamyka siê w kwocie 60792,48$. 

Aby uzyskaæ oszczednoœci z tej kwoty nale¿y przeprowadziæ symulacje kosztów w przedzialach czasowych oraz estymowanych kosztach rezerwacji.
Dodatkowo aby miec porónanie do rezerwacji na 1PB nale¿a³o oblyiczyæ koszt gromadzenie 1PB danych.
GX1 - koszt gromadzenia i sk³adowania 1PB danych w ciagu pierwszych trzech lat.  
    AX4 = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(71/365) = 2173,17$  (gdy¿ 1095-1024=71)
    GX1 = AXY + (AXY+RXY) + (AX4+2xRXY) = 2xAXY + 3xRXY + AX4
    GX1 = 4514,28$ + 13505,8395$ + 2173,17$
    GX1 = 20193,29$

róznica miedzy zebraniem 1PB a 1095TB (GX-GX1) wynosi 81,67$

                                        |PAYG 1-year    | 1-year FU reservation |RO¯NICA
----------------------------------------|---------------|-----------------------|----------
koszt gromadzenia 100TB                 | 1066,14$      | 1083,00$              |  -16,86$
Koszt sk³adowania 100TB                 | 1223,33$      | 1083,00$              |  140,00$
Koszt gromadzenia 200TB                 | 1794,36$      | 2166,00$              | -371,64$
Koszt gromadzenia 300TB                 | 2186,85$      | 3249,00$              |-1062,15$
Koszt gromadzenia 365TB                 | 2257,14$      | 4332,00$              |-2074,86$
koszt gromadzenia 1PB                   |N/A            |10589,00$              |N/A
Koszt sk³adowania 1PB                   |12630,09$      |10589,00$              | 2041,09$


                                        |PAYG 3-year    | 3-year FU reservation |RO¯NICA
----------------------------------------|---------------|-----------------------|---------
koszt gromadzenia (pierwszych) 100TB    | 3512,80$      | 2993,00$              |  519,80$
koszt gromadzenia (pierwszych) 200TB    | 6687,68$      | 5986,00$              |  701,86$
koszt gromadzenia (pierwszych) 300TB    | 9526,83$      | 8979,00$              |  547,83$
koszt gromadzenia (pierwszych) 400TB    |11692,71$      |11972,00$              | -279,28$ 
koszt gromadzenia 1PB                   |20193,29$      |29150,00$              |-8956,71$
Koszt sk³adowania 100TB                 | 3670,23$      | 2993,00$              |  677,33$
Koszt sk³adowania 200TB                 | 7340,46$      | 5986,00$              | 1354,46$
Koszt sk³adowania 300TB                 |11010,69$      | 8979,00$              | 2031,69$
Koszt sk³adowania 1PB                   |37890,65$      |29150,00$              | 8740,65$

koszt skladowania 1095TB w PAYG przez 71 dni
2627,16$
koszt sk³adowania 71TB w PAYG przez 3 lata
2605,69$
granica oplacalnosci zakladania rezerwacji FU na 100TB na 1 rok
88TB
granica oplacalnosci zakladania rezerwacji FU na 100TB na 3lata
81TB

Jak widaæ najwieksze róznice uzyskujemy przy rezerwacjach trzyletnich. Natomiast, w przypadku pierwszych trzech lat sensowne wydaje sie byæ zalo¿enie 
rezerwacji trzyletnich na pierwsze 200TB danych i nastepnie na pojawiaj¹ce siê pe³ne 100TB (pe³ne w sensie roku kalendarzowego) zak³adanie 
rezerwacji 1-dno rocznych. 
Moja propozycja planu wykupu rezerwacji:
 1. przed rozpoczêciem, wykupienie rezerwacji FU 3 letnich na 2x100TB w ten sposób odzyskamy z ogólnego rachunku     :  701,86$
 2. po 1-szym roku wykupienie 1 rezerwacji FU 1 rocznych na 100TB w ten sposób odzyskamy z ogólnego rachunku         :  140,00$
 3. po 2-óch latach wykupienie 5 rezerewacji FU 1 rocznych na 100TB w ten sposób odzyskamy z ogólnego rachunku       :  700,00$
 4. po trzech latach wykupienie 1 rezerwacji FU na 3 lata na 1PB w ten sposób odzyskamy z ogólnego rachunku          : 8740,65
 5. ró¿nica wynikaj¹ca 1PB i 1095TB (71TB) musi byc op³acona w trybie PAYG, 
    gdy¿ jest pod progiem op³acalnoœci wykupu rezerwacji (81TB)
 
Sumarycznie z rachunku PAYG odzyskujemy 10282,51$
koszt sk³adowania naszych danych zamyka siê w tym przypadku w kwocie 50 509,97$.

Ponadto zadanie wspomina o cyklicznym testowaniu backupów rocznych, które rozpoczyna siê po pierwszych dwóch latach.
Wynika z tego, ze w okresie 6 lat odbeda siê co najmniej 4 takie akcje.
Koszt jednej bedzie g³ównie kosztem sci¹gniecia 1TB z chmury Azure czyli 22,53$. 
Sumarycznie za wszystkie akcje wyjdzie 90,12$