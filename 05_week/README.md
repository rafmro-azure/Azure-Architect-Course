Zadanie domowe z Tygodnia 4 kursu AZ-303: Microsoft Azure Architect Technologies›TYDZIEŃ 4: Implement storage accounts

Zadania:
#TYDZIEŃ5.1 „Dla każdego typu Storage, którego się nauczyłeś w czasie kursu (min. 4 typy) dobierz dwa dobre i jedno złe zastosowanie. Chciałbym byś zweryfikował różne możliwości składowania danych w Azure i opowiedział, kiedy i do czego te możliwości możesz wykorzystać.”
#TYDZIEŃ5.2
„Wymień jeden dobry i jeden zły przykład wykorzystania StorSimple w swojej organizacji. Napisz, kiedy i w jakich scenariuszach się sprawdzi, a kiedy nie.”
#TYDZIEŃ5.3
„Liczymy Koszty :). Umówmy się. Twój system backupu (nie ma znaczenia jaki) składuje 1TB nowych danych każdego dnia. Wykorzystujesz oczywiście Azure do tej operacji i chcesz dane składować jak najtaniej. Przez dwa lata nie kasujesz zebranych danych. Po dwóch latach na próbę odtwarzasz dane z ostatniego dnia każdego roku. Po 3 roku kasujesz dane, zebrane w roku pierwszym.

 * Ile łącznie wygenerujesz kosztów w ramach tej usługi, jeśli rozważymy pełny, 6 letni okres jej działania.
 * Rozważ różne aspekty i różne możliwości usług i pokaż jako algorytm liczenia przyjąłeś.

#TYDZIEŃ5.1 - Odpowiedź do zadania : 
__________________
Reprezentacją usługi składowania danych w Azure (Azure Storage) jest Azure Storage Account. Zapewnia unikatową przestrzeń nazewniczą dla danych składowanych w jej obrębie by mogły być dostępne za pośrednictwem protokołu HTTP/S. 
W Storage Account mogą być reprezentowane wszystkie obiekty składowania danych w Azure tj. : 
                Blobs, Files, Queues, Tables oraz Disks.
Obecnie w Azure funkcjonują następujące typy Storage Accounts : 
                GPv2, GPv1, BlockBlobStorage, FileStorage oraz BlobStorage
Opcje niezawodnościowe dla Storage Accounts to obecnie:
                Locally redundant storage (LRS), Zone-redundant storage (ZRS), Geo-redundant storage (GRS) z Read-Access GRS (RA-GRS) 
                oraz Geo-zone-redundant storage (GZRS) z Read-Access GZRS (RA-GZRS).

                
Typy Storage Account        |Wspierane usługi                                        |Dostępne opcje niezawodnościowe       |Dostępne Opcje Wydajnościowe   | Poziomy dostępności
----------------------------|--------------------------------------------------------|--------------------------------------|-------------------------------|---------------------------
General-purpose V2          |Blob, File, Queue, Table, Disk i Data Lake Gen2         |LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS3 |Premium, Standard              |Hot, Cool, Archive
General-purpose V1          |Blob, File, Queue, Table i Disk                         |LRS, GRS, RA-GRS                      |Premium, Standard              |brak
BlockBlobStorage            |Blob (tylko block blobs i append blobs)                 |LRS, ZRS3                             |Premium                        |brak
FileStorage	                |File                                                    |LRS, ZRS3                             |Premium                        |brak
BlobStorage	                |Blob (tylko block blobs i append blobs)                 |LRS, GRS, RA-GRS                      |Standard                       |Hot, Cool, Archive
                
Jak łatwo się domyślic w zadaniu nie chodzi o podanie typów Storage Accounts a raczej chodzi podział Azure Storage na jego usługi tak jak to zostało
zrobione na stronie : https://azure.microsoft.com/en-us/services/storage/
Strona ta wymienia następujące produkty (usługi) Storage :
    - Blob storage
    - Azure Disk Storage
    - Azure Files
    - Azure Data Lake Storage
    - Azure Archive Storage
    - Azure HPC Cache

Przykłady dobrych i złych zastosowań poszczególnych 4 z 6 usług dostępnych w ramach Storage Account General Purpose w wersji 2giej (tu. Blob, File, Queue oraz Table) :
   1. Kontenery/Blobs:
        - Główne zastosowanie to : 
                a. przechowywanie plików multimedialnych (i udostępnianie przez CDN), 
                b. hosting statycznych stron www, 
                c. dyski VHD, 
                d. pliki backupów 
        - Niewłaściwe zastosowanie to : 
                a. hierarchiczne systemy plikowe z zagnieżdzonymi uprawnieniami jak ma to miejsce np. w korporacyjnych dyskach współdzielonych
                b. dynamiczne nadpisywane pliki logów (zwłaszcza przy włączonym wersjonowaniu)
                c. analityka dużych zbiorów dancyh (-> big data analitycs)
        Należy zwrócić uwagę iż, powyższe przykłady nie są jednak przykładami złego zastosowania Blobs Storage. Są tylko nieptymalnymi :) 
        Blobs jako usługa fundamentalna może zostac użyta praktycznie do każdego celu ;) 
        to tylko kwestia odpowiedniego wykorzystania SDK i metadanych ;)
                
    2. Azure Files
        - Główne zastosowanie to : 
                a. udostępnianie dysków współdzielonych dla systemów obsługujących SMB i NFS np. dyski sieciowe w Windows Serwer)
                b. wszędzie tam gdzie dostep realizowany przez API musi być obsługiwany szybko, łatwo i powtarzalnie np. poprzez skrypty Powershell, Az Cli, Perl, Go
        - Nie nadaje się do : 
                a. przetwarzania dużych wolumenów danych (ograniczenia do 5TB per udział) np. składowanie plików backupów 
                b. wysoko wydajne przetwarzanie danych (ogranczenie na IOPS 10k i transfery do 60MB/s) np. BigData
                c. trzymania danych, gdzie potrzeba jest hierarchicznego systemu uprawnien np. udziały NTFS 
                d. gdzie dane muszą być backupowane a wersje plików sledzone np. obiekty dokumentujące Compliance (nagrania, tracking, logi)

    3. Azure (Storage) Queue         
            W tym przypadku, należy raczej odpowiedzieć w jakich przypadkach stosować Azure Storage Queue a w jakich Azure Service Bus.
            I tak (z grubsza) Azure Storage Queue stosuje się do kolejek, które:
                a. mają zakładana pojemność wiekszą niż 80GB
                b. muszą śledzić przetwarzanie wiadomości 
                c. wymagane jest niezależne (od logowania workerów/konsumentów) logowanie transakcji przy przetwarzaniu wiadomości
            Jest to temat na tyle skomplikowany, że nawet nie pokuszę się o przykłady, gdzie mogło by mieć zastosowanie wyłącznie Azure Storage Queue
            (za https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)
            
    4. Azure Table
            Podobnie jak z Azure Storage Queue, o tym czy do konkretnego zastosowania można użyć Azure Storage Table czy też CosmosDB czy Azure SQL musi sobie odpowiedzieć
            sam architekt. Musi on jednak miec na względzie nastepujące ograniczenia:
                a. nie ma górnego ogranicznia na czas odpowiedź (CosmosDB ma do kilku ms)
                b. limit na długość wiersza (1MB)
                c. nie wspiera popularnych API NoSQL
                d. nie ma mozliwości nadawania uprawnien granularnych
                e. nie wspiera budowy aplikacji o zasiegu światowym 
                f. baza danych typu NoSQL
            i wiele innych. ..
            Azure Table jest to nic innego jak bardzo prosta baza danych NoSQL. Z wszystkimi jej wadami i zaletami :)


__________________
#TYDZIEŃ5.2 Odpowiedź do zadania
„Wymień jeden dobry i jeden zły przykład wykorzystania StorSimple w swojej organizacji. Napisz, kiedy i w jakich scenariuszach się sprawdzi, a kiedy nie.”

__________________
Azure StorSimple to dedykowane rozwiązanie, która ma za zadanie ułatwić zarządzanie danymi. Ze względu na to jak została skonstruowane można od razu zauważyć, iż nada się wsżedzie tam gdzie:
    - jest potrzeba skonsolidowania dużych zasobów różnych danych 
    - jest potrzeba funkcjonowania centralnych repozytoriów np. Backupów, nagrań monitoringu, zapisu logów, wytworzonych buildów, aktualizowanej dokumentacji etc
    - polityka bezpieczeństwa wskazuje że backupy powinny być off-sitowane poza siedzibe firmy (chmura)
    - gdzie plany DR przewidują prawie natychmiastowe odzyskiwanie danych w zapasowym centrum firmy
    - tranzycja do zasobów chmurowych musi obywać się etapowo i w długim okresie czasu
    - potrzebna jest niedroga macierz o ponadprzeciętnej pojemności :)
Trzeba jednak pamietać, iż mimo StorSimple jest również macierzą dyskową (lub może byc tak traktowana) to nie zaleca się jej korzystać w przypadku:
    - słabego lub przeciążonego łacza internetowego do chmury Azure
    - składowania plików o dużej rotacji zawartości jak np. dyski wirtualne, pliki DB etc.
    - szybkiej migracji do chmury Azure dużych wolumenów danych 

W naszej organizacji Azure StorSimple napewno sprawdził by się jako repozytorium kodu oraz buildów. Miejsca pod to zawsze brakuje do tego stopnia że trzeba ogranczać ilość buildów :)
Na pewno bym go nie uzył jako macirzy do HyperV lub ESXi. Tam dyski VHD muszą być dostępne cały czas i drobnymi rozmiarami raczej nie grzeszą :)

__________________
#TYDZIEŃ5.3 Odpowiedź:
„Liczymy Koszty :). Umówmy się. Twój system backupu (nie ma znaczenia jaki) składuje 1TB nowych danych każdego dnia. Wykorzystujesz oczywiście Azure do tej operacji i chcesz dane składować jak najtaniej. Przez dwa lata nie kasujesz zebranych danych. Po dwóch latach na próbę odtwarzasz dane z ostatniego dnia każdego roku. Po 3 roku kasujesz dane, zebrane w roku pierwszym.

 * Ile łącznie wygenerujesz kosztów w ramach tej usługi, jeśli rozważymy pełny, 6 letni okres jej działania.
 * Rozważ różne aspekty i różne możliwości usług i pokaż jako algorytm liczenia przyjąłeś.

__________________

Polityka backupu:
- backup każdego dnia (+1TB)
- odtwarzanie każdego roku backupu rocznego, pierwszy raz po dwóch latach
- usuwanie backupów starszych niż 1095 dni (3 lata) - (zakładam, ze w tym przypadku chodziło o polise tranzycji danych niz samodzielne usuwanie duzych zbiorów danych)

Wyliczenia w scenariuszu PAYG (Pay-As-You-Go):
  Przyjeto :
  
            rejon               : North Europe (obecnie najtańszy w Europie)
            usługa              : Blob Stroage
            poziom dostępności  : Archive
            1TB                 : 1024GB

obliczenia dla taryfikatora według płatności PAYG:

    CX - "srednia" cena z cennika za 1GB w wybranej usłudze składowania przez jeden miesiac dla wybranego regionu oraz typu usługi. 
        Uwaga: Za "sredni" kalkulator kosztów przyjmuje okres 30 dni.
        CX = 0,00099$
    DXG - koszt przechowywania 1GB przez 1 dzień (CX/30)
        DXG = 0,000033$
    DXT - koszt przechowywania 1TB przez 1 dzień (1024xDXG)
        DXT = 0,033792$
    TXY - koszt przechowywania 1TB danych przez rok (365xDXT). 
        TXY = 12,3341$
    RX1 - koszt przechowywania 100TB danych przez 1 rok (100xTXY)
        RX1 = 1233,408$
    RXY - koszt przechowywania 365TB danych przez 1 rok (365xTXY)
        RXY = 4501,9465$
    AX1 - koszt gromadzenia 100TB danych (po 1TB przez 100 dni) + koszt ich przechowywania w danym roku. 
            Koszt został policzony wg wzoru na sumę ciągu (kazdy dodany 1TB jest skladowany o dzien krocej od poprzednika):
        AX1 = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(265/365)
        AX1 = 1066,14$
    AXY - koszt gromadzenia 365TB danych (po 1TB przez 365 dni) + koszt ich przechowywania w danym roku. 
            Koszt został policzony wg wzoru na sumę ciągu (kazdy nastepny 1TB jest skladowany o dzien krocej w roku):
        AXY = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(1/365)
        AXY = 2257,14$
    GX - koszt gromadzenia i składowania 1095TB danych przez trzy pierwsze lata. 
        GX = AXY + (AXY+RXY) + (AXY+2xRXY) = 3xAXY + 3xRXY = 6769,17$ + 13505,79$
        GX = 20274,96$

    HXY - koszt składowania 1095TB danych przez rok (1095xTXY)
        HXY = 13505,84$
    HX3 - koszt składowania 1095TB (3x365) danych przez 3 lata (3xHXY)
        HX3 = 40517,52$
    WX - koszt całkowity gromadzenia i składowania 1095TB danych przez 6lat
        WX = HX3 + GX = 60792,48$
    
Całkowity koszt składowania założonej ilości danych zamyka się w kwocie 60792,48$. 

  Aby uzyskać oszczedności z tej kwoty należy przeprowadzić symulacje kosztów w przedzialach czasowych oraz estymowanych kosztach rezerwacji.
    Dodatkowo aby miec porónanie do rezerwacji na 1PB należało oblyiczyć koszt gromadzenie 1PB danych.
    GX1 - koszt gromadzenia i składowania 1PB danych w ciagu pierwszych trzech lat.  
        AX4 = TXY + TXY(364/365) + TXY(363/365) + ........ + TXY(71/365) = 2173,17$  (gdyż 1095-1024=71)
        GX1 = AXY + (AXY+RXY) + (AX4+2xRXY) = 2xAXY + 3xRXY + AX4
        GX1 = 4514,28$ + 13505,8395$ + 2173,17$
        GX1 = 20193,29$

  róznica miedzy zebraniem 1PB a 1095TB (GX-GX1) wynosi 81,67$

Rodzaj wolumenu danych                  |PAYG 1-year    | 1-year FU reservation |ROŻNICA
----------------------------------------|---------------|-----------------------|----------
koszt gromadzenia 100TB                 | 1066,14$      | 1083,00$              |  -16,86$
Koszt składowania 100TB                 | 1223,33$      | 1083,00$              |  140,00$
Koszt gromadzenia 200TB                 | 1794,36$      | 2166,00$              | -371,64$
Koszt gromadzenia 300TB                 | 2186,85$      | 3249,00$              |-1062,15$
Koszt gromadzenia 365TB                 | 2257,14$      | 4332,00$              |-2074,86$
koszt gromadzenia 1PB                   |N/A            |10589,00$              |N/A
Koszt składowania 1PB                   |12630,09$      |10589,00$              | 2041,09$


Rodzaj wolumenu danych                  |PAYG 3-year    | 3-year FU reservation |ROŻNICA
----------------------------------------|---------------|-----------------------|---------
koszt gromadzenia (pierwszych) 100TB    | 3512,80$      | 2993,00$              |  519,80$
koszt gromadzenia (pierwszych) 200TB    | 6687,68$      | 5986,00$              |  701,86$
koszt gromadzenia (pierwszych) 300TB    | 9526,83$      | 8979,00$              |  547,83$
koszt gromadzenia (pierwszych) 400TB    |11692,71$      |11972,00$              | -279,28$ 
koszt gromadzenia 1PB                   |20193,29$      |29150,00$              |-8956,71$
Koszt składowania 100TB                 | 3670,23$      | 2993,00$              |  677,33$
Koszt składowania 200TB                 | 7340,46$      | 5986,00$              | 1354,46$
Koszt składowania 300TB                 |11010,69$      | 8979,00$              | 2031,69$
Koszt składowania 1PB                   |37890,65$      |29150,00$              | 8740,65$

  koszt skladowania 1095TB w PAYG przez 71 dni                    : 2627,16$
  koszt składowania 71TB w PAYG przez 3 lata                      : 2605,69$
  granica oplacalnosci zakladania rezerwacji FU na 100TB na 1 rok :     88TB
  granica oplacalnosci zakladania rezerwacji FU na 100TB na 3lata :     81TB

  Jak widać najwieksze róznice uzyskujemy przy rezerwacjach trzyletnich. Natomiast, w przypadku pierwszych trzech lat sensowne wydaje sie być 
  zalożenie rezerwacji trzyletnich na pierwsze 200TB danych i nastepnie na pojawiające się pełne 100TB (pełne w sensie roku kalendarzowego) zakładanie 
  rezerwacji 1-dno rocznych. 
  
Moja propozycja planu wykupu rezerwacji:
 1. przed rozpoczęciem, wykupienie rezerwacji FU 3 letnich na 2x100TB w ten sposób odzyskamy z ogólnego rachunku     :  701,86$
 2. po 1-szym roku wykupienie 1 rezerwacji FU 1 rocznych na 100TB w ten sposób odzyskamy z ogólnego rachunku         :  140,00$
 3. po 2-óch latach wykupienie 5 rezerewacji FU 1 rocznych na 100TB w ten sposób odzyskamy z ogólnego rachunku       :  700,00$
 4. po trzech latach wykupienie 1 rezerwacji FU na 3 lata na 1PB w ten sposób odzyskamy z ogólnego rachunku          : 8740,65
 5. różnica wynikająca 1PB i 1095TB (71TB) musi byc opłacona w trybie PAYG, 
    gdyż jest pod progiem opłacalności wykupu rezerwacji (81TB)
 
Sumarycznie z rachunku PAYG odzyskujemy 10282,51$

  koszt składowania naszych danych zamyka się w tym przypadku w kwocie 50 509,97$.

Ponadto zadanie wspomina o cyklicznym testowaniu backupów rocznych, które rozpoczyna się po pierwszych dwóch latach.
  Wynika z tego, ze w okresie 6 lat odbeda się co najmniej 4 takie akcje.
  Koszt jednej bedzie głównie kosztem sciągniecia 1TB z chmury Azure czyli 22,53$. 
  Sumarycznie za wszystkie akcje testowania backupu wyjdzie 90,12$
