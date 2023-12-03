# Исследование информации о состоянии беспроводных сетей
Турбина Виктория

## Цель работы

1.  Получить знания о методах исследования радиоэлектронной обстановки.

2.  Составить представление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI.

3.  Зекрепить практические навыки использования языка программирования R
    для обработки данных

4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R

## Ход работы

### Подготовка данных

1.  Импортируйте данные.

``` r
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

``` r
dataset <- read.csv("C:/Users/Asus/Downloads/Telegram Desktop/mir.csv-01/mir.csv-01.csv")
```

``` r
dataset_1 <- read.csv(file="C:/Users/Asus/Downloads/Telegram Desktop/mir.csv-01/mir.csv-01.csv",nrows=167)
```

``` r
dataset_2 <- read.csv(file="C:/Users/Asus/Downloads/Telegram Desktop/mir.csv-01/mir.csv-01.csv",skip=169)
```

1.  Привести датасеты в вид “аккуратных данных”, преобразовать типы
    столбцов в соответствии с типом данных

2.  Просмотрите общую структуру данных с помощью функции glimpse()

``` r
dataset_1 %>% glimpse()
```

    Rows: 167
    Columns: 15
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1A", "9A:75:A8:B9…
    $ First.time.seen <chr> " 2023-07-28 09:13:03", " 2023-07-28 09:13:03", " 2023…
    $ Last.time.seen  <chr> " 2023-07-28 11:50:50", " 2023-07-28 11:55:12", " 2023…
    $ channel         <int> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14, 11, 11, 6, 6, …
    $ Speed           <int> 195, 130, 360, 360, 130, 130, 195, 130, 130, 195, 180,…
    $ Privacy         <chr> " WPA2", " WPA2", " WPA2", " WPA2", " WPA2", " OPN", "…
    $ Cipher          <chr> " CCMP", " CCMP", " CCMP", " CCMP", " CCMP", " ", " CC…
    $ Authentication  <chr> " PSK", " PSK", " PSK", " PSK", " PSK", "   ", " PSK",…
    $ Power           <int> -30, -30, -68, -37, -57, -63, -27, -38, -38, -66, -42,…
    $ X..beacons      <int> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 13…
    $ X..IV           <int> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, …
    $ LAN.IP          <chr> "   0.  0.  0.  0", "   0.  0.  0.  0", "   0.  0.  0.…
    $ ID.length       <int> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 1…
    $ ESSID           <chr> " C322U13 3965", " Cnet", " KC", " POCO X5 Pro 5G", " …
    $ Key             <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…

``` r
dataset_2 %>% glimpse()
```

    Rows: 12,269
    Columns: 7
    $ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E…
    $ First.time.seen <chr> " 2023-07-28 09:13:03", " 2023-07-28 09:13:03", " 2023…
    $ Last.time.seen  <chr> " 2023-07-28 10:59:44", " 2023-07-28 09:13:03", " 2023…
    $ Power           <chr> " -33", " -65", " -39", " -61", " -53", " -43", " -31"…
    $ X..packets      <chr> "      858", "        4", "      432", "      958", " …
    $ BSSID           <chr> " BE:F1:71:D5:17:8B", " (not associated) ", " BE:F1:71…
    $ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U…

### Анализ

#### Точки доступа

1.  Определить небезопасные точки доступа (без шифрования – OPN)

``` r
dataset_1 %>% filter(Privacy == ' OPN')
```

                   BSSID      First.time.seen       Last.time.seen channel Speed
    1  E8:28:C1:DC:B2:52  2023-07-28 09:13:03  2023-07-28 11:55:38       6   130
    2  E8:28:C1:DC:B2:50  2023-07-28 09:13:06  2023-07-28 11:55:12       6   130
    3  E8:28:C1:DC:B2:51  2023-07-28 09:13:06  2023-07-28 11:55:11       6   130
    4  E8:28:C1:DC:FF:F2  2023-07-28 09:13:06  2023-07-28 11:55:10       6    -1
    5  00:25:00:FF:94:73  2023-07-28 09:13:06  2023-07-28 11:56:21      44    -1
    6  E8:28:C1:DD:04:52  2023-07-28 09:13:09  2023-07-28 11:56:05      11   130
    7  E8:28:C1:DE:74:31  2023-07-28 09:13:13  2023-07-28 10:27:06       6   130
    8  E8:28:C1:DE:74:32  2023-07-28 09:13:13  2023-07-28 10:39:43       6   130
    9  E8:28:C1:DC:C8:32  2023-07-28 09:13:17  2023-07-28 11:52:32       1   130
    10 E8:28:C1:DD:04:50  2023-07-28 09:13:50  2023-07-28 11:43:39      11   130
    11 E8:28:C1:DD:04:51  2023-07-28 09:13:57  2023-07-28 10:48:00      11   130
    12 E8:28:C1:DC:C8:30  2023-07-28 09:15:45  2023-07-28 11:36:30       1   130
    13 E8:28:C1:DE:74:30  2023-07-28 09:17:45  2023-07-28 09:26:13       6    -1
    14 E0:D9:E3:48:FF:D2  2023-07-28 09:17:53  2023-07-28 10:51:37       7    -1
    15 E8:28:C1:DC:B2:41  2023-07-28 09:18:16  2023-07-28 11:36:43      48   360
    16 E8:28:C1:DC:B2:40  2023-07-28 09:18:16  2023-07-28 11:51:48      48   360
    17 00:26:99:F2:7A:E0  2023-07-28 09:18:17  2023-07-28 11:01:55       1    -1
    18 E8:28:C1:DC:B2:42  2023-07-28 09:18:30  2023-07-28 11:43:23      48   360
    19 E8:28:C1:DD:04:40  2023-07-28 09:18:30  2023-07-28 11:55:10      52   360
    20 E8:28:C1:DD:04:41  2023-07-28 09:18:30  2023-07-28 11:55:10      52   360
    21 E8:28:C1:DE:47:D2  2023-07-28 09:20:37  2023-07-28 11:51:18       1   130
    22 02:BC:15:7E:D5:DC  2023-07-28 09:24:46  2023-07-28 09:24:48      11   130
    23 E8:28:C1:DC:C6:B1  2023-07-28 09:32:42  2023-07-28 11:52:32       6   130
    24 E8:28:C1:DD:04:42  2023-07-28 09:34:57  2023-07-28 11:53:35      52   360
    25 E8:28:C1:DC:C8:31  2023-07-28 09:36:31  2023-07-28 11:36:30       1   130
    26 E8:28:C1:DE:47:D1  2023-07-28 09:40:06  2023-07-28 09:40:06       1   130
    27 00:AB:0A:00:10:10  2023-07-28 09:46:33  2023-07-28 11:15:49      60    -1
    28 E8:28:C1:DC:C6:B0  2023-07-28 09:59:19  2023-07-28 11:03:58       6   130
    29 E8:28:C1:DC:C6:B2  2023-07-28 10:02:42  2023-07-28 11:56:21       6    -1
    30 E8:28:C1:DC:BD:50  2023-07-28 10:16:31  2023-07-28 11:02:14      11   130
    31 E8:28:C1:DC:0B:B2  2023-07-28 10:20:20  2023-07-28 10:20:20      11    -1
    32 E8:28:C1:DC:33:12  2023-07-28 10:28:36  2023-07-28 10:51:35      11    -1
    33 00:03:7A:1A:03:56  2023-07-28 10:29:13  2023-07-28 10:29:19      11   130
    34 00:03:7F:12:34:56  2023-07-28 10:30:23  2023-07-28 10:30:23       6   130
    35 00:3E:1A:5D:14:45  2023-07-28 10:34:03  2023-07-28 10:34:05      11   130
    36 E0:D9:E3:49:00:B1  2023-07-28 10:35:31  2023-07-28 10:35:31       1   130
    37 E8:28:C1:DC:BD:52  2023-07-28 10:36:14  2023-07-28 10:36:14      11   130
    38 00:26:99:F2:7A:EF  2023-07-28 11:10:12  2023-07-28 11:10:12      44    -1
    39 02:67:F1:B0:6C:98  2023-07-28 11:16:05  2023-07-28 11:26:56      11   130
    40 02:CF:8B:87:B4:F9  2023-07-28 11:30:11  2023-07-28 11:30:11      11   130
    41 00:53:7A:99:98:56  2023-07-28 11:48:14  2023-07-28 11:48:14      11   130
    42 E8:28:C1:DE:47:D0  2023-07-28 11:55:11  2023-07-28 11:55:11       1   130
       Privacy Cipher Authentication Power X..beacons X..IV           LAN.IP
    1      OPN                         -63        251  3430  172. 17.203.197
    2      OPN                         -63        260   907  172. 17. 95.169
    3      OPN                         -63        279     0    0.  0.  0.  0
    4      OPN                          -1          0     3  172. 17. 84.217
    5      OPN                          -1          0     5    0.  0.  0.  0
    6      OPN                         -67          4   304  172. 17.216.149
    7      OPN                         -82          2     0    0.  0.  0.  0
    8      OPN                         -69          1     2  172. 17. 93.250
    9      OPN                         -69         12    71  192.168.155. 53
    10     OPN                         -78         20    35  192.168.  0.  1
    11     OPN                         -73          9     0    0.  0.  0.  0
    12     OPN                         -71          7    21  172. 17.105.120
    13     OPN                         -78          0     6    0.  0.  0.  0
    14     OPN                          -1          0     2  192.168. 14.235
    15     OPN                         -89          5    23  169.254.175.203
    16     OPN                         -88          5    89  172. 17.203.197
    17     OPN                          -1          0     2    0.  0.  0.  0
    18     OPN                         -87          5     0    0.  0.  0.  0
    19     OPN                         -84         30   171  172. 17. 84.175
    20     OPN                         -83         25   282  172. 17. 84.175
    21     OPN                         -71          3    31    0.  0.  0.  0
    22     OPN                         -67          1     0    0.  0.  0.  0
    23     OPN                         -78          5     0    0.  0.  0.  0
    24     OPN                         -81         23     0    0.  0.  0.  0
    25     OPN                         -73          8     0    0.  0.  0.  0
    26     OPN                         -76          1     0    0.  0.  0.  0
    27     OPN                          -1          0    20    0.  0.  0.  0
    28     OPN                         -71          4     5    0.  0.  0.  0
    29     OPN                         -70          0    34  192.168.  0.  1
    30     OPN                         -75          5     0    0.  0.  0.  0
    31     OPN                         -82          0     1    0.  0.  0.  0
    32     OPN                         -82          0     1    0.  0.  0.  0
    33     OPN                         -68          1     0    0.  0.  0.  0
    34     OPN                         -76          1     0    0.  0.  0.  0
    35     OPN                         -57          1     0    0.  0.  0.  0
    36     OPN                         -70          1     0    0.  0.  0.  0
    37     OPN                         -72          1     0    0.  0.  0.  0
    38     OPN                          -1          0     1    0.  0.  0.  0
    39     OPN                         -68          1     0    0.  0.  0.  0
    40     OPN                         -70          1     0    0.  0.  0.  0
    41     OPN                         -68          0     0    0.  0.  0.  0
    42     OPN                         -70          1     0    0.  0.  0.  0
       ID.length          ESSID Key
    1         13  MIREA_HOTSPOT  NA
    2         12   MIREA_GUESTS  NA
    3          0                 NA
    4          0                 NA
    5          0                 NA
    6         13  MIREA_HOTSPOT  NA
    7          0                 NA
    8         13  MIREA_HOTSPOT  NA
    9         13  MIREA_HOTSPOT  NA
    10        12   MIREA_GUESTS  NA
    11         0                 NA
    12        12   MIREA_GUESTS  NA
    13         0                 NA
    14         0                 NA
    15        12   MIREA_GUESTS  NA
    16        13  MIREA_HOTSPOT  NA
    17         0                 NA
    18         0                 NA
    19        13  MIREA_HOTSPOT  NA
    20        12   MIREA_GUESTS  NA
    21        13  MIREA_HOTSPOT  NA
    22         7        MT_FREE  NA
    23         0                 NA
    24         0                 NA
    25         0                 NA
    26         0                 NA
    27         0                 NA
    28        12   MIREA_GUESTS  NA
    29         0                 NA
    30        12   MIREA_GUESTS  NA
    31         0                 NA
    32         0                 NA
    33         7        MT_FREE  NA
    34         7        MT_FREE  NA
    35         7        MT_FREE  NA
    36         0                 NA
    37        13  MIREA_HOTSPOT  NA
    38         0                 NA
    39         7        MT_FREE  NA
    40         7        MT_FREE  NA
    41         7        MT_FREE  NA
    42        12   MIREA_GUESTS  NA

1.  Определить производителя для каждого обнаруженного устройства

<!-- -->

1.  Выявить устройства, использующие последнюю версию протокола
    шифрования WPA3, и названия точек доступа, реализованных на этих
    устройствах

``` r
dataset_1 %>% filter(Privacy == " WPA3 WPA2")
```

                  BSSID      First.time.seen       Last.time.seen channel Speed
    1 26:20:53:0C:98:E8  2023-07-28 09:15:45  2023-07-28 09:33:10      44   866
    2 A2:FE:FF:B8:9B:C9  2023-07-28 09:41:52  2023-07-28 09:41:52       6   130
    3 96:FF:FC:91:EF:64  2023-07-28 09:52:54  2023-07-28 10:25:02      44   866
    4 CE:48:E7:86:4E:33  2023-07-28 09:59:20  2023-07-28 10:04:15      44   866
    5 8E:1F:94:96:DA:FD  2023-07-28 10:08:32  2023-07-28 10:15:27      44   866
    6 BE:FD:EF:18:92:44  2023-07-28 10:15:24  2023-07-28 10:15:28       6   130
    7 3A:DA:00:F9:0C:02  2023-07-28 10:27:01  2023-07-28 10:27:10       6   130
    8 76:C5:A0:70:08:96  2023-07-28 11:16:36  2023-07-28 11:16:38       6   130
         Privacy Cipher Authentication Power X..beacons X..IV           LAN.IP
    1  WPA3 WPA2   CCMP        SAE PSK   -85          3     0    0.  0.  0.  0
    2  WPA3 WPA2   CCMP        SAE PSK   -70          1     0    0.  0.  0.  0
    3  WPA3 WPA2   CCMP        SAE PSK   -85          9     0    0.  0.  0.  0
    4  WPA3 WPA2   CCMP        SAE PSK   -65          9     0    0.  0.  0.  0
    5  WPA3 WPA2   CCMP        SAE PSK   -67         12     0    0.  0.  0.  0
    6  WPA3 WPA2   CCMP        SAE PSK   -64          0     0    0.  0.  0.  0
    7  WPA3 WPA2   CCMP        SAE PSK   -65          5     0    0.  0.  0.  0
    8  WPA3 WPA2   CCMP        SAE PSK   -52          1     0    0.  0.  0.  0
      ID.length               ESSID Key
    1        10                      NA
    2        12          Christie’s  NA
    3        10                      NA
    4        27  iPhone (Анастасия)  NA
    5        27  iPhone (Анастасия)  NA
    6        15            Димасик   NA
    7        26   iPhone XS Max 🦊🐱🦊  NA
    8        11                      NA

1.  Отсортировать точки доступа по интервалу времени, в течение которого
    они находились на связи, по убыванию.

``` r
a <- difftime(dataset_1$Last.time.seen, dataset_1$First.time.seen, units = "secs")
time_1 <- dataset_1
time_1 <- cbind(time_1,a)
time_1 %>% select(BSSID,ESSID, a) %>% arrange(desc(a))
```

                    BSSID                        ESSID         a
    1   00:25:00:FF:94:73                              9795 secs
    2   E8:28:C1:DD:04:52                MIREA_HOTSPOT 9776 secs
    3   E8:28:C1:DC:B2:52                MIREA_HOTSPOT 9755 secs
    4   08:3A:2F:56:35:FE                              9746 secs
    5   6E:C7:EC:16:DA:1A                         Cnet 9729 secs
    6   E8:28:C1:DC:B2:50                 MIREA_GUESTS 9726 secs
    7   E8:28:C1:DC:B2:51                              9725 secs
    8   48:5B:39:F9:7A:48                              9725 secs
    9   E8:28:C1:DC:FF:F2                              9724 secs
    10  8E:55:4A:85:5B:01                     Vladimir 9723 secs
    11  00:26:99:BA:75:80                         GIVC 9710 secs
    12  00:26:99:F2:7A:E2                         GIVC 9707 secs
    13  1E:93:E3:1B:3C:F4                   Galaxy A71 9633 secs
    14  9A:75:A8:B9:04:1E                           KC 9628 secs
    15  0C:80:63:A9:6E:EE                              9628 secs
    16  00:23:EB:E3:81:F2                         GIVC 9595 secs
    17  9E:A3:A9:DB:7E:01                              9555 secs
    18  E8:28:C1:DC:C8:32                MIREA_HOTSPOT 9555 secs
    19  1C:7E:E5:8E:B7:DE                              9524 secs
    20  00:26:99:F2:7A:E1                          IKB 9492 secs
    21  BE:F1:71:D5:17:8B                 C322U13 3965 9467 secs
    22  BE:F1:71:D6:10:D7                 C322U21 0566 9461 secs
    23  9E:A3:A9:D6:28:3C                              9451 secs
    24  E8:28:C1:DD:04:40                MIREA_HOTSPOT 9400 secs
    25  E8:28:C1:DD:04:41                 MIREA_GUESTS 9400 secs
    26  00:23:EB:E3:81:F1                          IKB 9348 secs
    27  00:23:EB:E3:81:FE                          IKB 9305 secs
    28  00:23:EB:E3:81:FD                         GIVC 9305 secs
    29  9E:A3:A9:BF:12:C0                              9270 secs
    30  E8:28:C1:DC:B2:40                MIREA_HOTSPOT 9212 secs
    31  AA:F4:3F:EE:49:0B             Redmi Note 8 Pro 9045 secs
    32  E8:28:C1:DE:47:D2                MIREA_HOTSPOT 9041 secs
    33  E8:28:C1:DD:04:50                 MIREA_GUESTS 8989 secs
    34  14:EB:B6:6A:76:37              Gnezdo_lounge 2 8915 secs
    35  56:99:98:EE:5A:4E                tementy-phone 8811 secs
    36  E8:28:C1:DC:B2:42                              8693 secs
    37  38:1A:52:0D:90:A1     EBFCD597-EE81fI_DMN1AOe1 8661 secs
    38  0A:C5:E1:DB:17:7B                AndroidAP177B 8608 secs
    39  E8:28:C1:DC:C8:30                 MIREA_GUESTS 8445 secs
    40  E8:28:C1:DC:C6:B1                              8390 secs
    41  E8:28:C1:DD:04:42                              8318 secs
    42  E8:28:C1:DC:B2:41                 MIREA_GUESTS 8307 secs
    43  12:51:07:FF:29:D6         DESKTOP-KITJO8R 5262 7483 secs
    44  CE:B3:FF:84:45:FC                              7271 secs
    45  E8:28:C1:DC:C8:31                              7199 secs
    46  E8:28:C1:DC:C6:B2                              6819 secs
    47  4A:EC:1E:DB:BF:95               POCO X5 Pro 5G 6658 secs
    48  00:26:99:F2:7A:E0                              6218 secs
    49  E8:28:C1:DD:04:51                              5643 secs
    50  E0:D9:E3:48:FF:D2                              5624 secs
    51  00:AB:0A:00:10:10                              5356 secs
    52  E8:28:C1:DE:74:32                MIREA_HOTSPOT 5190 secs
    53  10:50:72:00:11:08               MGTS_GPON_B563 4997 secs
    54  EA:D8:D1:77:C8:08      DIRECT-08-HP M428fdw LJ 4995 secs
    55  D2:6D:52:61:51:5D                              4636 secs
    56  E0:D9:E3:49:04:52                              4614 secs
    57  7E:3A:10:A7:59:4E                     vivo Y21 4611 secs
    58  BE:F1:71:D5:0E:53                 C322U06 9080 4578 secs
    59  A6:02:B9:73:83:18                 C239U52 6706 4577 secs
    60  9A:9F:06:44:24:5B        Long Huong Galaxy M12 4572 secs
    61  E8:28:C1:DE:74:31                              4433 secs
    62  92:F5:7B:43:0B:69                     Redmi 12 4392 secs
    63  E8:28:C1:DC:3C:92                              4331 secs
    64  38:1A:52:0D:84:D7     EBFCD57F-EE81fI_DL_1AO2T 4319 secs
    65  38:1A:52:0D:90:5D     EBFCD615-EE81fI_DOL1AO8w 4255 secs
    66  A2:64:E8:97:58:EE                     Mi Phone 4252 secs
    67  A6:02:B9:73:81:47                 C239U53 6056 4224 secs
    68  56:C5:2B:9F:84:90                   OnePlus 6T 4173 secs
    69  A6:02:B9:73:2F:76                 C239U51 0701 4144 secs
    70  38:1A:52:0D:97:60     EBFCD593-EE81fI_DMJ1AOI4 4086 secs
    71  0A:24:D8:D9:24:70                    IgorKotya 4071 secs
    72  E8:28:C1:DC:C6:B0                 MIREA_GUESTS 3879 secs
    73  8A:A3:03:73:52:08              Galaxy A30s5208 3451 secs
    74  5E:C7:C0:E4:D7:D4                    realme 10 3265 secs
    75  E8:28:C1:DC:54:72                              3074 secs
    76  4A:86:77:04:B7:28            iPhone (Искандер) 3008 secs
    77  B6:C4:55:B5:53:24                      Redmi 9 2987 secs
    78  E8:28:C1:DC:BD:50                 MIREA_GUESTS 2743 secs
    79  76:70:AF:A4:D2:AF                         витя 2733 secs
    80  86:DF:BF:E4:2F:23         DESKTOP-Q7R0KRV 2433 2688 secs
    81  38:1A:52:0D:8F:EC     EBFCD6C2-EE81fI_DR21AOa0 2635 secs
    82  EA:7B:9B:D8:56:34                     POCO C40 2241 secs
    83  38:1A:52:0D:85:1D     EBFCD5C1-EE81fI_DN11AOcY 2082 secs
    84  00:26:CB:AA:62:71                          IKB 1969 secs
    85  96:FF:FC:91:EF:64                              1928 secs
    86  E8:28:C1:DC:33:12                              1379 secs
    87  E8:28:C1:DC:F0:90                              1312 secs
    88  3A:70:96:C6:30:2C            iPhone (Искандер) 1300 secs
    89  36:46:53:81:12:A0           GGWPRedmi Note 10S 1248 secs
    90  CE:C3:F7:A4:7E:B3                 DIRECT-A6-HP 1224 secs
    91  26:20:53:0C:98:E8                              1045 secs
    92  92:12:38:E5:7E:1E                               868 secs
    93  E8:28:C1:DC:33:10                               846 secs
    94  E8:28:C1:DB:F5:F0                 MIREA_GUESTS  842 secs
    95  E8:28:C1:DC:0B:B0                               832 secs
    96  E8:28:C1:DB:F5:F2                               782 secs
    97  02:67:F1:B0:6C:98                      MT_FREE  651 secs
    98  E8:28:C1:DE:74:30                               508 secs
    99  1E:C2:8E:D8:30:91                         Damy  498 secs
    100 8E:1F:94:96:DA:FD           iPhone (Анастасия)  415 secs
    101 E0:D9:E3:49:04:50                               401 secs
    102 CE:48:E7:86:4E:33           iPhone (Анастасия)  295 secs
    103 00:26:99:BA:75:8F                               288 secs
    104 2A:E8:A2:02:01:73                Redmi Note 9S  220 secs
    105 2E:FE:13:D0:96:51             Redmi Note 8 Pro   58 secs
    106 9C:A5:13:28:D5:89               Galaxy M012063   43 secs
    107 22:C9:7F:A9:BA:9C                                41 secs
    108 E8:28:C1:DC:54:B0                                36 secs
    109 D2:25:91:F6:6C:D8                        Саня    13 secs
    110 3A:DA:00:F9:0C:02            iPhone XS Max 🦊🐱🦊    9 secs
    111 E8:28:C1:DB:FC:F2                                 9 secs
    112 DC:09:4C:32:34:9B               kak_vashi_dela    8 secs
    113 F2:30:AB:E9:03:ED              iPhone (Uliana)    7 secs
    114 E0:D9:E3:49:04:40                                 7 secs
    115 00:03:7A:1A:03:56                      MT_FREE    6 secs
    116 B2:CF:C0:00:4A:60          Михаил's Galaxy M32    5 secs
    117 BE:FD:EF:18:92:44                     Димасик     4 secs
    118 02:BC:15:7E:D5:DC                      MT_FREE    2 secs
    119 00:23:EB:E3:49:31                                 2 secs
    120 00:3E:1A:5D:14:45                      MT_FREE    2 secs
    121 76:C5:A0:70:08:96                                 2 secs
    122 82:CD:7D:04:17:3B                                 2 secs
    123 E0:D9:E3:49:00:B0                                 1 secs
    124 E8:28:C1:DC:54:B2                                 1 secs
    125 C6:BC:37:7A:67:0D            Mura's Galaxy A52    0 secs
    126 12:48:F9:CF:58:8E                                 0 secs
    127 76:E4:ED:B0:5C:9A               Инет от Путина    0 secs
    128 E0:D9:E3:48:FF:D0                                 0 secs
    129 E2:37:BF:8F:6A:7B                                 0 secs
    130 C2:B5:D7:7F:07:A8  DIRECT-a8-HP M227f LaserJet    0 secs
    131 8A:4E:75:44:5A:F6                  quiksmobile    0 secs
    132 00:03:7A:1A:18:56                                 0 secs
    133 E8:28:C1:DE:47:D1                                 0 secs
    134 A2:FE:FF:B8:9B:C9                   Christie’s    0 secs
    135 00:09:9A:12:55:04                                 0 secs
    136 E8:28:C1:DC:3A:B0                                 0 secs
    137 E8:28:C1:DC:0B:B2                                 0 secs
    138 E8:28:C1:DC:3C:80                                 0 secs
    139 00:23:EB:E3:44:31                                 0 secs
    140 A6:F7:05:31:E8:EE                                 0 secs
    141 BA:2A:7A:DD:38:3E                 Айфон (Oleg)    0 secs
    142 12:54:1A:C6:FF:71                       Amuler    0 secs
    143 76:5E:F3:F9:A5:1C                 Redmi 9C NFC    0 secs
    144 00:03:7F:12:34:56                      MT_FREE    0 secs
    145 E8:28:C1:DC:03:30                                 0 secs
    146 B2:1B:0C:67:0A:BD                                 0 secs
    147 E0:D9:E3:49:00:B1                                 0 secs
    148 E8:28:C1:DC:BD:52                MIREA_HOTSPOT    0 secs
    149 E8:28:C1:DE:72:D0                                 0 secs
    150 E0:D9:E3:49:04:41                                 0 secs
    151 00:26:99:F1:1A:E1                          IKB    0 secs
    152 00:23:EB:E3:44:32                                 0 secs
    153 00:26:CB:AA:62:72                         GIVC    0 secs
    154 E0:D9:E3:48:B4:D2                                 0 secs
    155 AE:3E:7F:C8:BC:8E                     Igorek✨    0 secs
    156 02:B3:45:5A:05:93                     HONOR 30    0 secs
    157 00:00:00:00:00:00                                 0 secs
    158 6A:B0:1A:C2:DF:49                                 0 secs
    159 E8:28:C1:DC:3C:90                                 0 secs
    160 30:B4:B8:11:C0:90                                 0 secs
    161 00:26:99:F2:7A:EF                                 0 secs
    162 02:CF:8B:87:B4:F9                      MT_FREE    0 secs
    163 E8:28:C1:DC:03:32                                 0 secs
    164 00:53:7A:99:98:56                      MT_FREE    0 secs
    165 00:03:7F:10:17:56                                 0 secs
    166 00:0D:97:6B:93:DF                                 0 secs
    167 E8:28:C1:DE:47:D0                 MIREA_GUESTS    0 secs

1.  Обнаружить топ-10 самых быстрых точек доступа.

``` r
dataset_1 %>% select(BSSID,ESSID,Speed) %>% arrange(desc(Speed)) %>% head(10)
```

                   BSSID               ESSID Speed
    1  26:20:53:0C:98:E8                       866
    2  96:FF:FC:91:EF:64                       866
    3  CE:48:E7:86:4E:33  iPhone (Анастасия)   866
    4  8E:1F:94:96:DA:FD  iPhone (Анастасия)   866
    5  9A:75:A8:B9:04:1E                  KC   360
    6  4A:EC:1E:DB:BF:95      POCO X5 Pro 5G   360
    7  56:C5:2B:9F:84:90          OnePlus 6T   360
    8  E8:28:C1:DC:B2:41        MIREA_GUESTS   360
    9  E8:28:C1:DC:B2:40       MIREA_HOTSPOT   360
    10 E8:28:C1:DC:B2:42                       360

1.  Отсортировать точки доступа по частоте отправки запросов (beacons) в
    единицу времени по их убыванию.

``` r
dataset_1 %>% select(BSSID,ESSID,X..beacons) %>% arrange(desc(X..beacons))
```

                    BSSID                        ESSID X..beacons
    1   BE:F1:71:D6:10:D7                 C322U21 0566       1647
    2   1E:93:E3:1B:3C:F4                   Galaxy A71       1390
    3   0A:C5:E1:DB:17:7B                AndroidAP177B       1251
    4   BE:F1:71:D5:17:8B                 C322U13 3965        846
    5   6E:C7:EC:16:DA:1A                         Cnet        750
    6   AA:F4:3F:EE:49:0B             Redmi Note 8 Pro        738
    7   38:1A:52:0D:84:D7     EBFCD57F-EE81fI_DL_1AO2T        704
    8   9A:75:A8:B9:04:1E                           KC        694
    9   D2:6D:52:61:51:5D                                     647
    10  BE:F1:71:D5:0E:53                 C322U06 9080        617
    11  4A:EC:1E:DB:BF:95               POCO X5 Pro 5G        510
    12  4A:86:77:04:B7:28            iPhone (Искандер)        361
    13  56:C5:2B:9F:84:90                   OnePlus 6T        317
    14  E8:28:C1:DC:B2:51                                     279
    15  E8:28:C1:DC:B2:50                 MIREA_GUESTS        260
    16  76:70:AF:A4:D2:AF                         витя        253
    17  E8:28:C1:DC:B2:52                MIREA_HOTSPOT        251
    18  8E:55:4A:85:5B:01                     Vladimir        248
    19  3A:70:96:C6:30:2C            iPhone (Искандер)        145
    20  1C:7E:E5:8E:B7:DE                                     142
    21  38:1A:52:0D:85:1D     EBFCD5C1-EE81fI_DN11AOcY        130
    22  38:1A:52:0D:90:A1     EBFCD597-EE81fI_DMN1AOe1        112
    23  48:5B:39:F9:7A:48                                     109
    24  38:1A:52:0D:8F:EC     EBFCD6C2-EE81fI_DR21AOa0        107
    25  38:1A:52:0D:90:5D     EBFCD615-EE81fI_DOL1AO8w         90
    26  5E:C7:C0:E4:D7:D4                    realme 10         85
    27  00:26:99:F2:7A:E2                         GIVC         84
    28  36:46:53:81:12:A0           GGWPRedmi Note 10S         82
    29  00:26:99:F2:7A:E1                          IKB         65
    30  00:26:99:BA:75:80                         GIVC         61
    31  A2:64:E8:97:58:EE                     Mi Phone         52
    32  9E:A3:A9:D6:28:3C                                      51
    33  00:23:EB:E3:81:FE                          IKB         47
    34  00:23:EB:E3:81:FD                         GIVC         46
    35  0C:80:63:A9:6E:EE                                      42
    36  9E:A3:A9:DB:7E:01                                      40
    37  12:51:07:FF:29:D6         DESKTOP-KITJO8R 5262         32
    38  E8:28:C1:DD:04:40                MIREA_HOTSPOT         30
    39  38:1A:52:0D:97:60     EBFCD593-EE81fI_DMJ1AOI4         28
    40  A6:02:B9:73:2F:76                 C239U51 0701         26
    41  E8:28:C1:DD:04:41                 MIREA_GUESTS         25
    42  E8:28:C1:DD:04:42                                      23
    43  9A:9F:06:44:24:5B        Long Huong Galaxy M12         22
    44  E8:28:C1:DD:04:50                 MIREA_GUESTS         20
    45  00:23:EB:E3:81:F1                          IKB         19
    46  A6:02:B9:73:81:47                 C239U53 6056         19
    47  92:F5:7B:43:0B:69                     Redmi 12         18
    48  A6:02:B9:73:83:18                 C239U52 6706         17
    49  E8:28:C1:DC:C8:32                MIREA_HOTSPOT         12
    50  8E:1F:94:96:DA:FD           iPhone (Анастасия)         12
    51  86:DF:BF:E4:2F:23         DESKTOP-Q7R0KRV 2433         11
    52  E8:28:C1:DD:04:51                                       9
    53  9E:A3:A9:BF:12:C0                                       9
    54  96:FF:FC:91:EF:64                                       9
    55  CE:48:E7:86:4E:33           iPhone (Анастасия)          9
    56  E8:28:C1:DC:C8:31                                       8
    57  00:23:EB:E3:81:F2                         GIVC          7
    58  E8:28:C1:DC:C8:30                 MIREA_GUESTS          7
    59  B6:C4:55:B5:53:24                      Redmi 9          7
    60  F2:30:AB:E9:03:ED              iPhone (Uliana)          6
    61  1E:C2:8E:D8:30:91                         Damy          6
    62  E8:28:C1:DC:B2:41                 MIREA_GUESTS          5
    63  E8:28:C1:DC:B2:40                MIREA_HOTSPOT          5
    64  E8:28:C1:DC:B2:42                                       5
    65  E8:28:C1:DC:C6:B1                                       5
    66  D2:25:91:F6:6C:D8                        Саня           5
    67  E8:28:C1:DC:BD:50                 MIREA_GUESTS          5
    68  3A:DA:00:F9:0C:02            iPhone XS Max 🦊🐱🦊          5
    69  E8:28:C1:DD:04:52                MIREA_HOTSPOT          4
    70  E8:28:C1:DC:C6:B0                 MIREA_GUESTS          4
    71  B2:CF:C0:00:4A:60          Михаил's Galaxy M32          4
    72  26:20:53:0C:98:E8                                       3
    73  E8:28:C1:DE:47:D2                MIREA_HOTSPOT          3
    74  7E:3A:10:A7:59:4E                     vivo Y21          3
    75  9C:A5:13:28:D5:89               Galaxy M012063          3
    76  E8:28:C1:DE:74:31                                       2
    77  00:26:CB:AA:62:71                          IKB          2
    78  10:50:72:00:11:08               MGTS_GPON_B563          2
    79  0A:24:D8:D9:24:70                    IgorKotya          2
    80  2E:FE:13:D0:96:51             Redmi Note 8 Pro          2
    81  E8:28:C1:DE:74:32                MIREA_HOTSPOT          1
    82  76:E4:ED:B0:5C:9A               Инет от Путина          1
    83  56:99:98:EE:5A:4E                tementy-phone          1
    84  02:BC:15:7E:D5:DC                      MT_FREE          1
    85  C2:B5:D7:7F:07:A8  DIRECT-a8-HP M227f LaserJet          1
    86  E8:28:C1:DE:47:D1                                       1
    87  A2:FE:FF:B8:9B:C9                   Christie’s          1
    88  EA:D8:D1:77:C8:08      DIRECT-08-HP M428fdw LJ          1
    89  BA:2A:7A:DD:38:3E                 Айфон (Oleg)          1
    90  00:03:7A:1A:03:56                      MT_FREE          1
    91  76:5E:F3:F9:A5:1C                 Redmi 9C NFC          1
    92  00:03:7F:12:34:56                      MT_FREE          1
    93  00:3E:1A:5D:14:45                      MT_FREE          1
    94  E0:D9:E3:49:00:B1                                       1
    95  E8:28:C1:DC:BD:52                MIREA_HOTSPOT          1
    96  00:26:CB:AA:62:72                         GIVC          1
    97  6A:B0:1A:C2:DF:49                                       1
    98  02:67:F1:B0:6C:98                      MT_FREE          1
    99  76:C5:A0:70:08:96                                       1
    100 EA:7B:9B:D8:56:34                     POCO C40          1
    101 02:CF:8B:87:B4:F9                      MT_FREE          1
    102 E8:28:C1:DE:47:D0                 MIREA_GUESTS          1
    103 E8:28:C1:DC:FF:F2                                       0
    104 00:25:00:FF:94:73                                       0
    105 08:3A:2F:56:35:FE                                       0
    106 C6:BC:37:7A:67:0D            Mura's Galaxy A52          0
    107 E8:28:C1:DE:74:30                                       0
    108 E0:D9:E3:48:FF:D2                                       0
    109 12:48:F9:CF:58:8E                                       0
    110 00:26:99:F2:7A:E0                                       0
    111 2A:E8:A2:02:01:73                Redmi Note 9S          0
    112 E8:28:C1:DC:3C:92                                       0
    113 14:EB:B6:6A:76:37              Gnezdo_lounge 2          0
    114 E0:D9:E3:48:FF:D0                                       0
    115 E2:37:BF:8F:6A:7B                                       0
    116 8A:4E:75:44:5A:F6                  quiksmobile          0
    117 CE:B3:FF:84:45:FC                                       0
    118 00:03:7A:1A:18:56                                       0
    119 E8:28:C1:DC:54:72                                       0
    120 00:AB:0A:00:10:10                                       0
    121 00:09:9A:12:55:04                                       0
    122 E8:28:C1:DC:3A:B0                                       0
    123 E8:28:C1:DC:C6:B2                                       0
    124 E8:28:C1:DB:F5:F2                                       0
    125 BE:FD:EF:18:92:44                     Димасик           0
    126 E8:28:C1:DC:0B:B2                                       0
    127 E8:28:C1:DC:3C:80                                       0
    128 00:23:EB:E3:49:31                                       0
    129 00:23:EB:E3:44:31                                       0
    130 A6:F7:05:31:E8:EE                                       0
    131 12:54:1A:C6:FF:71                       Amuler          0
    132 CE:C3:F7:A4:7E:B3                 DIRECT-A6-HP          0
    133 E8:28:C1:DC:33:12                                       0
    134 E8:28:C1:DB:FC:F2                                       0
    135 00:26:99:BA:75:8F                                       0
    136 DC:09:4C:32:34:9B               kak_vashi_dela          0
    137 E8:28:C1:DC:F0:90                                       0
    138 E0:D9:E3:49:04:52                                       0
    139 E0:D9:E3:49:04:50                                       0
    140 E8:28:C1:DC:03:30                                       0
    141 E0:D9:E3:49:04:40                                       0
    142 B2:1B:0C:67:0A:BD                                       0
    143 E8:28:C1:DC:54:B0                                       0
    144 E0:D9:E3:49:00:B0                                       0
    145 E8:28:C1:DC:33:10                                       0
    146 E8:28:C1:DB:F5:F0                 MIREA_GUESTS          0
    147 E8:28:C1:DE:72:D0                                       0
    148 E0:D9:E3:49:04:41                                       0
    149 00:26:99:F1:1A:E1                          IKB          0
    150 8A:A3:03:73:52:08              Galaxy A30s5208          0
    151 00:23:EB:E3:44:32                                       0
    152 E0:D9:E3:48:B4:D2                                       0
    153 AE:3E:7F:C8:BC:8E                     Igorek✨          0
    154 02:B3:45:5A:05:93                     HONOR 30          0
    155 00:00:00:00:00:00                                       0
    156 E8:28:C1:DC:3C:90                                       0
    157 30:B4:B8:11:C0:90                                       0
    158 00:26:99:F2:7A:EF                                       0
    159 22:C9:7F:A9:BA:9C                                       0
    160 92:12:38:E5:7E:1E                                       0
    161 E8:28:C1:DC:0B:B0                                       0
    162 82:CD:7D:04:17:3B                                       0
    163 E8:28:C1:DC:03:32                                       0
    164 E8:28:C1:DC:54:B2                                       0
    165 00:53:7A:99:98:56                      MT_FREE          0
    166 00:03:7F:10:17:56                                       0
    167 00:0D:97:6B:93:DF                                       0

#### Данные клиентов

1.  Определить производителя для каждого обнаруженного устройства

<!-- -->

1.  Обнаружить устройства, которые НЕ рандомизируют свой MAC адрес

``` r
dataset_2 %>% select(Station.MAC) %>% filter(!Station.MAC %in% grep(":",dataset_2$Station.MAC, value = TRUE))
```

                                                                             Station.MAC
    1                                                                         Galaxy A71
    2                                                                    Galaxy A30s5208
    3                                                                       C322U06 9080
    4                                                                              Kesha
    5                                                                                Дом
    6                                                                      MIREA_HOTSPOT
    7                                                                              Kesha
    8                                                                              Kesha
    9                                                                                M26
    10                                                                             Kesha
    11                                                                           kmkdz_g
    12                                                                  Moscow_WiFi_Free
    13                                                          AAAAADVpTWoADwFlRedmi 4X
    14                                                                             Kesha
    15                                                                     helvetia-free
    16                                                                      MIREA_GUESTS
    17                                                                             Kesha
    18                                                                 RT-5GHz_WiFi_5756
    19                                                                      vestis.local
    20                                                                             Kesha
    21                                                                            -D-13-
    22                                                                             Kesha
    23                                                                             Kesha
    24                                                                               M26
    25                                                                             Kesha
    26                                                                      MGTS_5makmak
    27                                                                     helvetia-free
    28                                                                               M26
    29                                                                      TP-Link_3144
    30                                                                               M26
    31                                                                               M26
    32                                                                                Шк
    33                                                                                Шк
    34                                                                 AndroidShare_8397
    35  \\xA7\\xDF\\xA7\\xD1\\xA7\\xE3\\xA7\\xE4\\xA7\\xD6\\xA7\\xE9\\xA7\\xDC\\xA7\\xD1
    36                                                                 AndroidShare_8795
    37                                                                       home 466_5G
    38                                                                             Kesha
    39                                                                             Kesha
    40                                                                           MT_FREE
    41                                                                          Redmi 12
    42                                                                 AndroidShare_2335
    43                                                                 AndroidShare_2061
    44                                                                 AndroidShare_2335
    45                                                                         KHRISTAKI
    46                                                               MTSRouter_5G_142878
    47                                                                 AndroidShare_1901
    48                                                                 AndroidShare_1901
    49                                                                        拯救者 Y70
    50                                                                 AndroidShare_1901
    51                                                                 AndroidShare_1901
    52                                                                 AndroidShare_1576
    53                                                                      MIREA_GUESTS
    54                                                                        Beeline121
    55                                                                           edeeeèe
    56                                                                 AndroidShare_1576
    57                                                                        Beeline121
    58                                                                                it
    59                                                                           edeeeèe
    60                                                                           edeeeèe
    61                                                                 AndroidShare_1901
    62                                                          AAAAADVpTWoADwFlRedmi 4X
    63                                                                           edeeeèe
    64                                                                     Snickers_ASSA
    65                                                                 AndroidShare_1901
    66                                                                 AndroidShare_1901
    67                                                                 AndroidShare_1576
    68                                                                 AndroidShare_1901
    69                                                                 AndroidShare_1901
    70                                                                 AndroidShare_1901
    71                                                                            podval
    72                                                                 AndroidShare_1901
    73                                                                          Hornet24
    74                                                                                 1
    75                                                                 AndroidShare_1901
    76                                                                                it
    77                                                                                it
    78                                                                           edeeeèe
    79                                                                 AndroidShare_1901
    80                                                                 AndroidShare_1901
    81                                                                                it
    82                                                                        Beeline121
    83                                                                         CPPK_Free
    84                                                                      SevenSky2.4G
    85                                                                 AndroidShare_1901
    86                                                                        Beeline121
    87                                                                 AndroidShare_1901
    88                                                                 AndroidShare_1576
    89                                                                 AndroidShare_1901
    90                                                                 AndroidShare_1901
    91                                                                        Beeline121
    92                                                                        Beeline121
    93                                                                 AndroidShare_1901
    94                                                                 AndroidShare_1901
    95                                                                                it
    96                                                                        Beeline121
    97                                                                           edeeeèe
    98                                                                 AndroidShare_1901
    99                                                                           edeeeèe
    100                                                                      Timo Resort
    101                                                                       Beeline121
    102                                                                       Beeline121
    103                                                                          edeeeèe
    104                                                                RT-5GHz_WiFi_5756
    105                                                                RT-5GHz_WiFi_5756
    106                                                                 MTS_GPON5_ac0968
    107                                                                AndroidShare_1901
    108                                                                RT-5GHz_WiFi_5756
    109                                                                               it
    110                                                                    helvetia-free
    111                                                                    helvetia-free
    112                                                                AndroidShare_1901
    113                                                                     MIREA_GUESTS
    114                                                         BgAAAFytPg4AHwF7Redmi 4A
    115                                                                    helvetia-free
    116                                                                AndroidShare_1901
    117                                                             \\xAC\\xBA\\xAC\\xDC
    118                                                             \\xAC\\xBA\\xAC\\xDC
    119                                                                AndroidShare_1901
    120                                                                          edeeeèe
    121                                                                AndroidShare_1901
    122                                                                             WiFi
    123                                                                       lenovo_pad
    124                                                                          edeeeèe
    125                                                                AndroidShare_1901
    126                                                                              RST
    127                                                                       Beeline121
    128                                                                AndroidShare_1901
    129                                                                AndroidShare_1901
    130                                                                          edeeeèe
    131                                                                AndroidShare_1901
    132                                                                AndroidShare_1901
    133                                                                          edeeeèe
    134                                                                AndroidShare_1576
    135                                                                       Beeline121
    136                                                                AndroidShare_1901
    137                                                                AndroidShare_1901
    138                                                                AndroidShare_1901
    139                                                                                1
    140                                                                AndroidShare_1901
    141                                                                AndroidShare_1901
    142                                                                    Snickers_ASSA
    143                                                                Beeline_5G_F2F425
    144                                                                AndroidShare_1901
    145                                                                                1
    146                                                                       Beeline121
    147                                                                          edeeeèe
    148                                                                AndroidShare_1901
    149                                                                AndroidShare_1576
    150                                                                AndroidShare_1901
    151                                                                AndroidShare_1901
    152                                                                AndroidShare_1901
    153                                                                AndroidShare_1901
    154                                                                               it
    155                                                                AndroidShare_1901
    156                                                                AndroidShare_1901
    157                                                                          edeeeèe
    158                                                                       Beeline121
    159                                                                AndroidShare_1901
    160                                                                AndroidShare_1901
    161                                                                       Beeline121
    162                                                                AndroidShare_1901
    163                                                                          edeeeèe
    164                                                                          edeeeèe
    165                                                                          edeeeèe
    166                                                                       Beeline121
    167                                                                               it
    168                                                                               it
    169                                                                       Beeline121
    170                                                                AndroidShare_1901
    171                                                                AndroidShare_1901
    172                                                                AndroidShare_1576
    173                                                                       Beeline121
    174                                                                     vestis.local
    175                                                                AndroidShare_1901
    176                                                                AndroidShare_1901
    177                                                                          edeeeèe
    178                                                                AndroidShare_1901
    179                                                                       Beeline121
    180                                                                          edeeeèe
    181                                                                AndroidShare_1901
    182                                                                               Шк
    183                                                                AndroidShare_1901
    184                                                                          edeeeèe
    185                                                                               Шк
    186                                                                AndroidShare_1901
    187                                                                          edeeeèe
    188                                                                               it

1.  Кластеризовать запросы от устройств к точкам доступа по их именам.
    Определить время появления устройства в зоне радиовидимости и время
    выхода его из нее.

<!-- -->

1.  Оценить стабильность уровня сигнала внури кластера во времени.
    Выявить наиболее стабильный кластер.

## Вывод

Используя программный пакет dplyr языка программирования R был
произведен анализ журналов.
