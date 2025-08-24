# Szkolenie ILUM - Dzień 4: DevOps na ILUM - utrzymanie platformy, CI/CD, monitoring

# 🕘 09:00 - 11:00 | Zarządzanie platformą ILUM (perspektywa DevOps)

---

## **🟢 Cele bloku:**

* Zrozumienie architektury wdrożenia ILUM z perspektywy administracyjnej i DevOps
* Poznanie konfiguracji Helm chartu ILUM oraz możliwości dostosowania instalacji
* Praktyczne ćwiczenie modyfikacji konfiguracji ILUM i aktualizacji platformy
* Omówienie zarządzania wieloma klastrami
---

## **🎤 Narracja:**

### **[Slajd 1: „Powitanie - Dzień DevOps"]**

**Dzień dobry wszystkim!**

Bardzo się cieszę, że widzę Was na czwartym dniu naszego kursu ILUM. Mam nadzieję, że wszyscy są gotowi, bo czeka nas bardzo intensywny i praktyczny dzień.
Dzisiaj zmieniamy perspektywę w sposób dosyć radykalny. Przez ostatnie trzy dni koncentrowaliśmy się na tym, jak korzystać z ILUM - jak uruchamiać zadania, pisać kod Spark, budować pipeline'y danych, korzstać z zintegrowanych modułów. Dzisiaj spojrzymy na ILUM oczami osób, które są odpowiedzialne za to, żeby ta platforma działała sprawnie, bezpiecznie i niezawodnie.

Jeśli w poprzednich dniach odpowiadaliśmy na pytanie "jak korzystać z ILUM", to dziś odpowiemy na pytanie "jak utrzymać ILUM w ruchu". To kluczowy dzień dla osób odpowiedzialnych za infrastrukturę, ale także dla wszystkich, którzy chcą zrozumieć, co dzieje się "pod maską" platformy. Ale nie martwcie się jeśli nawet się tym nie zajmujecie, bo pozostali na pewno również znajdą coś dla siebie. 

W grupie z tego co wiemy znajdują się osoby z różnym doświadczeniem, co jest idealne. Ci z Was, którzy mają doświadczenie DevOps, będą mogli dzielić się swoimi spostrzeżeniami, a ci, którzy dopiero poznają ten świat, zobaczą, jak wygląda zarządzanie platformą danych.

### **[Slajd 2: „Plan dnia"]**

Zanim zagłębimy się w szczegóły, omówmy plan dzisiejszego dnia. Przygotowaliśmy dla Was bardzo praktyczny program, który pozwoli nie tylko zrozumieć teorię, ale przede wszystkim nauczyć się konkretnych umiejętności.

1. Zaczniemy od zarządzania platformą ILUM z perspektywy DevOps. Poznamy architekturę wdrożenia, nauczymy się modyfikować konfigurację Helm chartu i zobaczymy, jak zarządzać wieloma klastrami jednocześnie.

2. Po pierwszej przerwie, około wpół do dwunastej, przejdziemy do tematu CI/CD dla aplikacji Sparkowych. Zobaczymy, jak zautomatyzować wdrażanie aplikacji Spark i jak zintegrować ILUM z popularnymi narzędziami CI/CD. Zbudujemy razem kompletny pipeline od kodu do produkcji.

3. Po przerwie obiadowej skupimy się na monitoringu klastra i aplikacji. To bardzo ważny temat - nauczymy się konfigurować Prometheus, Grafana, systemy logowania i alertingu. Bez dobrego monitoringu nie ma mowy o niezawodnej platformie produkcyjnej.

4. Następnie omówimy najlepsze praktyki DevOps specyficzne dla platform danych. Podejście Infrastructure as Code, strategie backup, bezpieczeństwo, optymalizacja kosztów - wszystko to, co potrzebujecie, żeby profesjonalnie utrzymać ILUM.

Postaramy się dzisiaj przedstawić dużo praktyki. Będziemy modyfikować konfiguracje, testować aktualizacje, budować pipeline'y automatyzacji. Także przygotujcie się na intensywny, ale miejsmy nadzieję, wartościowy dla was dzień.

🎯 **Czy macie jakieś pytania do planu dnia, zanim przejdziemy do pierwszego tematu?**

---

### **[Slajd 3: „Architektura ILUM z perspektywy DevOps"]**

Dobrze, zaczynamy pierwszą sesję techniczną. Zanim zagłębimy się w szczegóły zarządzania, musimy przypomnieć sobie architekturę ILUM, ale tym razem spojrzymy na nią z zupełnie innej perspektywy - perspektywy osoby odpowiedzialnej za utrzymanie tej platformy.
Kiedy patrzymy na ILUM jako DevOps, nie interesuje nas tylko to, jak uruchomić zadanie Spark. Interesuje nas to, jak zapewnić, żeby wszystkie komponenty działały sprawnie, jak je monitorować, jak je aktualizować, jak zapewnić wysoką dostępność.
Przypomnijmy sobie kluczowe komponenty. 
  * ilum-core - to główny silnik platformy, który wystawia REST API i zarządza zadaniami Spark.
  * ilum-ui - interfejs webowy dla użytkowników końcowych. To może wydawać się mniej krytyczne, ale dla użytkowników to często jedyny sposób interakcji z platformą.
  * ilum-livy-proxy - nasza implementacja RESTowego Api aplikacji Apache Livy, którego funkcjonalności są w pełni pokryte przez ILUMa, dzięki czemu można korzystać z narzędzi z Livym zintegrowanych, konieczna do pracy z niektórymi modułami jak jupyter, czy airflow w pewnej konfiguracji, czy innych aplikacji, które właśnie obsługują komunikacje z Apache Livy'm
  * ilum-sql - moduł obsługujące w ILUMie pracę z SQL edytorem, dzięki niemu możemy uruchamiać na naszych danych zapytania SQLowe w Sparku, czy trino
  * ilum-marquez - open-sourceowy tool do obsługi i zbierania dobrze nam znanego data lineagu

  * MongoDB - bazę metadanych, gdzie przechowywane są definicje zadań, historie wykonań, konfiguracje. To absolutnie krytyczny komponent - bez niego ILUM nie może działać. 
  * Apache Kafka - system komunikacji, opcjonalny, ale zalecany w środowiskach produkcyjnych, konieczny do uzyskania HA ilum-core. 
  * MinIO - magazyn obiektowy zgodny z S3, domyślny storage dla ILUM. 
  * Postgresql - baza danych używana przez niektóre z modułów zintegrowanych z ILUMem takich jak, Hive, Marquez, Airflow, więc często przechowuje ona kluczowe dla waszych procesów metadane, jak i dane same w sobie

  * KPS - stack monitorujący zaiwerający w defaultowej konfiguracji prometheusa oraz Grafane, aby out-of-the-box zapewnić ILUMowi bez konieczności wdrażania czegoś "na zewnątrz" możliwość monitorowania aplikacji
  * Loki & Promtail - zalecany w produkcyjnych wdrożeniach agregatory logów, promtail - je zbiera i wysyła do Lokiego, który je przechowuje i pozwala przeglądać

  * Wszystkie zintegorwane z ILUMem moduły, takie jak jupyter, airflow, kestra i wszystkie te które są opisane u ans w doksach, w kolejnym kroku pokaże wam jak tymi modułami możemy manipulować, włączyć lub je wyłączać

Każdy z tych wyżej wymienionych komponentów jest elementem chartu aplikacji ILUM oraz jendocześnie samodzielnym, możliwym do oddzielnego zdeployowania chartem, jest to kluczowe do zrozumienia tego jak zorganizowana jest nasza aplikacja. Oznacza to, że Chart zbiorczy ILUM_AIO, którego podczas tego kursu zazwyczaj prezentujemy i którego używamy jest jedynie dodatkowo opakowanym zbiorem tych wszystkich wyżej wymienionych chartów. 

Z perspektywy DevOps każdy z tych komponentów wymaga:

Po pierwsze - odpowiedniej konfiguracji zasobów. Ile CPU, ile RAM, ile storage zależnie od naszych potrzeb. 
Po drugie - monitoringu stanu i wydajności. 
Musimy wiedzieć, czy wszystko działa prawidłowo. 
Po trzecie - strategii backup i disaster recovery. Co robimy, gdy coś się zepsuje? 
Po czwarte - zarządzania aktualizacjami i migracjami. Jak bezpiecznie aktualizować platformę?

🎯 **Niektóryz z Was prawdopodobnie mieli już do czynienia z zarządzaniem podobnymi systemami? Wiąże się to zwykle z wieloma wyzwaniami, jak wyestymować zużycie zasobów dla poszczególnych komponentów, jakiego środowiska użyć, na te pytania nie jest niestety łatwo odpowiedzieć, szczególnie nie posiadając wiedzy domenowej na temat projektów jakie planuje realizować klient, jednak dzisiaj postaramy się podzielić z wami dobrymi praktykami, które nawet jeśli nie rozwiąża wszystkich tych problemów to może pozwolą wam na wdrożenie ILUMa i nie tylko w taki sposób, aby można tym wdrożeniem łatwo manipulować i dostosować do zmieniających się warunków**


### **[Slajd 4: „Helm Chart ILUM - konfiguracja, `helm install`"]**


Teraz przejdźmy do konkretów. ILUM jest dystrybuowany jako Helm Chart, co znacznie ułatwia zarządzanie konfiguracją. Dla tych z Was, którzy nie są jeszcze bardzo zaznajomieni z Helm, czym tak właściwie on jest? - Helm to menedżer pakietów dla Kubernetesa, który upraszcza wdrażanie, zarządzanie i aktualizowanie aplikacji w klastrach. Działa jak "apt" lub "yum" dla Kubernetesa, umożliwiając definiowanie, instalowanie i skalowanie aplikacji za pomocą tzw. chartów - prekonfigurowanych szablonów. Helm pozwala na łatwe zarządzanie zależnościami, wersjonowaniem i konfiguracją, co przyspiesza i automatyzuje procesy DevOps.
Wszystkie resourcy k8sowe danej aplikacji są w takim charcie zgromadzone i wstępnie skonfigurowane jednocześnie dając możliwośc szybkiej zmiany tej konfiguracji za pośrednictwem pliku konfiguracyjnego - values.yaml - główny plik z domyślnymi wartościami, może być podmieniony na nasz własny, wartości z naszego pliku nadpisują te domyślne.

*Przechodzimy na [artifacthub](https://artifacthub.io/) i na przkładzie ILUMa pokazujemy jak go znaleźć przechodzimy kilka kroków po valuesach, tłumacząc czym są, gdzie znaleźć ich dokumentacje, jak znaleźć zbundlowane charty, pokazujemy jak się w tym odnaleźć*

*Przechodzimy do dokumentacji, pokazujemy gdzie szukać wskazówek o tym jakich valuesów użyć w zależnosci od casea, najpierw https://ilum.cloud/resources/getting-started , potem https://ilum.cloud/docs/production/#pre-configured-stack-examples , przechodzimy przez ten plik tłumacząc co jest, wstępnie opisując jak można zdeployować ILUMa bardziej produkcyjnie, jakie są tego zalety i takie tam.*

To, co jest tutaj kluczowe, to zrozumienie, że każda zmiana w tych plikach może wpłynąć na działanie całej platformy. Dlatego zawsze robimy zmiany stopniowo, testujemy je najpierw na środowisku testowym, i mamy plan rollback na wypadek problemów, o tym jak można soie z tymi problemami poradzić będzie troche więcej powiedziane pod koniec dnia w sekcji dobrych praktyk.

*Instaluje ILUMA pokazując terminal na minikube*

### **[Slajd 5: „`helm upgrade` Modyfikacja konfiguracji ILUM"]**

## **💻 Demo: Modyfikacja konfiguracji ILUM**

Dobrze, czas na kolejną praktyczną demonstrację. Pokażę Wam teraz, jak w praktyce modyfikuje się konfigurację działającej instancji ILUM. To typowe zadanie DevOps - na przykład podbicie wersji, zwiększenie zasobów/wyskalowanie dla ilum-core, gdy widzimy, że platforma jest przeciążona, czy włączenie lub wyłączenie niektórych funkcjonalności lub modułów.

*Robimy demo upgradeu, tworzenie pliku, wywołanie komendy, analiza czy działa, zalogowanie się na stworzonego usera*

### **[Slajd 6: „Upgrade - szczegóły"]**

Jeszcze jedną rzeczą, o której warto wspomnieć w kwesti upgradeu ILUMa, a mianowicie regularnie publikowane przez nas upgrade notesy. 
*Przechodzimy na strone w [doksach](https://ilum.cloud/docs/upgrade-notes/)*
Wyjaśniamy, czym te upgrade notesy są, jak się w nich odnaleźć.

🎯 **Czy macie pytania do tego procesu? Czy coś było niejasne?**

### **[Slajd 7: „Zarządzanie wieloma klastrami"]**

Przejdźmy teraz do bardzo interesującej, wydaje mi się że szczególnie dla was funkcjonalności ILUM - zarządzania wieloma klastrami Kubernetes z jednej instancji. To jedna z mocnych stron ILUM i coś, co jest szczególnie przydatne w właśnie większych zespołach.
Wyobraźcie sobie sytuację - macie klaster deweloperski, testowy, staging i produkcyjny. Albo macie klastry w różnych regionach geograficznych. Albo różne zespoły potrzebują izolowanych środowisk. A może jakieś środowisko powinno być w szczególny sposób skonfigurowane? Tradycyjnie musielibyście zarządzać każdym klastrem osobno.
ILUM pozwala zarządzać wszystkimi tymi klastrami z jednego miejsca, uwzględniając też to, że wspiera nie tylko k8s, ale również Yarna oraz używany praktycznie jedynie w testach klaster lokalny. Możecie uruchamiać zadania na różnych klastrach, monitorować je wszystkie z jednego interfejsu, zarządzać użytkownikami i uprawnieniami centralnie.

Z perspektywy DevOps to oznacza, że zamiast zarządzać wieloma instancjami ILUM, zarządzacie jedną instancją, która kontroluje wiele klastrów. To znacznie upraszcza administrację, monitoring, zarządzanie użytkownikami.
Oczywiście, są też wyzwania, które za tym idą. Musimy zapewnić bezpieczną komunikację między klastrami, zarządzać certyfikatami, monitorować stan wszystkich klastrów, wystawiać pewne serwisy, takie jak np. kafka, czy minio jeśli chcemy z niego korzystać, na zewnątrz naszego klastra co często jest albo trudne albo z przyczyn bezpieczeństwa po prostu niemożliwe. Jednak myślę, że korzyści częściej zdecydowanie przeważają nad wyzwaniami.

Scenariusze użycia oczywiście są różne. Możecie mieć osobne klastry dla różnych środowisk - development, test, production. Możecie mieć klastry dedykowane różnym podzespołom, czy tez w celu izolacji zasobów między różnymi projektami.

Bardzo ciekawym scenariuszem, wydaje mi się że szczególnie przydatnym dla was jest też swego rodzaju fałszywe zarządzanie wieloma klastrami, dobrze wiedzieć, że w ILUMie dla klastra typu kubernetes można ustalać **Resource Quoty** i **Limit Range**, czyli w skrócie po prostu limitować zasoby dla danego klastra, a faktycznie dla k8sowego namespacea, z którego on korzysta. Na co nam to pozwala, ponieważ klaster w ILUMie jest jedynie swego rodzaju abstrakcją, dla pojedynczego realnego k8sa możemy w ILUMie stworzyc wiele instancji tych naszych klastrów, każdemu z nich przydzielić inny namespace i nałożyć na niego zasoby, chciałbym wam teraz to przedstawić w praktyce i pokazać na co nam coś takiego pozwala. 

## **💻 Demo: Zarządzanie wieloma klastrami z poziomu minikubea, na którym przed chwilą zainstalowaliśmy ILUMa**

Teraz po krótce postaram wam się przybliżyć ten proces najpierw prezentując gdzie możemy te klastry dodawać, a potem pokaże wam jak w praktyce może wyglądać zrealizowanie tego casea, o którym wspominałem.
Wspominamy o default klastrze, tym że teoretycznie pozwala on się komunikować z maszyną, na której stoi ILUM i tym, że aby na tym samym k8sie stworzyć nowy klaster wystarczy go sklonować.

*Prezentujemy proces, najpierw formularz dodania klastra, a następnie tworząc dwa nowe klastry z różnymi limitami i przentujemy jak to działa w praktyce*

---

### **[Slajd 8, Przerwa: „Podsumowanie bloku"]**
 
# ⏰ 11:00 - 11:30 | Przerwa kawowa

**Podsumwoanie bloku**

Co omówiliśmy:

✅ Architekturę ILUM z perspektywy DevOps  
✅ Konfigurację i modyfikację Helm chartu  
✅ Zarządzanie wieloma klastrami 
✅ Praktyczny przykład wykorzystania zarządzania wieloma klastrami 

Kluczowe wnioski:
- ILUM jest wysoce konfigurowalny przez Helm
- Obsługuje złożone scenariusze multi-cluster
- Wymaga przemyślanej architektury dla środowisk prod
- Większość problemów i obserwacji można wykonać przez kubectl i logi

🎯 **Pytania przed przerwą?**

---

### **[Slajd 9: „CI/CD w świecie Big Data"]**

# 🕥 11:30 - 13:00 | CI/CD dla aplikacji Spark z wykorzystaniem ILUM

---

## **🟢 Cele bloku:**

* Zrozumienie roli ILUM w procesach CI/CD dla aplikacji Spark
* Poznanie różnych strategii wdrażania aplikacji Spark z ILUM
* Praktyczne zbudowanie pipeline'u CI/CD wykorzystującego REST API ILUM
* Integracja z poprzed narzędzia CI/CD na przykładzie GitLab CI/CD
* Automatyzacja testowania i wdrażania aplikacji Spark

---

Witamy z powrotem po przerwie! Mam nadzieję, że trochę odpoczęliscie i jesteście gotowi na kolejną porcję wiedzy.

W tym bloku skupimy się na jednym z najważniejszych aspektów nowoczesnego DevOps - Continuous Integration(ciągła integracja) i Continuous Delivery(ciagłe wdrażanie) w kontekście aplikacji Spark i platformy ILUM.

**Zacznijmy od podstawowego pytania - co to jest tak właściwie CI/CD i dlaczego jest ważne dla aplikacji Spark?**

Krótka definicja - CI/CD to zestaw praktyk w wytwarzaniu oprogramowania, które automatyzują i usprawniają procesy budowania, testowania i wdrażania aplikacji. CI (Continuous Integration): Programiści regularnie integrują swój kod do wspólnego repozytorium, gdzie automatyczne testy weryfikują poprawność zmian. Celem jest szybkie wykrywanie błędów i zapewnienie stabilności kodu.
CD (Continuous Deployment/Delivery): Automatyczne wdrażanie zatwierdzonych zmian na środowiska testowe lub produkcyjne.
CI/CD przyspiesza rozwój oprogramowania, zwiększa jakość kodu i umożliwia częste, niezawodne aktualizacje aplikacji.

Na wstępie, zanim przejdziemy dalej, może jesteście w stanie podzielić się z nami, czy w waszych procesach CI/CD jest obecne, jeśli tak, jak to wygląda w Waszej organizacji?

A jak to się ma do samego Sparka. Aplikacje Spark, podobnie jak każdy inny kod, wymagają systematycznego podejścia:
  * Po pierwsze - testowania. Zarówno testy jednostkowe kodu, jak i testy integracyjne na prawdziwych danych. Ile razy zdarzyło się Wam, że aplikacja działała lokalnie, ale na produkcji się wysypała?
  * Po drugie - wersjonowania. Musimy śledzić zmiany i mieć możliwość szybkiego rollback, gdy coś pójdzie nie tak.
  * Po trzecie - automatyzacji. Eliminujemy ręczne błędy przy wdrożeniach. Człowiek ma prawo zapomnieć o jakimś parametrze, źle skopiować plik, automat raaaczej nie.
  * Po czwarte - spójności. Identyczne procesy na różnych środowiskach. To, co działa na dev, musi działać na prod.


Jakie są wyzwania tradycyjnego podejścia do wdrażania Spark?
  * Ręczne budowanie JAR-ów lub wheel packages. Skomplikowane komendy spark-submit z dziesiątkami parametrów - kto z Was pamięta wszystkie te --conf spark.kubernetes.coś.tam?
  * Trudność w testowaniu na środowiskach podobnych do produkcji. I brak centralnego miejsca do monitorowania wdrożeń - każdy zespół robi to po swojemu.

I tutaj wchodzi ILUM wraz ze świadomym podejściem i wykorzystaniem CI/CD właśnie z rozwiązaniem tych problemów.

W kwestii CI/CD kluczowe jest w ILUMie REST API, które umożliwia programowe zarządzanie zadaniami. Mamy dzięki niemu dostep do zarządzania platformą w przystępnej i wszystkich dobrze znanej formie, o API na kursie już troche rozmawialiśmy więc nie będę się teraz na tym skupiać, jeśli macie co do niego jakiekolwiek wątpliwości zawsze można odnosić się do dokumemtacji.

Za moment zobaczymy sobie jak to wygląda w praktyce oraz zrealizujemy sobie jedno małe zadanko.

### **[Slajd 10: „Tradycyjne vs ILUM-based CI/CD"]**

CI/CD dla aplikacji sparkowych oczywiście tez można realizować bez ILUMa, jednak ten bardzo nam to może ułatwić co postaram się wam pokazać
Mamy tu małe porównanie - jak wygląda tradycyjny pipeline Spark na tle pipeline z użyciem ILUM.

**Po lewej stronie mamy tradycyjne podejście** 
Spójrzcie na ten deploy stage - spark-submit z master k8s, deploy-mode cluster, nazwa aplikacji, klasa główna, konfiguracja executor-ów, obraz kontenerowy, namespace...**
I to jest tylko początek! W prawdziwym środowisku produkcyjnym mamy często 20, 30, czasem 50 parametrów. Konfiguracja sieci, storage, security, monitoring, resource quotas... i to wszystko dla każdego joba z osobna.
Nie wspominając o wstępnych potrzebach środowiska, na którym taki pipeline jest uruchamiany, na dzień dobry musimy mieć odpowiednią wersje javy, rozpakowane artefakty samego sparka i ewentualne doinstalowane jary do komunikacji, np. z interfejsem S3, czy GCSa, czegokolwiek musimy uzyć, jednym słowem koszmar i pole do popisu dla błędów.  

**A teraz spójrzcie na pipeline z ILUM**
Deploy stage to jedno proste wywołanie curl'a . Cała konfiguracja jest przechowywana i konfigurowana z poziomu samego ILUMa, nie potrzebujemy, ani javy, ani sparka, dodatkowych jarów, ani nieskonczonej liczby parametrów konfiguracyjnych.

Widzicie różnicę? Zamiast zapamiętywać dziesiątki parametrów spark-submit, mamy czytelny, prosty sposób na osiągnięcie podobnego celu.

**Korzyści z tego podejścia są oczywiste. Prostota - mniej konfiguracji, mniej błędów. Spójność - ta sama konfiguracja na wszystkich środowiskach. Monitoring - centralne miejsce do śledzenia wszystkich wdrożeń. I łatwy rollback przez UI lub API.**

🎯 **Jak u was wygląda wdrażanie aplikacji Sparkowych?**


### **[Slajd 11: "CI/CD dla aplikacji Sparkowej przy użyciu Gitlab CI/CD i REST API ILUMa"]**

## **💻 Demo: Budowa pipeline'u CI/CD z ILUM**


Przejdziemy teraz sobie do przykładu, a nastepnie otrzymacie zadanie do wykonania.
Na czym się w tym zadaniu skupimy. Mamy na celu stworzyć gitowe repo przechowujące kod źródłowy implementacji interfejsu grupy ILUMowej. Chcemy, aby każda zmiana kodu zmergeowana do gałęzi głównej repozytorium prowokowała stworzenie lub aktualizacje grupy ILUMowej, która będzie zasilona tym kodem jaki znajdować się będzie w naszym repozytorium

*Przechodzimy do prezentacji procesu stworzenia przykłądowego pipelineu*


### **[Slajd 12: „💻 Zadanie - Stwórzmy własny pipeline CI/CD"]**

Teraz przedziemy sobie do jedynego zadanka jakie dzisiaj dla was przygotowaliśmy, spróbujecie zrobić coś podobnego, może w nieco prostszej formie do tego co było chwile temu przedstawione 

*Do zrobienia będzie*
  *Stworzenie repo z dwoma plikami, jeden to definicja pipelineu, drugi to kod prostego pythonowego single joba*
  *Pipeline uruchamia kod na ILUMie po każdym mergeu do mastera*
  *W skrypcie PySparkowym prosty dowolny kawałek kodu*
  *Gotowe przykłady będa mieli na mailu*

🎯 **Z 20/30 minut na realizacje/pytania/pomoc - informacje dla userów w HandOucie**


### **[Slajd 13: „Dobre praktyki CI/CD dla plikacji Sparkowych i nie tylko"]**

Na koniec tego bloku chciałbym podzielić się z Wami najważniejszymi dobrymi praktykami CI/CD dla aplikacji Spark, ale są one wydaje mi się też uniwerslane w innych przypadkach.

**Po pierwsze - separacja środowisk** 
  Nigdy nie testujcie na produkcji! Środowisko dev ma minimalne zasoby - wystarczy do sprawdzenia, czy aplikacja się uruchamia. Produkcja ma pełne zasoby.
  Przy developowaniu takiego pipelineu co bardzo dobrze wiem z własnego doświadczenia istnieje bardzo niewielka szansa na to, że wszystko od razu wykona się poprawnie, praktycznie zawsze o czymś zapomnimy, dlatego na pewno nie chcemy naruszać środowiska produkcyjnego, anie tym bardziej produkcyjnych danych, które w efekcie mogłyby zostać blędnie przetowrzone, lub co gorsza zmienione. po prostu popsute.

**Po drugie - wersjonowanie artefaktów. Każda wersja musi być jednoznacznie identyfikowalna:**

  Często w takich pipelinach budujemy jakieś artefakty i pushujemy je, czy to na storage, czy sa to na przykład obrazy dockerowe lub własnie uruchamiamy jakieś procesy tak jak robiliśmmy to chwile temu
  Jeśli jest tag Git - możemy go użyć. Jeśli nie - commit SHA, czy jakikolwikem inny czytelny identyfikator jaki sobie wymyślicie. Zawsze wiemy, która wersja kodu jest gdzie wdrożona, jaki artefakt został stworzony z jakiej bazy kodu, pomaga to później w kwestiach ewentualnego rollbacka, debugowania, czy po prostu ulepsza organizacje pracy.
   
**Po trzecie - testowanie przed wdrożeniem. To oczywiste, ale warto podkreślić:**
  Troche wiąże się to z separacją środowisk, warto zawsze taki pipeline podpiąć do jakiejś próbki danych lub jeśli dotyczy on, np pushowania obrazów podpiąć oddzielne prywaten repozytorium obrazów, aby nie wprowadzac bałaganu w tym docelowym. Gwarantuje wam, że taki piepline często trzeba tworzyć godzinami i rozwiązywać bład po błedzie, dlatego dobre przygotowanie się do wyczerpującego testowania go jest kluczowe i może nam oszczędzić problemów w przyszłości, możemy yniknąć sytuacji, w której taki piepline ingerujący już w środowisko produkcyjne zrobi za nas coś niepsodziewanego :) Pamiętajmy, że aby uniknąć zaskoczeń środowisko testowe powinno być jak najbardziej podobne do produkcji.

**Po czwarte - strategia rollback. Coś pójdzie nie tak, trzeba szybko wrócić:**

  Zawsze ostatecznie coś może pójść nie tak, dlatego powinniśmy zawsze mieć przygotowaną strategie rollbacku, która cofnie wprowadzone przez nas zmiany i pozwoli jak najszybciej pozbyć się błedu, czy to usunięcie wadliwego artefaktu i upload poprzedniego, to już zależy od charaktersytki danego pipelineu.
  Jedna komenda powinna nam wystarczyć na powrót do poprzedniej wersji. Dzięki wersjonowaniu artefaktów nie będzie to trudne zadanie, także jak widzimy, te dobre praktyki się ze sobą zazębiają.

**Po piąte - jak zwykle, monitoring wdrożeń. Nie wystarczy wdrożyć, trzeba sprawdzić, czy działa:**

  Takie mechanizmy jak automatyczne sprawdzanie statusu, alerty w przypadku problemów. Nie polegajcie na tym, że ktoś ręcznie sprawdzi, to zawsze może zawieźć.
  Zawsze warto taki pipeline przygotowac komlpeksowo pamiętając o tych wskazówkach.

**Te praktyki mogą wydawać się oczywiste, ale w rzeczywistości wiele organizacji ich nie stosuje. A potem pojawiają problemy z wdrożeniami, nie wiedzą, która wersja jest gdzie, nie mogą szybko wrócić do poprzedniej wersji.**

---

### **[Slajd 14: „Podsumowanie bloku CI/CD"]**

**Dobrze, podsumujmy to, co omówiliśmy w tym bloku o CI/CD:**

  * Porównaliśmy tradycyjne podejście do CI/CD z podejściem wykorzystującym ILUM. Widzieliście, jak bardzo ILUM upraszcza konfigurację i zarządzanie wdrożeniami.
  * Pokazałem Wam praktyczne przykłady pipeline'ów dla GitLab'a. Wykorzystujemy REST API ILUM, więc integracja jest bardzo prosta.
  * Przeszliśmy przez dobre praktyki dla aplikacji Spark - separację środowisk, wersjonowanie, testowanie, strategie rollback i monitoring.

**Świetnie! Zapraszam was w takm razie na przerwę obiadową. Po powrocie zajmiemy się monitoringiem - równie ważnym tematem dla DevOps.**

---

### **[Slajd 15: „Przerwa obiadowa"]**

# ⏰ 13:00 - 14:00 | Przerwa obiadowa 🍴

# 🕒 14:00 - 15:30 | Monitoring klastra i aplikacji (Observability)

---

### **[Slajd 16: Monitoring w środowisku ILUM"]**

## **🟢 Cele bloku:**

* Zrozumienie znaczenia observability w środowiskach Big Data
* Poznanie narzędzi monitoringu metryk Spark i Kubernetes
* Konfiguracja stosu monitoringu (Prometheus + Grafana)
* Konfiguracja alertingu i najlepsze praktyki monitorowania ILUM

**Witamy z powrotem po przerwie obiadowej! Mam nadzieję, że trochę odspanęliście i jesteście gotowi na kolejną porcję wiedzy.**

W tym bloku skupimy się na jednym z najważniejszych aspektów utrzymania platformy danych - observability, czyli zdolności do zrozumienia stanu systemu na podstawie danych, które on generuje.
Zacznijmy od podstawowego pytania retorycznego - kto z Was miał kiedyś sytuację, że aplikacja Spark przestała działać i na pierwszy rzut oka nie wiedzieliście dlaczego? Wydaje mi, że każda osoba która mierzy się ze Sparkiem ma takie doświadczenia, w Big Data jest to codzienność. I dlatego właśnie observability jest tak kluczowe dla ILUMa i nie tylko.

W środowisku Big Data mamy do czynienia z wyjątkowymi wyzwaniami:
  * Rozproszone obliczenia. Zadania Spark działają na wielu węzłach, dane są podzielone na partycje, każdy executor może mieć inne problemy.
  * Dynamiczne zasoby. Pody pojawiają się i znikają, Kubernetes skaluje w górę i w dół, zasoby są przydzielane dynamicznie.
  * Duże wolumeny danych. Trudno przewidzieć wszystkie scenariusze, dane mogą być różne, jakość może się zmieniać.
  * Złożone zależności. ILUM zależy od Kubernetes, Kubernetes od storage, Spark od sieci, wszystko od wszystkiego.

I dlatego właśnie kluczowe tutaj jest observability. To nie jest tylko monitoring - to zdolność do zrozumienia, co się dzieje w systemie.

Mamy trzy filary observability:

  * Po pierwsze - metryki. To liczby opisujące stan systemu - CPU, RAM, czas wykonania, liczba błędów. Metryki mówią nam "co" się dzieje.
  * Po drugie - logi. To szczegółowe informacje o wydarzeniach - kto, kiedy, co zrobił, jakie były parametry. Logi mówią nam "dlaczego" się dzieje.
  * Po trzecie - traces. To śledzenie przepływu żądań przez system - jak żądanie przechodzi przez różne komponenty. Traces mówią nam "jak" się dzieje.

W kontekście ILUM powinniśmy monitorować wszystko co się da:
  * Stan komponentów platformy - czy ilum-core działa, czy MongoDB odpowiada, czy MinIO ma miejsce.
  * Wydajność zadań Spark - ile czasu trwa wykonanie, ile zasobów zużywa, czy są bottlenecki.
  * Stan klastra Kubernetes - czy węzły są zdrowe, czy pody się uruchamiają, czy storage działa.
  * Jakość danych i SLA procesów - czy dane przychodzą na czas, czy są kompletne, czy procesy kończą się w założonym czasie.
  * To wszystko razem daje nam pełny obraz tego, co się dzieje w naszej platformie.**


**Teraz pokażę Wam w praktyce narzędzia do monitoringu dostarczane wraz z ILUMem, jak wygląda kompletny stos observability dla ILUM.**

  **Prometheus zbiera i przechowuje metryki. To serce systemu metryk - co 15 sekund odpytuje wszystkie źródła i zapisuje dane.**
    Prometheus sam w sobie nie służy ani do wizualizacji, ani do przeglądania danych, ale do tego przejdziemy za moment, jednak udostępnia on UIa, na którym możemy wstępnie przeglądać zgromadzone przez niego metryki
    *Przechodzimy do prometheusa i robimy mini-demo*
    Jak widzimy prometheus zbiera bardzo dużo metryk, tak naprawde praktycznie wszystkie systemy używane w ILUMie łąćznie z jobami sparkowymi, poprzez odpowiednią konfigurację mozna monitorować i zbierac z nich metrykli za pomocą prometheusa dlatego jest on podstawowym i bardzo przydatnym narzędziem w swiecie BigData i nie tylko.

  **Grafana do wizualizacji metryk w dashboardach - tutaj widzicie wykresy CPU, pamięci, czasów wykonania.**
    A grafana właśnie jest narzędziem stosowanym do wizualizacji tych metryk przez prometheusa gromadzonych, za jej pomoca możemy sobie przygotować dashbordy, które będą nam ukazywać interesujące nas informacje, daje nam ogromną dowolnośc, bo na prawdę możemy tam sobie stworzyć dashbord z danymi taki jaki sobie tlyko wyobrazimy, grafana zintegrowana z ILUMem przychodzi z kilkoma gotowymi dashbordami do monitorinug samych komponentów ILUMA jak i jobów Sparkowych przez ILUMA uruchamianych co zaraz postaram wam się przedstawić.
    *Przechodzimy na Grafane i pokazujemy dashbordy krótko komentując*

  **Poiza tym w prostych przypadkach możemy oczywiście posługiwać się starym dobrym Spark UIem**
    Ktory sam w sobie jest narzędziem do wizualizacji metryk pojedynczego joba sparkowego i daje nam bardzo dużo informacji na jego temat oraz naszymi sparkowymi matrykami i analizami dostarczanymi przez samego ILUMa.
    *Krotka prezentacja jakiegos SparkUIa i naszych metryk*

  **Na początku może być troche mozolnej pracy z tym aby te wszystkie dahsbordy stworzyc, rozeznac sie w metrykach, ale gdy to już działa, to jest nieocenione. Nie wyobrażam sobie zarządzania platformą Big Data bez tego.**

Gdybyśmy mieli troche więcje czasu moglibyśmy pokusić sie o swtorzenie jakiegos grafanowego dashbordu, ale to może zostawimy dla was na później jeśli ktoś będzie miał ochotę.
---

### **[Slajd 17: „Logowanie na przykładzie Lokiego"]**

Przejdzmy sobie teraz do zbierania logów aplikacji. Jak wiemy Każda aplikacja, serwer czy system generuje logi - to zapis zdarzeń, komunikatów i operacji, które pozwalają nam zrozumieć, co dzieje się „pod maską” systemu. Mogą to być informacje o uruchomieniu usługi, błędach, wyjątkach, zapytaniach użytkowników czy stanie infrastruktury. JEst to kluczowy aspekt przy reaguowaniu na błedy lub moniotorowaniu działania systemu, jeśli system dobrze nie "loguje" swojej pracy znacznie ciężej diagnozuje się, a w konsekwencji rozwiązuje jego problemy.

Należy sobie zadać pytanie, po co zbieramy logi?
  Diagnostyka i rozwiązywanie problemów - logi pomagają szybko zlokalizować błędy i awarie.
  Monitoring i bezpieczeństwo - analiza logów pozwala wykrywać nietypowe zachowania i potencjalne zagrożenia.
  Audyt i zgodność z regulacjami - wiele branż wymaga przechowywania logów w celach kontrolnych i prawnych.
  Optymalizacja i analiza trendów - dane z logów pomagają poprawiać wydajność systemów, przewidywać problemy i planować rozwój infrastruktury.
  Współpraca zespołowa - centralne logi ułatwiają komunikację między developerami, DevOps i zespołami wsparcia.

Jak ILUM realizuje te potrzeby. ILUMa możemy zainstalować z włączonym modułem agregacji logów, wraz z aplikacją deployowany jest wtedy Loki i Promtail.
  Loki - system do agregacji logów, zaprojektowany z myślą o łatwej integracji z Grafaną. Pozwala przeszukiwać i wizualizować logi z wielu źródeł w jednym miejscu.
  Promtail - lekki agent, który zbiera logi z serwerów i kontenerów, a następnie przesyła je do Loki w uporządkowany sposób.

Jakie są korzyści tego podejścia 
  Centralizacja logów - jeden spójny system zamiast plików rozproszonych po serwerach.
  Łatwe przeszukiwanie i filtrowanie logów.
  Tworzenie dashboardów i alertów w czasie rzeczywistym.
  Szybsze diagnozowanie problemów i raportowanie incydentów.

Oczywiście ILUM pozwala bezpośrednio z UIa czytać logi jobów sparkowych, jednak nie pokrywa wszystkich tych potrzeb, bez lokiego nie będziemy wstanie analizowac i przeglądać logów wszytskich komponentów system zaczynając od baz danych przezsame Pody Ilumowe kończąc na jobach Sparkowych, w kontekście samego ILUMa pozwala on również na czyszczenie zasbów na klastrze, np usuwanie podów gdy joby sparkowe już sie zakończa, bo nie musimy z nich korzystać aby zlistować logi danego joba sparkowego, gdyż są one już zapisane w lokim i przez jego API łatwo nam je jest znaleźc.

DLatego w produkcyjnych wdrożeniach warto rozważyć wdrożenie Lokiego aby jeszcze lepiej zarządzać naszym systemem.

### **[Slajd 18: Alerting, Prometheus AlertManager"]**

Jeśli mówimy o monitoringu nie sposób nie powiedzieć troche wiecej o alertach.

W systemach Big Data (np. opartych Spark, Flink, Hadoop) mamy do czynienia z przetwarzaniem dużych ilości danych w czasie rzeczywistym lub wsadowo. W takim środowisku alerting pełni kluczową rolę, bo pozwala na:
  * Szybką reakcję na awarie jobów, np. Spark job się nie wykonał, przetwarzanie zatrzymało się, albo jakieś dane nie zostały zapisane.
  * Monitorowanie wydajności, np. czas wykonania jobów przekroczył próg, zużycie pamięci czy CPU jest zbyt wysokie.
  * Wczesne wykrywanie nieprawidłowości w danych, np. spadek liczby rekordów w strumieniu lub nieoczekiwany wzrost błędów.
  * Bez alertingu problemy mogą pozostać niewykryte przez dłuższy czas, co w środowiskach Big Data może skutkować poważnymi konsekwencjami biznesowymi - np. brak aktualnych danych dla analityki, problemy z raportami lub systemami downstream.

Prometheus i Alertmanager - co to jest
Prometheus jak wiemy to system monitorowania i zbierania metryk. Zbiera metryki z różnych źródeł, m.in. aplikacji Spark, baz danych, serwerów itp. Ale pozwala on też definiować reguły alertów na podstawie metryk (np. CPU > 80%, liczba błędów > 100).

A Alertmanager jest to komponent Prometheusa odpowiedzialny za zarządzanie alertami:
  * Przyjmuje on alerty od Prometheusa.
  * Wysyła powiadomienia w różny sposób, np, powiadamiając zainteresowanego na Slacky, czy poprzez wiadomośc e-mail.
  * Pozwala też grupować, tłumić i zarządzać powtarzającymi się alertami.
  * W skrócie: Prometheus wykrywa problem, Alertmanager powiadamia odpowiednią osobę/zespół.

W takim razie w jaki sposób możemy wykorzystać te zalety Alertmanagera we współpracy z prometheusem w Sparku
Niestey nie mamy aż tyle czasu żebym wam to mógł zaprezentować w parktyce więc jedynie postaram się podać pewiem przykład aby wam to zobrazować.
Spark jak już wiemy udostępnia metryki w formacie przyjaznym dla prometheusa.

* Przykładowa metryka może zbierać czas trwania jobu w sekundach.
* Prometheus zbiera te metryki co np. 15 sekund.
* Definiujemy sobie przykładowy alert w Prometheusie, który sprawdza czy wartość tej metryki nie przekracza 600 sekund, czyli 10 minut
* AlertManagera, możemy skonfigurować tak aby wtedy, gdy taki alert zostanie ztrigerrowany  wysłać wiadomość na Slacka do odpowiedzialnego dewelopera

Efekt:
  Jeśli job Spark trwa >10 minut, Prometheus wykrywa alert.
  Alert trafia do Alertmanagera, który wysyła powiadomienie na Slacka zespołu operacyjnego.
  Dzięki temu inżynierowie mogą szybko interweniować - restart jobu, debug danych, itp.

Krótko podsumowując, dlaczego alerting jest ważny
* Szybkie wykrywanie problemów - w Big Data nawet godzina opóźnienia może oznaczać błędne raporty czy przetwarzanie danych w złym stanie.
* Automatyzacja reakcji - alerty mogą wyzwalać automatyczne skrypty naprawcze.
* Redukcja ryzyka biznesowego - pozwala uniknąć awarii produkcyjnych i niedostarczonych danych.
* Lepsze planowanie i optymalizacja - analiza alertów pozwala zidentyfikować wąskie gardła w jobach Spark i w infrastrukturze.

---

### **[Slajd 19, Przerwa: „Podsumowanie bloku"]**
 
# ⏰ 15:30 - 16:00 | Przerwa kawowa

**Podsumwoanie bloku**

Co omówiliśmy:

✅ Architekturę observability dla ILUM. Widzieliście, jak Prometheus i Grafana współpracują, żeby dać pełny obraz stanu platformy.  
✅ Krótko przedstawiliśmy funkcjonalności Prometheusa i Grafany. Pokazałem Wam, jak przeglądać metryki z ILUM i Spark oraz jak wyglądają dashboardy.
✅ Omówilismu sobie Logi, czyli drugą po metrykach nogę observability. 
✅ Poruszyliśmy kwestie alertingu, czym jest i dlaczego jest tak ważny

Kluczowe wnioski:
- Observability to fundament niezawodnej platformy. Bez tego jesteście ślepi.
- Monitoruj SLI, nie wszystko co się da. Skupcie się na tym, co ma znaczenie dla użytkowników.
- Alerty muszą być actionable. Każdy alert musi mówić, co robić.
- Dashboardy dostosuj do odbiorcy. Executive potrzebuje innych informacji niż DevOps.
- Logi i metryki razem dają pełny obraz. Metryki mówią "co", logi mówią "dlaczego".


🎯 **Pytania przed przerwą?**

**Świetnie! Po przerwie ostatni blok - dobre praktyki DevOps dla platform danych. To będzie synteza wszystkiego, co omówiliśmy dzisiaj.**

---

### **[Slajd 20: „Infrastructure as Code dla ILUM"]**

## **🟢 Cele bloku:**

* Poznanie najlepszych praktyk Infrastructure as Code dla ILUM
* Strategie backup i disaster recovery dla platform danych
* Bezpieczeństwo ILUM - autentykacja, autoryzacja, szyfrowanie
* Optymalizacja kosztów i zarządzanie zasobami
* Współpraca Dev-DataEng-DevOps w kontekście platform danych

---

**Witamy w ostatnim bloku dzisiejszego dnia! Mam nadzieję, że trochę odpoczęliście i jesteście gotowi na ostatnią już dzisiaj porcję wiedzy.**
W tym bloku skupimy się na najlepszych praktykach DevOps specyficznych dla platform danych. To wiedza, która pozwoli Wam utrzymać ILUM w sposób profesjonalny, skalowalny i bezpieczny.

Zacznijmy od Infrastructure as Code. Co to tak właściwie jest?
Jest to podejście do zarządzania infrastrukturą IT (np. serwery, sieci, bazy danych, chmura) przy użyciu kodów i skryptów zamiast ręcznej konfiguracji.
Celem takiego podejścia jest automatyzacja tworzenia, modyfikacji i skalowania infrastruktury.
Jakie niesie to ze sobą korzyści:
  Powtarzalność - te same środowiska można odtworzyć wielokrotnie.
  Szybkość - provisioning środowisk w minutach zamiast godzin/dni.
  Bezpieczeństwo - konfiguracja w repozytorium, możliwość przeglądu zmian i audytu.
  Popularne narzędzia: Terraform, Ansible, CloudFormation, Pulumi, czy ArgoCD.
W skrócie: IaC pozwala traktować infrastrukturę tak samo jak kod - wersjonować ją, testować i automatyzować.

A jakie jest tradycyjne podejście do tego problemu i do czego może prowadzić, Tradycyjne podejście to "klikaj i modyfikuj", czyli wykonywanie wszyskich instalacji z przysłowiowego palce, prowadzi to często do:
  * configuration drift. Środowisko dev wygląda inaczej niż test, test inaczej niż prod. Nikt nie wie, jakie są różnice.
  * brak wersjonowania. Ktoś zmienił konfigurację, ale nikt nie wie co, kiedy i dlaczego.
  * problemy z odtwarzalnością. "U mnie działa" - klasyka. Nie można odtworzyć środowiska.
  * brak dokumentacji. Wiedza jest w głowach ludzi. Gdy ktoś odchodzi, wiedza odchodzi z nim.

Infrastructure as Code rozwiązuje te problemy:
  * deklaratywne definicje. Opisujemy żądany stan, narzędzie dba o to, żeby go osiągnąć.
  * wersjonowanie w Git. Każda zmiana jest śledzona, można wrócić do poprzedniej wersji.
  * automatyzacja. Spójne wdrożenia, bez ręcznych błędów.
  * dokumentacja jako kod. Infrastruktura sama się dokumentuje - kod to dokumentacja.

W kontekście ILUM oznacza to, że cała konfiguracja - klaster Kubernetes, Helm charty, monitoring, alerting - wszystko jest w kodzie, wersjonowane, automatycznie wdrażane.

Brzmi jak dużo pracy? Na początku tak, ale długoterminowe korzyści są ogromne.
Postaram się wam teraz przedstawić mały przykład tego jak takie podejście może być wykorzystane w ILUMie
*Przechodzimy do dema ILUM + ArgoCD*

### **[Slajd 21: „Backup i Disaster Recovery"]**

Przejdźmy teraz do bardzo ważnego tematu - strategii backup i disaster recovery dla platform danych. 
Co to jest?

Backup - tworzenie kopii zapasowych danych (np. hurtowni, plików w HDFS, tabel w Lakehouse), które można odtworzyć w przypadku awarii lub utraty danych.
A Disaster Recovery to strategia i procesy pozwalające odtworzyć cały system (nie tylko dane) po poważnej awarii - np. awarii klastra, regionu chmurowego czy centrum danych.

🔹 Dlaczego to ważne w Big Data?
Dane są często krytycznym zasobem biznesowym - utrata oznacza straty finansowe i reputacyjne.
Systemy Big Data są rozproszone - awaria jednego elementu może zatrzymać całą analitykę lub przetwarzanie strumieniowe.
Z uwagi na wolumen danych, backup i DR muszą być zautomatyzowane i zoptymalizowane kosztowo (np. backup przyrostowy, snapshoty w chmurze).

🔹 Przykłady podejść

Backup w systemach plików rozproszonych (HDFS snapshoty, S3 versioning).
Kopie metadanych i konfiguracji (np. Hive Metastore, Spark job configs).
DR mozemy realizowac w chmurze: replika klastra i danych.
Automatyczne testy DR - symulacja awarii i odtwarzanie środowiska.

👉 W skrócie: Backup chroni dane, Disaster Recovery chroni cały system. Razem zapewniają ciągłość działania w Big Data.
W środowisku Big Data nie możemy sobie pozwolić na utratę danych czy długie przestoje.

**Dlaczego backup jest krytyczny dla ILUM?**

W ILUM mamy kilka kluczowych komponentów, które przechowują dane:
* **MongoDB** - metadane zadań, historie wykonań, konfiguracje użytkowników. To serce platformy.
* **MinIO** - artefakty, wyniki zadań, pliki tymczasowe. Często gigabajty czy terabajty danych.
* **PostgreSQL** - dane Hive Metastore, Airflow, Marquez. Kluczowe metadane dla całego ekosystemu przetwarzania.

Utrata któregokolwiek z tych komponentów może oznaczać:
- Niemożność uruchomienia zadań
- Utratę historii wykonań
- Konieczność rekonfiguracji całej platformy
- W najgorszym przypadku - utratę miesięcy pracy

Dlatego najlepiej dla każdego z tych komponentów należy przygotować strategię backupu

**Disaster Recovery Plan:**
Jesli chodz o disaster recovery plan to wczesniej omówione IAC moze bardzo pomóc w szybkich odtworzeniu środosiwka w pierwotnej postaci
Ważne aby byc na takie ewentualnosci byc przygotowanym.

**Kluczowe zasady:**
- Backup to nie tylko kopia - to testowana procedura odtwarzania
- Automatyzacja wszystkich procesów backup
- Monitoring stanu backup - alerty gdy coś się nie powiedzie
- Regularne testy disaster recovery 

### **[Slajd 22: „Bezpieczeństwo - Ograniczony RBAC, OAuth"]**

Bezpieczeństwo w platformach danych to nie opcja - to konieczność. Za pośrednictwem ILUMa przetwarza się często wrażliwe dane biznesowe, m.in. z tego powodu musimy zapewnić odpowiedni poziom ochrony.

**Główne obszary bezpieczeństwa w ILUM:**

**1. Autentykacja i autoryzacja w ramcha ILUM UI:**

**RBAC (Role-Based Access Control, ):**
to model kontroli dostępu, w którym uprawnienia przypisuje się do ról, a nie bezpośrednio do użytkowników.
Użytkownik otrzymuje jedną lub więcej ról (np. Data Analyst, Admin).
Rola definiuje zestaw uprawnień (np. odczyt danych, uruchamianie jobów, zarządzanie klastrem).
Dzięki temu zarządzanie dostępem jest prostsze, spójne i łatwiejsze do audytowania.
🔹 W świecie Big Data RBAC pozwala np.:
Oddzielić role data scientistów (dostęp do danych) od administratorów (zarządzanie infrastrukturą).
Ograniczyć ryzyko – użytkownik dostaje tylko to, co jest mu niezbędne (zasada najmniejszych uprawnień).
👉 W skrócie: RBAC = kontrola dostępu oparta na rolach, zapewniająca bezpieczeństwo i przejrzystość w dużych systemach danych.
- Definiujemy role, każda z nich ma określone uprawnienia do zasobów
- Użytkownik może mieć wiele ról w różnych projektach
- Granularne uprawnienia - kto może uruchamiać zadania, kto może je tylko przeglądać

**OAuth 2.0 / OpenID Connect:**
- Integracja z zewnętrznymi dostawcami (Azure AD, Google, Keycloak)
- Single Sign-On (SSO) - jedno logowanie do wszystkich systemów
- Centralne zarządzanie użytkownikami

**LDAP:**
- ILUM integruje się z umożliwiając ustawienie szczegółów połączenia, zdefiniowanie zapytań uwierzytelniających i mapowanie atrybutów LDAP na wewnętrzny model użytkownika Ilum.

**2. Monitoring bezpieczeństwa:**

**Audit logging:**
- ILUM gromadzi spis aktywności - Kto, kiedy, co zrobił w systemie

**Vulnerability scanning:**
- Skanowanie obrazów kontenerowych, dbajmy o to aby nasze obrazy kontenerowe nie posiadały krytycznych CVE

**Praktyczne wskazówki w ramach tej dobrej praktyki:**
- Principle of least privilege - minimalne uprawnienia potrzebne do pracy
- Regularne przeglądy uprawnień - co kwartał
- Dwuetapowa autoryzacja dla adminów
- Segregacja obowiązków - nikt nie powinien mieć pełnego dostępu sam


### **[Slajd 23: „Skalowanie i optymalizacja kosztów"]**

Zarządzanie kosztami w środowiskach Big Data to sztuka. Z jednej strony potrzebujemy wydajności, z drugiej nie możemy wydawać bez kontroli.

**Wyzwania kosztowe w Big Data:**
Aplikacje Spark mogą być bardzo zasobożerne:
- Jeden job może zużyć setki GB RAM
- Przetwarzanie może trwać godziny
- Dane rosną wykładniczo
- Zasoby często są niedostatecznie wykorzystane

**Strategie optymalizacji w ILUM:**

**1. Estymacja zużycia zasobów:**

**Monitoring i analiza:**
Poprzez narzędzia do monitoringu możemy lepiej estymować zuzycie zasobów co pozwoli nam unikać zbędnych kosztów
- Prometheus zbiera metryki zużycia CPU, RAM, storage
- Grafana dashboardy pokazują trendy
- Analiza wzorców użytkowania - kiedy są szczyty, kiedy spokój
- Identyfikacja jobów najbardziej zasobożernych

**Estymacja potrzeb:**
- Przewidywanie przyszłych potrzeb na podstawie trendów
- Planowanie zakupów infrastruktury

**2. Resource management w Kubernetes:**

**Resource Quotas, czym są:**
- Limity na poziomie namespace'ów
- Kontrola maksymalnego zużycia CPU, RAM, storage
- Zapobieganie monopolizacji zasobów przez jeden zespół
ILUM implementuje to rozwiązanie w ramach konfiguracji klastra, co pozwala na ograniczanie zasobów, dzieki czemu jesteśmy pewnie, że któs przez przypadek nie zużyje zasobów jakie nie sa mu przypisane

**Limit Ranges, podobnie:**
- Domyślne i maksymalne limity dla pojedynczych podów
- Zapobieganie uruchamianiu zbyt dużych zadań
- Wymuszanie rozsądnych wartości

**3. Autoskalowanie:**

**Dynamiczne alokowanie zasobów w Sparku:**
- Automatyczne skalowanie liczby replik na podstawie metryk
- Reakcja na wzrost obciążenia
- Oszczędności w okresach niskiego ruchu
W ILUM można to osiągnąć dzieki dynamic allocation w Sparku, czy na przykład autoPauzowalnej grupie ILUMOwej

**Cluster Autoscaler, mechanizm K8S:**
- Automatyczne dodawanie/usuwanie węzłów klastra
- Reakcja na zapotrzebowanie na zasoby
- Znaczne oszczędności w chmurze

**Praktyczne wskazówki w ramach Skalowania i optymalizacji kosztów:**
- Regularne przeglądy kosztów - co miesiąc
- Edukacja zespołów o kosztach zasobów
- Chargeback/showback - zespoły widzą swoje koszty
- Automatyzacja wszystkich procesów optymalizacji


### **[Slajd 24: „Współpraca DevOps i Data Engineering"]**

Na koniec porozmawiajmy o współpracy między zespołami DevOps i Data Engineering. To często największe wyzwanie w organizacjach - różne perspektywy, różne priorytety, ale wspólny cel.

**Różnice w podejściu:**

**DevOps myśli o:**
- Stabilności infrastruktury
- Automatyzacji procesów
- Monitoringu i alertach
- Bezpieczeństwie i compliance
- Kosztach infrastruktury

**Data Engineering myśli o:**
- Jakości danych
- Wydajności przetwarzania
- Logice biznesowej
- Optymalizacji algorytmów
- Dostarczaniu wartości biznesowej

**Wspólne wyzwania:**
- Różne języki techniczne
- Różne priorytety czasowe
- Różne metryki sukcesu
- Różne narzędzia i procesy

**Model współpracy w ILUM:**

**1. Wspólne odpowiedzialności:**

**DevOps odpowiada za:**
- Infrastrukturę Kubernetes i ILUM
- CI/CD pipeline'y we współpracy z DE
- Monitoring infrastruktury
- Backup i disaster recovery
- Bezpieczeństwo platformy
- Skalowanie i optymalizację kosztów

**Data Engineering odpowiada za:**
- Logikę biznesową aplikacji
- Optymalizację zapytań Spark
- Jakość i walidację danych
- Definicję pipeline'ów danych
- Monitoring jakości danych
- Performance tuning aplikacji

**2. Narzędzia współpracy:**

**Git workflows:**
- Wspólne repozytoria dla kodu i konfiguracji
- Code review dla wszystkich zmian
- Branching strategy dostosowana do zespołu

**Infrastructure as Code:**
- DevOps definiuje infrastrukturę
- Data Engineers mogą modyfikować konfigurację aplikacji
- Wszystko wersjonowane i audytowane

**Shared monitoring:**
- Wspólne dashboardy w Grafana
- Alerty dla obu zespołów
- SLA/SLI zdefiniowane wspólnie

**3. Procesy współpracy:**

**Planowanie:**
- Wspólne planowanie
- Uwzględnienie potrzeb infrastrukturalnych Data Enginerow przez DevOpsów
- Synchronizacja roadmap

**Reakcja na błędy:**
- Wspólne procedury reagowania na incydenty
- Eskalacja między zespołami
- Post-mortem analysis

**Dzielenie sie wiedzą:**
- Regularne sesje edukacyjne
- Dokumentacja procesów
- Cross-training między zespołami

**4. Dobre praktyki:**

**Komunikacja:**
- Regularne standupy między zespołami
- Slack channels dla szybkiej komunikacji
- Dokumentacja decyzji i zmian

**Automatyzacja:**
- Automatyzacja wszystkich powtarzalnych zadań
- Self-service dla Data Engineers
- Monitoring end-to-end

**Ciągłe doskonalenie:**
- Retrospektywy po każdym większym wdrożeniu
- Metryki współpracy
- Feedback loops

**Korzyści z dobrej współpracy:**
- Szybsze wdrożenia
- Mniej incydentów produkcyjnych
- Lepsza jakość rozwiązań
- Wyższa satysfakcja zespołów
- Większa wartość biznesowa

### **[Slajd 25: „Podsumowanie"]**

**To był już ostatni slajd**

Tym samym dotarliśmy do końca 4 dnia naszego kursu, dzisiaj udało nam się omówić kilka ważnych kwesti w ramach zarządzania platformą ILUM i nie tylko
1. Zarządzanie Platformą. Wdrażanie, aktualizacje
2. CI/CD aplikacji SParkowych z użyciem ILUM
3. Monitoring
4. DObre prakatyki DevOps

Dziękujemy bardzo za uwagę, jeśli są jakieś pytania to zapraszamy do ich zadawania.
---

