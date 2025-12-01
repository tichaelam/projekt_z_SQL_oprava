# Projekt z SQL oprava
- V repozitáři se nachází opravené skripty SQL_script_primary_final a datovy_podklad_VO (zde specificky pro VO1 a VO2) se zapracovanými požadavky. Zároveň je vložen
  těmito opravami nově vygenerovaný t_michaela_ticha_project_sql_primary_final.csv.
- Na konci průvodní listiny se nově nachází odpovědi na VO dle zadání, nechybí také nový popis mezivýsledků.

## Přehled obsahu repozitáře
- **Úvodní dokument  a popis mezivýsledků**
1. pruvodni_listina.md
- **SQL skripty**
1. SQL_script_primary_final - SQL skript generující [tuto](https://github.com/tichaelam/projekt_z_SQL_oprava/blob/main/t_michaela_ticha_project_sql_primary_final.csv) tabulku
2. SQL_script_secondary_final - SQL skript generující [tuto](https://github.com/tichaelam/projekt_z_SQL_oprava/blob/main/t_michaela_ticha_project_sql_secondary_final.csv) tabulku
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
- **Byly použity čtyři hlavní zdroje a k nim přidružené dimenzní tabulky, první dva pro finalizaci [primární tabulky](https://github.com/tichaelam/projekt_z_SQL_oprava/blob/main/t_michaela_ticha_project_sql_primary_final.csv) a druhé dva pro [sekundární tabulku](https://github.com/tichaelam/projekt_z_SQL_oprava/blob/main/t_michaela_ticha_project_sql_secondary_final.csv):**
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
- U VO 2 je identifikace těchto období klíčové. Vybrala jsem tedy jako první období první rok (2006) a jako poslední poslední rok (2018).

### Filtrace dat
- V tabulce *economies* jsou ve sloupci *country* pomíchané jak regiony, tak státy, proto jsem musela vyfiltrovat seznam EU států na základě [údajů od Council of Europe](https://www.coe.int/en/web/portal/members-states).

### VO 4
- po vyvolání [SQL skriptu, ze kterého lze odpovědět na VO 4](https://github.com/tichaelam/projekt_z_SQL_oprava/blob/main/datovy_podklad_VO), vyjede prázdná tabulka, z čehož lze usuzovat, že růst cen potravin o 10 % nenastal.

## Postup a mezivýsledky v primárním SQL skriptu
- Nejdříve jsem z tabulky czechia_price vybrala pouze ty řádky, které měli platný časový údaj a region_code. Z date_from pomocí EXTRACT(YEAR) a EXTRACT(QUARTER) potom určuji year a quarter. Vzniká tak price_with_quarter.
  - 1 řádek se rovná 1 měření ceny v daném regionu, s odvozeným rokem a kvartálem a jsou odstraněny řádky bez data nebo regionu.
- Agregací den do kvartálů vzniká price_quarterly_raw. Z price_with_quarter odvozuji průměrné ceny potravin za kvartál, a to pro kombinaci všech year, quarter a category_code a to pomocí fce AVG(hodnota) AS avg_price_raw. Důvodem je to, že potřebuji jednu cenu potraviny, která reprezentuje jak kvartál tak kategorii potravin (pro porovnání s mzdami.
- Dalším krokem je vytvoření payroll_clean. Z czechia_payroll vybírám jen value_type_code = 5958 a calculation_code = 100, a informace, kde industry_branch_code IS NOT NULL. Z payroll_year dělám year a z payroll_quarter se stává quarter. Výsledkem je řádek reprezentující 1 rok, 1 kvartál, 1 průmyslové odvětví a 1 mzdový ukazatel. Odstraňuji NULL hodnoty.
- Roční průměrná mzda podle průmyslového odvětví je pojmenována jako payroll_yearly_avg_branch_raw, počítáno z payroll_clean jako průměr všech kvartálů daného roku fcí AVG(hodnota) GROUP BY year, industry_branch_code. avg_wage_yearly_branch_raw je pak oční průměr mezd daného průmyslového období.
Z payroll_clean také počítám celkový roční průměr všech mezd (payroll_yearly_avg_overall_raw) fcí AVG(hodnota) GROUP BY year. avg_wage_yearly_overall_raw je pak průměrná mzda v celé ČR.
- Z daných průměrů za kvartály (price_quarterly_raw) počítám price_yearly_by_cat_raw jakožto roční průměrnou cenu pro každou kategorii potravin pomocí AVG(avg_price_raw) GROUP BY year, category_code pro pozdější možnost sledování meziročního růstu (YoY). Vzniká avg_price_yearly_raw – roční průměrná cena potraviny.
- Na roční průměrné ceny (price_yearly_by_cat_raw) aplikuji funkci LAG(), čímž zjistím cenu v předchozím roce pro danou kategorii potravin (yoy) a získávám price_yearly_by_cat_yoy_raw, a to skrze prev_price_yearly_raw = LAG(avg_price_yearly_raw) OVER (PARTITION BY category ORDER BY year). Z toho počítám yoy v procentech: price_yoy_raw = ((avg_price_yearly_raw - prev_price_yearly_raw) / prev_price_yearly_raw) * 100. Jelikož neexistuje předchozí hodnota pro první rok každé kategorie potravin, má price_yoy_raw = NULL.
- Z price_yearly_by_cat_raw dále vytvořím agregovaný potravinový index food_year. avg_food_price_yearly_raw je pak průměr ročních cen všech kategorií potravin. Twnto index spojím s payroll_yearly_avg_overall_raw a přes fci LAG() získám yoy růst potravinového indexu food_yoy_raw a yoy růst průměrné mzdy wage_yoy_raw.  Tím získám yoy růst cen a mezd food_and_wage_yearly_raw.
- Všechny mezivýsledky spojím do finální tabulky data_academy_content.t_michaela_ticha_project_sql_primary_final, a zaokrouhlím všechny hodnoty na dvě desetinná čísla. 
- Posledním krokem je flagování chleba a mléka, a to vytvořením dvou BOOLEAN sloupců. 

## Postup v sekundárním SQL skriptu
- Přímo ve skriptu se žádné rozdělení na kroky nenachází, jelikož je kód podstatněji kratší. Proto jen obecně popíšu postup:
  
**1. KROK** - Zdrojem dat je dataset *economies*, ze kterého vybírám potřebné sloupce country, year, gdp, population a gini.

**2. KROK** - Filtruji období mezi lety 2006 a 2018 a pouze EU státy. Evropské státy musím filtrovat ručně, protože jsou ve sloupci country i celé regiony nebo jiné kategorie zemí.

**3. KROK** - Seřadím tabulku podle country a year a vytvořím finální sekundární tabulku t_michaela_ticha_project_sql_secondary_final.

## Odpovědi na výzkumné otázky
# VO1 Rostou v průběhu let mzdy ve všech odvětvích, nebo v některých klesají?
- Mzdy nerostou rovnoměrně ve všech odvětvích, jsou i takové odvětví, kde průměrná roční mzda meziročně klesla. Tabulka, která byla vytvořena po vyvolání SQL sady k zobrazení podkladů pro zodpovězení VO1 obsahuje v druhém sloupci počet let s poklesem mezd v daném odvětví. V odvětví „Těžba a dobývání“ došlo k nejvyššímu počtu poklesů za rok, a to během 4 let. Další odvětví, jako například „Kulturní, zábavní a rekreační činnosti“, „Veřejná správa a obrana“ nebo „Výroba a rozvod elektřiny, plynu, tepla a klimatiz. vzduchu“, zaznamenala pokles dvakrát. Naopak některá odvětví nezaznamenala žádný pokles, například „Ostatní činnosti“ nebo „Zpracovatelský průmysl“. Mzdy tedy v ČR dlouhodobě rostou, ale ne rovnoměrně napříč všemi odvětvími.

# VO2 Kolik je možné si koupit litrů mléka a kilogramů chleba za první a poslední srovnatelné období v dostupných datech cen a mezd?
- V roce 2006 byla průměrná mzda 20 342,38 Kč a průměrná cena chleba 16,17 Kč/kg. Za tuto mzdu si bylo možné koupit přibližně 1 258 kg chleba. V roce 2018 průměrná mzda činila 31 980,26 Kč a cena chleba vzrostla na 24,24 Kč/kg. I přesto si v roce 2018 si člověk mohl koupit z jedné mzdy 1 319 kg chleba. To ukazuje, že mzdy rostly rychleji než cena chleba.
- V případě mléka byla v roce 2006 průměrná cena 14,44 Kč/l, což znamená, že si člověk v daném roce mohl koupit za průměrnou mzdu cca 1 409 litrů mléka. V roce 2018 cena mléka vzrostla na 19,82 Kč/litr, což by zajistilo 1 614 litrů mléka. 

# VO3 Která kategorie potravin zdražuje nejpomaleji (je u ní nejnižší percentuální meziroční nárůst)?
- Potravina s nejnižším meziročním růstem ceny je „Cukr krystalový“ (category_code: 118101). Výsledná hodnota průměrné meziroční změny ceny činí –1,92 %, což znamená, že tato potravina nejenže nezdražuje, ale v průměru dokonce zlevňuje. Tato hodnota je vypočtena jako průměr všech meziročních procentuálních změn ceny cukru v období let 2006–2018, přičemž byly ignorovány roky bez relevantních dat (NULL). To znamená, že ve sledovaném období se cena krystalového cukru dlouhodobě pohybovala pod hodnotami předchozích let a vývoj cen tak u této položky šel proti celkovému trendu růstu cen potravin. 

# VO4 Existuje rok, ve kterém byl meziroční nárůst cen potravin výrazně vyšší než růst mezd (větší než 10 %)?
- V celém sledovaném období neexistuje rok, ve kterém by meziroční růst cen potravin převýšil růst mezd o více než 10 procent. Výsledek dotazu je prázdná tabulka – to znamená, že v žádném roce nenastala situace, kdy by ceny potravin rostly rychleji než mzdy. Dá se tedy říci, že i v letech, kdy ceny potravin rostly, mzdy rostly buď podobným tempem, nebo je předstihly. V období 2006–2018 nedošlo k situaci, že by potraviny nebyly dostupné skrze nepoměr mezi růstem cen potravin a růstem mezd.

# VO5 Má výška HDP vliv na změny ve mzdách a cenách potravin? Neboli, pokud HDP vzroste výrazněji v jednom roce, projeví se to na cenách potravin či mzdách ve stejném nebo následujícím roce výraznějším růstem?
- Nelze potvrdit přímou kauzalitu mezi růstem HDP a růstem mezd či cen potravin v České republice v období 2006–2018. HDP během celého období spíše rostlo, ale jeho trajektorie kolísala podle globální ekonomické situace. V prvním sledovaném období 2006 z 197,47 miliard Kč vzrostl na 253,04 miliard Kč pro poslední sledované období 2018. Naopak, růst cen potravin a mezd nevykazoval žádný sledovatelné nebo předvídatelné směry vývoje. U cen potravin vidíme, že yoy kolísají bez nějaké vazby na HDP. Například v roce 2014 došlo k poklesu tempa růstu cen potravin na 0,74 %, přestože HDP rostlo. Prudký nárust pak vidíme v roce 2017 (9,63 %), ale HDP tohoto roku lineárně pokračoval korespondenčně s růstovým trendem. Můžeme tedy tvrdit, že ceny potravin jsou spíše ovlivňovány jiným faktorem, než HDP. To stejné je viditelné u mezd, jejichž yoy ve většině let zaznamenala průměrový růst. Mezi lety 2016 a 2017 vzrostly mzdy o 6,40 %, což je vyšší tempo než v mnoha předchozích letech, a přesto nebylo doprovázeno mimořádným růstem HDP. Mezi lety 2007 a 2008 také došlo k výraznému nárůstu mezd (8,06 %), ale není zde patrný dramatický nárust HDP. Poklesy HDP se sice v některých obdobích projevily i na cenách potravin a mzdách, ale není z toho patrný žádný vzorec.
