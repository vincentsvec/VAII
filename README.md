# TrailBook - zdieľanie turistických trás

Vincent Švec

Študijná skupina: 5ZYI33

## Stručný opis projektu
TrailBook je webová aplikácia na zdieľanie a objavovanie turistických trás. Registrovaní používatelia do nej pridávajú trasy, ktoré prešli. Ku každej trase uvedú popis, región, náročnosť, dĺžku a prevýšenie, priložia fotografie a voliteľne aj GPX súbor so záznamom trasy. Ostatní používatelia si trasy prezerajú v zozname aj na interaktívnej mape, filtrujú ich podľa regiónu, náročnosti či dĺžky, hodnotia ich, píšu k nim recenzie a ukladajú si ich medzi obľúbené.

Aplikácia je určená turistom, ktorí hľadajú inšpiráciu na výlet alebo si chcú overiť skúsenosti iných s konkrétnou trasou. Jej hlavným účelom je mať na jednom mieste prehľadné a komunitou hodnotené informácie o trasách vrátane mapy, fotografií a aktuálneho počasia v mieste štartu.

## Role v projekte
- **Návštevník (neprihlásený používateľ)** - môže prezerať zoznam trás, ich detail, mapu, fotografie a recenzie. Môže vyhľadávať a filtrovať trasy. Nemôže pridávať ani upravovať žiadny obsah. Môže sa zaregistrovať alebo prihlásiť.
- **Registrovaný používateľ** - má všetky oprávnenia návštevníka. Okrem toho môže vytvárať trasy, upravovať a mazať **vlastné** trasy, nahrávať k nim fotografie a GPX súbory, písať recenzie s hodnotením, upravovať a mazať **vlastné** recenzie a spravovať zoznam obľúbených trás. Spravuje svoj profil.
- **Moderátor** - má všetky oprávnenia registrovaného používateľa. Zodpovedá za kvalitu obsahu: môže mazať nevhodné recenzie a fotografie aj od iných používateľov.
- **Administrátor** - má všetky oprávnenia moderátora. Spravuje používateľov (zmena roly, zablokovanie, zmazanie) a číselník regiónov (vytvorenie, úprava, zmazanie).

Rola používateľa je uložená ako atribút v tabuľke používateľov.

## Prípady použitia podľa rolí

### Návštevník
- Návštevník si prezrie úvodnú stránku s najnovšími a najlepšie hodnotenými trasami.
- Návštevník vyhľadá trasy podľa názvu a vyfiltruje ich podľa regiónu, náročnosti a dĺžky.
- Návštevník listuje zoznamom trás po stránkach.
- Návštevník si zobrazí detail trasy s mapou, fotogalériou, recenziami a aktuálnym počasím.
- Návštevník si nechá zobraziť trasy vo svojom okolí podľa aktuálnej polohy.
- Návštevník sa zaregistruje alebo prihlási.

### Registrovaný používateľ
- Používateľ vytvorí novú trasu a vyplní jej údaje.
- Používateľ nahrá k trase GPX súbor. Aplikácia z neho vypočíta dĺžku a prevýšenie a trasu vykreslí na mape.
- Používateľ nahrá k trase fotografie (aj pomocou drag & drop) a zmaže ich.
- Používateľ upraví alebo zmaže svoju trasu.
- Používateľ pridá recenziu s hodnotením 1-5 k trase, ktorú prešiel, a neskôr ju upraví alebo zmaže.
- Používateľ pridá trasu do obľúbených alebo ju z nich odstráni.
- Používateľ si na svojom profile prezrie svoje trasy, obľúbené trasy a štatistiky (počet trás, nachodené kilometre).
- Používateľ upraví svoj profil a zmení si heslo.
- Používateľ sa odhlási.

### Moderátor
- Moderátor zmaže nevhodnú recenziu iného používateľa.
- Moderátor zmaže nevhodnú fotografiu iného používateľa.

### Administrátor
- Administrátor si zobrazí zoznam používateľov a zmení im rolu.
- Administrátor zmaže používateľa.
- Administrátor vytvorí, upraví (priamo v tabuľke) a zmaže región.

## Plánované entity
- **User (Používateľ)** - registrovaný používateľ aplikácie.
  Atribúty: `id`, `username`, `email`, `password_hash`, `role` (user / moderator / admin), `created_at`.
- **Region (Región)** - horský alebo turistický región, do ktorého trasa patrí (napr. Vysoké Tatry, Malá Fatra).
  Atribúty: `id`, `name`, `description`.
- **Trail (Trasa)** - hlavná entita aplikácie, jedna turistická trasa.
  Atribúty: `id`, `user_id` (autor), `region_id`, `name`, `description`, `difficulty` (ľahká / stredná / náročná), `length_km`, `elevation_gain`, `duration_min`, `start_lat`, `start_lng`, `gpx_file`, `created_at`, `updated_at`.
- **TrailPhoto (Fotografia trasy)** - fotografia nahratá k trase.
  Atribúty: `id`, `trail_id`, `user_id` (kto ju nahral), `filename`, `caption`, `created_at`.
- **Review (Recenzia)** - hodnotenie a slovná recenzia trasy od používateľa.
  Atribúty: `id`, `trail_id`, `user_id`, `rating` (1-5), `text`, `hiked_at` (dátum absolvovania), `created_at`.
- **Favorite (Obľúbená trasa)** - asociačná tabuľka, ktorá rozkladá vzťah M:N medzi používateľmi a trasami.
  Atribúty: `user_id`, `trail_id`, `created_at`.

## Vzťahy medzi entitami
- **Region 1:N Trail** - región obsahuje viacero trás, každá trasa patrí práve do jedného regiónu.
- **User 1:N Trail** - používateľ môže vytvoriť viacero trás, každá trasa má jedného autora.
- **Trail 1:N TrailPhoto** - trasa môže mať viacero fotografií. Pri zmazaní trasy sa zmažú aj jej fotografie (záznamy v DB aj súbory na disku).
- **Trail 1:N Review** - trasa môže mať viacero recenzií. Každá recenzia patrí k jednej trase.
- **User 1:N Review** - používateľ môže napísať viacero recenzií, ku každej trase však najviac jednu (unikátna dvojica `trail_id`, `user_id`).
- **User 1:N TrailPhoto** - používateľ môže nahrať viacero fotografií.
- **User M:N Trail (cez Favorite)** - používateľ si môže uložiť viacero obľúbených trás a jednu trasu môže mať v obľúbených viacero používateľov.

## Hlavné stránky aplikácie
1. **Úvodná stránka** - najnovšie a najlepšie hodnotené trasy, rýchle vyhľadávanie.
2. **Zoznam trás** - tabuľka/karty trás s filtrovaním (región, náročnosť, dĺžka), vyhľadávaním a stránkovaním načítavanými cez AJAX. Prepínanie na mapové zobrazenie.
3. **Detail trasy** - popis, parametre, mapa s vykreslenou GPX trasou, fotogaléria, priemerné hodnotenie, recenzie, počasie v mieste štartu a tlačidlo „pridať do obľúbených“.
4. **Formulár trasy** - vytvorenie a úprava trasy vrátane uploadu GPX súboru a fotografií (drag & drop s náhľadom), s validáciou na strane klienta aj servera.
5. **Profil používateľa** - vlastné trasy, obľúbené trasy, recenzie a štatistiky. Úprava profilu a zmena hesla.
6. **Administrácia** - správa používateľov a ich rolí, správa regiónov s in-place editáciou.
7. **Prihlásenie a registrácia** - formuláre s kontrolou vstupov, pri registrácii aj kontrola dostupnosti mena a emailu cez AJAX.

## Rozdelenie funkcionality

### Základné funkcie
- Registrácia, prihlásenie a odhlásenie, heslá uložené ako hash.
- Roly používateľov a kontrola oprávnení na strane servera (vrátane vlastníctva záznamov).
- CRUD operácie nad trasami, recenziami a regiónmi.
- Upload, zobrazenie a mazanie fotografií trás.
- Filtrovanie a stránkovanie zoznamu trás cez AJAX.
- Pridávanie recenzií cez AJAX s okamžitým prepočtom priemerného hodnotenia.
- Pridávanie a odoberanie obľúbených trás cez AJAX.
- Validácia všetkých vstupov na strane klienta aj servera.
- Responzívny dizajn s vlastnými CSS (SASS) štýlmi.
- Spustenie v prostredí Docker s automatickým importom ukážkových dát.

### Rozširujúce funkcie
- Interaktívna mapa trás pomocou knižnice Leaflet a OpenStreetMap (externé API).
- Upload a spracovanie GPX súboru: výpočet dĺžky a prevýšenia, vykreslenie trasy na mape, výškový profil.
- Zobrazenie aktuálneho počasia v mieste štartu trasy cez Open-Meteo API.
- Vyhľadanie trás v okolí pomocou HTML5 Geolocation API.
- Drag & drop upload fotografií s náhľadom (HTML5 Drag and Drop a File API).
- In-place editácia regiónov v administrácii.
- Kontrola dostupnosti používateľského mena a emailu pri registrácii cez AJAX.
- Pokročilá klientska validácia formulárov so živými chybovými hláškami.
- Moderovanie obsahu (mazanie nevhodných recenzií a fotografií).
