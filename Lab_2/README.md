# Основы обработки данных с помощью R
Турбина Виктория

## Цель работы

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Развить пркатические навыки использования функций обработки данных
    пакета dplyr – функции select(), filter(), mutate(), arrange(),
    group_by()

## Ход работы

1.  Сколько строк в датафрейме?

``` r
library('dplyr')
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

``` r
starwars %>% nrow()
```

    [1] 87

1.  Сколько столбцов в датафрейме?

``` r
starwars %>% ncol()
```

    [1] 14

1.  Как просмотреть примерный вид датафрейма?

``` r
starwars %>% glimpse()
```

    Rows: 87
    Columns: 14
    $ name       <chr> "Luke Skywalker", "C-3PO", "R2-D2", "Darth Vader", "Leia Or…
    $ height     <int> 172, 167, 96, 202, 150, 178, 165, 97, 183, 182, 188, 180, 2…
    $ mass       <dbl> 77.0, 75.0, 32.0, 136.0, 49.0, 120.0, 75.0, 32.0, 84.0, 77.…
    $ hair_color <chr> "blond", NA, NA, "none", "brown", "brown, grey", "brown", N…
    $ skin_color <chr> "fair", "gold", "white, blue", "white", "light", "light", "…
    $ eye_color  <chr> "blue", "yellow", "red", "yellow", "brown", "blue", "blue",…
    $ birth_year <dbl> 19.0, 112.0, 33.0, 41.9, 19.0, 52.0, 47.0, NA, 24.0, 57.0, …
    $ sex        <chr> "male", "none", "none", "male", "female", "male", "female",…
    $ gender     <chr> "masculine", "masculine", "masculine", "masculine", "femini…
    $ homeworld  <chr> "Tatooine", "Tatooine", "Naboo", "Tatooine", "Alderaan", "T…
    $ species    <chr> "Human", "Droid", "Droid", "Human", "Human", "Human", "Huma…
    $ films      <list> <"The Empire Strikes Back", "Revenge of the Sith", "Return…
    $ vehicles   <list> <"Snowspeeder", "Imperial Speeder Bike">, <>, <>, <>, "Imp…
    $ starships  <list> <"X-wing", "Imperial shuttle">, <>, <>, "TIE Advanced x1",…

1.  Сколько уникальных рас персонажей (species) представлено в данных?

``` r
starwars %>% group_by(species) %>% summarize(total = n()) %>% nrow()
```

    [1] 38

1.  Найти самого высокого персонажа.

``` r
starwars %>% arrange(desc(height)) %>% select(name) %>% head(1)
```

    # A tibble: 1 × 1
      name       
      <chr>      
    1 Yarael Poof

1.  Найти всех персонажей ниже 170.

``` r
starwars %>% filter(height < 170)
```

    # A tibble: 23 × 14
       name     height  mass hair_color skin_color eye_color birth_year sex   gender
       <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
     1 C-3PO       167    75 <NA>       gold       yellow           112 none  mascu…
     2 R2-D2        96    32 <NA>       white, bl… red               33 none  mascu…
     3 Leia Or…    150    49 brown      light      brown             19 fema… femin…
     4 Beru Wh…    165    75 brown      light      blue              47 fema… femin…
     5 R5-D4        97    32 <NA>       white, red red               NA none  mascu…
     6 Yoda         66    17 white      green      brown            896 male  mascu…
     7 Mon Mot…    150    NA auburn     fair       blue              48 fema… femin…
     8 Wicket …     88    20 brown      brown      brown              8 male  mascu…
     9 Nien Nu…    160    68 none       grey       black             NA male  mascu…
    10 Watto       137    NA black      blue, grey yellow            NA male  mascu…
    # ℹ 13 more rows
    # ℹ 5 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>

1.  Подсчитать ИМТ (индекс массы тела) для всех персонажей.

``` r
starwars %>% mutate('ИМТ' = mass/(height * height))
```

    # A tibble: 87 × 15
       name     height  mass hair_color skin_color eye_color birth_year sex   gender
       <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
     1 Luke Sk…    172    77 blond      fair       blue            19   male  mascu…
     2 C-3PO       167    75 <NA>       gold       yellow         112   none  mascu…
     3 R2-D2        96    32 <NA>       white, bl… red             33   none  mascu…
     4 Darth V…    202   136 none       white      yellow          41.9 male  mascu…
     5 Leia Or…    150    49 brown      light      brown           19   fema… femin…
     6 Owen La…    178   120 brown, gr… light      blue            52   male  mascu…
     7 Beru Wh…    165    75 brown      light      blue            47   fema… femin…
     8 R5-D4        97    32 <NA>       white, red red             NA   none  mascu…
     9 Biggs D…    183    84 black      light      brown           24   male  mascu…
    10 Obi-Wan…    182    77 auburn, w… fair       blue-gray       57   male  mascu…
    # ℹ 77 more rows
    # ℹ 6 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>, ИМТ <dbl>

1.  Найти 10 самых “вытянутых” персонажей. “Вытянутость” оценить по
    отношению массы (mass) к росту (height) персонажей.

``` r
starwars %>% mutate('вытянутость' = mass/height) %>% arrange(desc(вытянутость)) %>% head(10)
```

    # A tibble: 10 × 15
       name     height  mass hair_color skin_color eye_color birth_year sex   gender
       <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
     1 Jabba D…    175  1358 <NA>       green-tan… orange         600   herm… mascu…
     2 Grievous    216   159 none       brown, wh… green, y…       NA   male  mascu…
     3 IG-88       200   140 none       metal      red             15   none  mascu…
     4 Owen La…    178   120 brown, gr… light      blue            52   male  mascu…
     5 Darth V…    202   136 none       white      yellow          41.9 male  mascu…
     6 Jek Ton…    180   110 brown      fair       blue            NA   male  mascu…
     7 Bossk       190   113 none       green      red             53   male  mascu…
     8 Tarfful     234   136 brown      brown      blue            NA   male  mascu…
     9 Dexter …    198   102 none       brown      yellow          NA   male  mascu…
    10 Chewbac…    228   112 brown      unknown    blue           200   male  mascu…
    # ℹ 6 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>, вытянутость <dbl>

1.  Найти средний возраст персонажей каждой расы вселенной Звездных
    войн.

``` r
starwars %>% filter( birth_year != 'NA', species != 'NA')%>% group_by(species) %>%  summarize(средний = mean(birth_year))
```

    # A tibble: 15 × 2
       species        средний
       <chr>            <dbl>
     1 Cerean            92  
     2 Droid             53.3
     3 Ewok               8  
     4 Gungan            52  
     5 Human             53.4
     6 Hutt             600  
     7 Kel Dor           22  
     8 Mirialan          49  
     9 Mon Calamari      41  
    10 Rodian            44  
    11 Trandoshan        53  
    12 Twi'lek           48  
    13 Wookiee          200  
    14 Yoda's species   896  
    15 Zabrak            54  

1.  Найти самый распространенный цвет глаз персонажей вселенной Звездных
    войн.

``` r
starwars %>% group_by(eye_color) %>% summarize(total = n()) %>% arrange(desc(total))%>% select(eye_color)%>% head(1)
```

    # A tibble: 1 × 1
      eye_color
      <chr>    
    1 brown    

1.  Подсчитать среднюю длину имени в каждой расе вселенной Звездных
    войн.

``` r
starwars %>% group_by(species) %>%  summarize(средний = mean(nchar(name)))
```

    # A tibble: 38 × 2
       species   средний
       <chr>       <dbl>
     1 Aleena      13   
     2 Besalisk    15   
     3 Cerean      12   
     4 Chagrian    10   
     5 Clawdite    10   
     6 Droid        4.83
     7 Dug          7   
     8 Ewok        21   
     9 Geonosian   17   
    10 Gungan      11.7 
    # ℹ 28 more rows

## Вывод

Таким образом, используя программный пакет dplyr, удалось развить
практические навыки использования языка программирования R для обработки
данных.
