# Projekt z SQL oprava
- V repozitáři se nachází opravené skripty SQL_script_primary_final a datovy_podklad_VO (zde specificky pro VO1 a VO2) se zapracovanými požadavky. Zároveň je vložen
  těmito opravami nově vygenerovaný t_michaela_ticha_project_sql_primary_final.csv.
- Na konci průvodní listiny se nově nachází odpovědi na VO dle zadání, nechybí také nový popis mezivýsledků.

## Přehled obsahu repozitáře
- **Úvodní dokument  a popis mezivýsledků**
1. pruvodni_listina.md
- **SQL skripty**
1. SQL_script_primary_final - SQL skript generující [tuto](https://github.com/tichaelam/projekt_z_SQL/blob/main/t_michaela_ticha_project_sql_primary_final.csv) tabulku
2. SQL_script_secondary_final - SQL skript generující [tuto](https://github.com/tichaelam/projekt_z_SQL/blob/main/t_michaela_ticha_project_sql_secondary_final.csv) tabulku
3. datovy_podklad_VO - SQL skript, který získá datový podklad k zodpovězení všech VO
- **tabulky**
1. t_michaela_ticha_project_sql_primary_final.csv
2. t_michaela_ticha_project_sql_secondary_final.csv
   
## Úvod
- Projekt se zabývá životní úrovní občanů, a to na základě dostupnosti základních potravin širové veřejnosti. Slouží jako podkladový materiál pro porovnání dostupnosti potravin na základě průměrných příjmů za období od 2006 do 2018, s cílem zodpovězení následujících výzkumných otázek (VO):
1. Rostou v průběhu let mzdy ve všech odvětvích, nebo v některých klesají?
2. Kolik je možné si koupit litrů mléka a kilogramů chleba za první a poslední srovnatelné období v dostupných datech cen a mezd?
3. Která kategorie potravin zdražuje nejpomaleji (je u ní nejnižší percentuální meziroční nárůst)? 
4. Existuje rok, ve kterém byl meziroční nárůst cen potravin výrazně vyšší než růst mezd (větší než 10 %)?
5. Má výška HDP vliv na změny ve mzdách a cenách potravin? Neboli, pokud HDP vzroste výrazněji v jednom roce, projeví se to na cenách potravin či mzdách ve stejném nebo následujícím roce výraznějším růstem?
- Pro zodpovězení páté VO byla vytvořena dodatečná tabulka o EU státech pro porovnání.
- **Byly použity čtyři hlavní zdroje a k nim přidružené dimenzní tabulky, první dva pro finalizaci [primární tabulky](https://github.com/tichaelam/projekt_z_SQL/blob/main/t_michaela_ticha_project_sql_primary_final.csv) a druhé dva pro [sekundární tabulku](https://github.com/tichaelam/projekt_z_SQL/blob/main/t_michaela_ticha_project_sql_secondary_final.csv):**
*1. czechia_payroll*
*2. czechia_price*
*3. countries*
*4. economies*

## Popis kódů sloupců tabulek
- t_michaela_ticha_project_sql_primary_final.csv

| Název                     | Popis                                                         |
|---------------------------|---------------------------------------------------------------|
| year                      | Kalendářní rok, ke kterému se záznam vztahuje                |
| quarter                   | Čtvrtletí (Q1–Q4), ve kterém je hodnota zjišťována           |
| category_code             | Kód potravinové kategorie                                    |
| category_name             | Název potravinové kategorie                                  |
| unit_standardized         | Standardizovaná jednotka měření (kg, litr, ks)              |
| avg_price                 | Průměrná cena dané kategorie za období                      |
| industry_branch_code      | Kód průmyslového odvětví                                    |
| industry_branch_name      | Název průmyslového odvětví                                  |
| avg_wage_yearly_branch    | Průměrná roční mzda v daném odvětví                          |
| avg_price_yearly          | Průměrná roční cena dané kategorie                           |
| price_yoy_percent         | Meziroční procentuální změna ceny                            |
| avg_food_price_yearly     | Průměrná roční cena potravin celkem                          |
| food_yoy_percent          | Meziroční procentuální změna cen potravin*                    |
| avg_wage_yearly_overall   | Průměrná roční mzda všech odvětví                            |
| wage_yoy_percent          | Meziroční procentuální změna mezd                            |
| is_bread                  | Změna dostupnosti chleba v čase, 1 = TRUE, 0 = FALSE         |
| is_milk                   | Změna dostupnosti mléka v čase, 1 = TRUE, 0 = FALSE          |

*Year over Year (= porovnání hodnoty v aktuálním roce se stejným ukazatelem z předchozího roku)

- t_michaela_ticha_project_sql_secondary_final.csv

| Název       | Popis                                     |
|-------------|-------------------------------------------|
| country     | Název státu                               |
| year        | Rok, ke kterému se hodnoty vztahují       |
| gdp         | HDP státu                                 |
| population  | Počet obyvatel státu                      |
| gini        | Gini koeficient (míra ekonomické nerovnosti) |

## Hlavní oblasti zájmu
- V průběhu psaní skriptu bylo stěžejní se zaměřit na několik problematik, aby byl nejenom funkční, ale aby se z výstupních dat daly získat potřebné informace. Tyto problematiky budou sepsány níže.
  
### Mzdové údaje
- calculation_code 100 a value_type_code 5958 byla jediná kombinace, se kterou se následně daly srovnat ceny potravin.

### Regionálnost cen potravin
- Jelikož výše mezd byla poskytnuta na celostátní úrovni, musela jsem použít celostátní průměr cen potravin. To je především odůvodněno zněním VO, které se ptají na celostátní stav nebo podle stav podle odvětví. Regiony nebyly pro analýzu potřebné.

### Časové období
- Dva hlavní datasety czechia_payroll a czechia_price se překrývají pouze v období 2006-2018, brala jsem toto období jako srovnatelné období.

 ### Přiřazení date_from a date_to ke quarter a year
 - czechia_price určoval cenovou úroveň potravin na základě šestidenního pozorování, kdy jsem ceny přiřadila ke korespondenčním kvartálům payroll_quarter a payroll_year za pomocí definování **EXTRACT(QUARTER FROM date_from)** a **EXTRACT(YEAR FROM date_from)**. Díky tomu jsem mohla agregovat ceny na kvartály a mohla jsem spojit dataset správně k údajům z czech_payroll. Tento krok byl také důležitý pro výpočet ročních průměrů a ročních nárůstů cen.

### Kvartální agregace cen
- Kvůli mnoha cenovým měření za rok jsem musela nejdříve spočítat průměry kvartálu a z nich roční průměry. Toto rozhodnutí hlavně umožnilo mít jednu průměrnou cenu na kvartál i potravinovou kategorii a bylo to vhodnější skrze porovnávání se mzdami.

### Průměrování
- Důležité pro *yoy* (označení pro meziroční změnu == percentuální růst cen potravin a výše mezd). Výpočet *yoy* je totiž možný jen na úrovni let a VO se také odkazují na meziroční nárůst. Proto byla mzdová data průměrována jako průměr všech kvartálů daného roku (Q1-Q4) a cenová data byla průměrována jako všechny kvartální průměry daného roku).

### Výpočet yoy hodnot
- pomocí fce LAG() jsem získala hodnotu z roku t-1. Zde jsem také musela vyřešit missing values u let, kde neexistovaly údaje pro předchozí rok.

### Missing values
- NULL hodnoty v projektu nejsou nahrazovány ani nulou, ani dopočítáváním. Funkce AVG() M/U ignoruje. Jelikož výpočet *yoy* hodnot požaduje dvě hodnoty, a to hodnoty z t-1 a současného roku, tak kde chyběl jeden nebo oba údaje, je yoy=NULL. Proto ve finální tabulce je hodně NULL values pro: price_yoy_percent, food_yoy_percent, wage_yoy_percent.

### Duplicity
- Funkcí EXTRACT jsme vyloučili chybu ve výpočtu kvartálů u cen potravin.

### První a poslední srovnatelné období
- U VO 2 je identifikace těchto období klíčové. Vybrala jsem tedy jako první období první kvartál prvního roku (2006 Q1) a jako poslední čtvrtý kvartál posledního roku (2018 Q4).

### Filtrace dat
- V tabulce *economies* jsou ve sloupci *country* pomíchané jak regiony, tak státy, proto jsem musela vyfiltrovat seznam EU států na základě [údajů od Council of Europe](https://www.coe.int/en/web/portal/members-states).

### VO 4
- po vyvolání [SQL skriptu, ze kterého lze odpovědět na VO 4](https://github.com/tichaelam/projekt_z_SQL/blob/main/datovy_podklad_VO), vyjede prázdná tabulka, z čehož lze usuzovat, že růst cen potravin o 10 % nenastal.


## Popis jednotlivých kroků v primárním SQL skriptu
- Obsahem této kapitoly úvodního dokumentu je komentář k jednotlivým krokům a mezivýsledkům SQL skriptu, které jsou přímo v kódu odděleny označením KROK.
  
**1. KROK** - Příprava dat pro analýzu
-  Z tabulky czechia_price jsem vybrala pouze řádky, které mají:
 - platný časový údaj date_from (není NULL)
 - platný region_code (není NULL)
- Pomocí EXTRACT(YEAR) a EXTRACT(QUARTER) jsem z date_from dopočítala sloupce year a quarter.
- Mezivýsledek: View price_with_quarter:
  - 1 řádek = 1 měření ceny v konkrétním regionu + odvozený rok a kvartál
  - odstraněny řádky bez data nebo regionu
    
**2. KROK** - Kvartální agregace
- Z view price_with_quarter jsme spočítali průměrné kvartální ceny (AVG(value)) pro každou kombinaci:
 - year
 - quarter
 - category_code
- Mezivýsledek: View price_quarterly_raw:
 - 1 řádek = 1 rok/ 1 kvartál/ 1 potravinová kategorie
 - sloupec avg_price_raw = surový kvartální průměr ceny

**3. KROK** - Vytvoření payroll_clean
- Z tabulky czechia_payroll jsem vybrala jen ty informace, které reprezentují průměrnou hrubou mzdu na zaměstnance (value_type_code = 5958), jsou počítány jako průměr (calculation_code = 100), mají vyplněné průmyslové odvětví (industry_branch_code IS NOT NULL) a mají vyplněnou hodnotu (value IS NOT NULL).
- Zároveň jsem vytvořila sloupce:
  - payroll_year → year
  - payroll_quarter → quarter
- Mezivýsledek: View payroll_clean:
  - 1 řádek = 1 rok/ 1 kvartál/ 1 odvětví/ 1 mzdový ukazatel
- Také byly odstraněny nevhodné typy výpočtů a NULL hodnoty. Z payroll_clean teprve budu počítat roční průměry podle průmyslových odvětví (KROK 4) a celkový roční průměr (KROK 5)

**4. KROK** - Výpočet roční průměrné mzdy pro každé průmyslové odvětví
- Pro porovnání s ročními cenami potravin potřebuji také mzdy na roční úrovni, proto jsem z payroll_clean spočítala průměrnou roční mzdu pro každé průmyslové odvětví jako průměr přes 4 kvartály daného roku.
- Mezivýsledek: View payroll_yearly_avg_branch_raw:
  - 1 řádek = 1 rok/ 1 odvětví
  - avg_wage_yearly_branch_raw = roční průměr mezd v daném oboru

**5. KROK** - Průměrná mzda v ČR v daném roce
- S tímto průměrem budu porovnávat ceny potravin a HDP.
- Mezivýsledek: View payroll_yearly_avg_overall_raw:
  - 1 řádek = 1 rok
  - avg_wage_yearly_overall_raw = průměrná mzda v celé ČR

**6. KROK** - Roční průměrná cena pro každou kategorii potravin
- Důležitý krok, stejně jako KROK 5, pro výpočet yoy růstu.
- Mezivýsledek: View price_yearly_by_cat_raw:
  - 1 řádek = 1 rok/ 1 kategorie potravin
  - avg_price_yearly_raw = roční průměrná cena potraviny

**7. KROK** - Meziroční růst cen potravin yoy
- Na roční průměrné ceny (price_yearly_by_cat_raw) jsem aplikovala fci LAG, abych získala cenu v předchozím roce pro danou kategorii. Z ní jsem spočítala meziroční změnu měřenou v procentech (price_yoy_raw).
- Mezivýsledek: View price_yearly_by_cat_yoy_raw:
  - 1 řádek = 1 rok/ 1 kategorie
  - avg_price_yearly_raw = roční cena
  - prev_price_yearly_raw = cena v předchozím roce
  - price_yoy_raw = meziroční změna (%)
- POZNÁMKA: První rok v každé kategorii má price_yoy_raw = NULL, protože neexistuje předchozí hodnota.

**8. KROK** - Potravinový index a yoy růsty mezd a potravin
- Vzniká agregovaný potravinový index a jeho meziroční změny, které jsem zároveň porovnala s yoy růstem průměrné mzdy v ČR.
- Z price_yearly_by_cat_raw jsem vytvořila view food_year, kde je pro každý rok spočítán průměr skrze všechny kategorie (avg_food_price_yearly_raw). Toto jsem spojila s roční průměrnou mzdou (payroll_yearly_avg_overall_raw) a zase aplikovala fci LAG pro výpočet food_yoy_raw (yoy růst avg_food_price_yearly_raw) a wage_yoy_raw (yoy růst průměrné mzdy)
- Mezivýsledek: View food_and_wage_yearly_raw:
   - 1 řádek = 1 rok
   - avg_food_price_yearly_raw = průměrná roční cena souboru konkrétních potravin
   - avg_wage_yearly_overall_raw = průměrná roční mzda
   - food_yoy_raw = yoy potravin
   - wage_yoy_raw = yoy mezd
     
**9. KROK** - Vytvoření finální tabulky
- Zde dochází ke spojení všech mezivýsledků do tabulky t_michaela_ticha_project_sql_primary_final, raw hodnoty zaokrouhluji. 

**10. KROK** - Flagování kategorií pro chleba a mléko
- Přidávám dva sloupce typu boolean, pro jednoduché zodpovězení VO 2.

## Popis jednotlivých kroků v sekundárním SQL skriptu
- Přímo ve skriptu se žádné rozdělení na kroky nenachází, jelikož je kód podstatěji kratší. Proto jen obecně popíšu postup:
  
**1. KROK** - Zdrojem dat je dataset *economies*, ze kterého vybírám potřebné sloupce country, year, gdp, population a gini.

**2. KROK** - Filtruji období mezi lety 2006 a 2018 a pouze EU státy. Evropské státy musím filtrovat ručně, protože jsou ve sloupci country i celé regiony nebo jiné kategorie zemí.

**3. KROK** - Seřadím tabulku podle country a year a vytvořím finální sekundární tabulku t_michaela_ticha_project_sql_secondary_final.
# projekt_z_SQL_oprava
