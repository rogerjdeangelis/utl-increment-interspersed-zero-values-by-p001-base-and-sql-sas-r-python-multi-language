# utl-increment-interspersed-zero-values-by-p001-base-and-sql-sas-r-python-multi-language
Increment interspersed zero values by .001  sql sas r python multi language
    %let pgm=utl-increment-interspersed-zero-values-by-p001-base-and-sql-sas-r-python-multi-language;

    Increment interspersed zero values by p001 base and sql sas r python multi language

    github
    https://tinyurl.com/dha6m3td
    https://github.com/rogerjdeangelis/utl-increment-interspersed-zero-values-by-p001-base-and-sql-sas-r-python-multi-language

    stackoverflow
    https://tinyurl.com/5xsbbr6d
    https://stackoverflow.com/questions/79181727/how-to-replace-zero-with-incremental-value-in-in-r-data-frame

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

          SOLUTIONS  (TRIVIAL IN SAS DATASTEP)

            1 sas sql monotonic (not supported)
            2 r sql
            3 python sql
            4 sas trivial code
            5 r tidyverse language (uses tidyverse functions %>% filter mutate arrange
            6 r simple for loop  (like many other languages including sas)
              frowned upon by R programmers


    /**************************************************************************************************************************/
    /*                  |                                            |                                                        */
    /*     INPUT        |       PROCESS                              |      OUTPUT                                            */
    /*                  |  INCREMENT CONSECUTIVE 0s by 0.001         |                                                        */
    /*                  |      (SELF EXPLANATORY?)                   |                                                        */
    /*                  | SAS                                        |                                                        */
    /*                  |                                            |                                                        */
    /* YEAR    VALUE    | select                                     |  YEAR    VALUE                                         */
    /*                  |    year                                    |                                                        */
    /* 1981      2      |   ,monotonic()*0.001 as value              |  1981    2.000                                         */
    /* 1982      5      | from                                       |  1982    5.000                                         */
    /* 1983      2      |    (select * from sd1.have where value=0)  |  1983    2.000                                         */
    /* 1984      1      | union                                      |  1984    1.000                                         */
    /* 1985      1      |    all                                     |  1985    1.000                                         */
    /* 1986      5      | select                                     |  1986    5.000                                         */
    /* 1987      2      |    *                                       |  1987    2.000                                         */
    /* 1988      4      | from                                       |  1988    4.000                                         */
    /* 1989      3      |    sd1.have                                |  1989    3.000                                         */
    /* 1990      5      | where                                      |  1990    5.000                                         */
    /* 1991      5      |    value ne 0                              |  1991    5.000                                         */
    /*                  | order                                      |                                                        */
    /* 1992      0      |    by year                                 |  1992    0.001  was 0 start increment                  */
    /*                  |                                            |                                                        */
    /* 1993      1      |--------------------------------------------|  1993    1.000                                         */
    /* 1994      2      |                                            |  1994    2.000                                         */
    /* 1995      4      | R AND PYTHON                               |  1995    4.000                                         */
    /* 1996      2      |                                            |  1996    2.000                                         */
    /* 1997      2      | with                                       |  1997    2.000                                         */
    /*                  |   zeros as                                 |                                                        */
    /* 1998      0      | (select                                    |  1998    0.002   was 0 increment by 0.001              */
    /*                  |     year                                   |                                                        */
    /* 1999      3      |    ,row_number()                           |  1999    3.000                                         */
    /*                  |  over                                      |                                                        */
    /* 2000      0      |     (order by value) as rownum             |  2000    0.003   was 0 increment by 0.001              */
    /*                  |  from                                      |                                                        */
    /*                  |     have                                   |                                                        */
    /*                  |  where                                     |                                                        */
    /*                  |     value=0                                |                                                        */
    /*                  |   )                                        |                                                        */
    /*                  | select                                     |                                                        */
    /*                  |     year                                   |                                                        */
    /*                  |    ,cast(rownum as real)*0.001 as value    |                                                        */
    /*                  | from                                       |                                                        */
    /*                  |     zeros                                  |                                                        */
    /*                  | union                                      |                                                        */
    /*                  |     all                                    |                                                        */
    /*                  | select                                     |                                                        */
    /*                  |     *                                      |                                                        */
    /*                  | from                                       |                                                        */
    /*                  |    have                                    |                                                        */
    /*                  | where                                      |                                                        */
    /*                  |    value <> 0                              |                                                        */
    /*                  | order                                      |                                                        */
    /*                  |    by year                                 |                                                        */
    /*                  |                                            |                                                        */
    /*                  |--------------------------------------------|                                                        */
    /*                  |                                            |                                                        */
    /*                  | SAS (LIKE BASE R)                          |                                                        */
    /*                  |                                            |                                                        */
    /*                  |  data want(drop=inc);                      |                                                        */
    /*                  |    retain inc 0;                           |                                                        */
    /*                  |    set sd1.have;                           |                                                        */
    /*                  |    if value=0 then do                      |                                                        */
    /*                  |       inc   = inc+0.001;                   |                                                        */
    /*                  |       value = inc;                         |                                                        */
    /*                  |    end;                                    |                                                        */
    /*                  |  run;quit;                                 |                                                        */
    /*                  |                                            |                                                        */
    /*                  |--------------------------------------------|                                                        */
    /*                  |                                            |                                                        */
    /*                  | R TIDYVERSE LANGUAGE                       |                                                        */
    /*                  | %>% filter mutate arrange(cumsum is base r)|                                                        */
    /*                  |                                            |                                                        */
    /*                  | want<-have %>%                             |                                                        */
    /*                  |   filter(VALUE!= 0) %>%                    |                                                        */
    /*                  |   rbind(.,                                 |                                                        */
    /*                  |     have %>%                               |                                                        */
    /*                  |       filter(VALUE == 0) %>%               |                                                        */
    /*                  |       mutate(VALUE =                       |                                                        */
    /*                  |        cumsum(VALUE + .001))) %>%          |                                                        */
    /*                  |   arrange(YEAR)                            |                                                        */
    /*                  |                                            |                                                        */
    /*                  |--------------------------------------------|                                                        */
    /*                  |                                            |                                                        */
    /*                  | R SIMPLE FOR LOOP (LIKE SAS)               |                                                        */
    /*                  |                                            |                                                        */
    /*                  | for (i in 1:nrow(have)) {                  |                                                        */
    /*                  |   if (have[i,2]==0.0) {                    |                                                        */
    /*                  |      inc = inc + 0.001                     |                                                        */
    /*                  |      have[i,2]=inc                         |                                                        */
    /*                  |   }                                        |                                                        */
    /*                  | }                                          |                                                        */
    /*                  |                                            |                                                        */
    /*                  |                                            |                                                        */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
       input  Year Value;
    cards4;
    1981     2
    1982     5
    1983     2
    1984     1
    1985     1
    1986     5
    1987     2
    1988     4
    1989     3
    1990     5
    1991     5
    1992     0
    1993     1
    1994     2
    1995     4
    1996     2
    1997     2
    1998     0
    1999     3
    2000     0
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.HAVE total obs=20                                                                                                  */
    /*                                                                                                                        */
    /* Obs    YEAR    VALUE                                                                                                   */
    /*                                                                                                                        */
    /*   1    1981      2                                                                                                     */
    /*   2    1982      5                                                                                                     */
    /*   3    1983      2                                                                                                     */
    /*   4    1984      1                                                                                                     */
    /*   5    1985      1                                                                                                     */
    /*   6    1986      5                                                                                                     */
    /*   7    1987      2                                                                                                     */
    /*   8    1988      4                                                                                                     */
    /*   9    1989      3                                                                                                     */
    /*  10    1990      5                                                                                                     */
    /*  11    1991      5                                                                                                     */
    /*  12    1992      0                                                                                                     */
    /*  13    1993      1                                                                                                     */
    /*  14    1994      2                                                                                                     */
    /*  15    1995      4                                                                                                     */
    /*  16    1996      2                                                                                                     */
    /*  17    1997      2                                                                                                     */
    /*  18    1998      0                                                                                                     */
    /*  19    1999      3                                                                                                     */
    /*  20    2000      0                                                                                                     */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                             _
    / |  ___  __ _ ___   ___  __ _| |
    | | / __|/ _` / __| / __|/ _` | |
    | | \__ \ (_| \__ \ \__ \ (_| | |
    |_| |___/\__,_|___/ |___/\__, |_|
                                |_|
    */
    proc sql;
      create
         table want as
      select
         year
        ,monotonic()*0.001 as value format=8.3
      from
         (select * from sd1.have where value=0)
      union
         all
      select
         *
      from
         sd1.have
      where
         value ne 0
      order
         by year
    ;quit;

    proc print data=sd1.rsqlwant;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* YEAR    VALUE                                                                                                          */
    /*                                                                                                                        */
    /* 1981    2.000                                                                                                          */
    /* 1982    5.000                                                                                                          */
    /* 1983    2.000                                                                                                          */
    /* 1984    1.000                                                                                                          */
    /* 1985    1.000                                                                                                          */
    /* 1986    5.000                                                                                                          */
    /* 1987    2.000                                                                                                          */
    /* 1988    4.000                                                                                                          */
    /* 1989    3.000                                                                                                          */
    /* 1990    5.000                                                                                                          */
    /* 1991    5.000                                                                                                          */
    /* 1992    0.001 **                                                                                                       */
    /* 1993    1.000                                                                                                          */
    /* 1994    2.000                                                                                                          */
    /* 1995    4.000                                                                                                          */
    /* 1996    2.000                                                                                                          */
    /* 1997    2.000                                                                                                          */
    /* 1998    0.002 **                                                                                                       */
    /* 1999    3.000                                                                                                          */
    /* 2000    0.003 **                                                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                     _
    |___ \   _ __   ___  __ _| |
      __) | | `__| / __|/ _` | |
     / __/  | |    \__ \ (_| | |
    |_____| |_|    |___/\__, |_|
                           |_|
    */

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    want<-sqldf('
     with
       zeros as
     (select
         year
        ,row_number()
      over
         (order by value) as rownum
      from
         have
      where
         value=0
       )
     select
         year
        ,cast(rownum as real)*0.001 as value
     from
         zeros
     union
         all
     select
         *
     from
        have
     where
        value <> 0
     order
        by year
    ')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="rsqlwant"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.rsqlwant;
    format value 8.3;
    run;quit;

    /**************************************************************************************************************************/
    /*  SAME OUTPUT                                                                                                           */
    /**************************************************************************************************************************/

    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read());
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    want=pdsql('''                                 \
     with                                          \
       zeros as                                    \
     (select                                       \
         year                                      \
        ,row_number()                              \
      over                                         \
         (order by value) as rownum                \
      from                                         \
         have                                      \
      where                                        \
         value=0                                   \
       )                                           \
     select                                        \
         year                                      \
        ,cast(rownum as real)*0.001 as value       \
     from                                          \
         zeros                                     \
     union                                         \
         all                                       \
     select                                        \
         *                                         \
     from                                          \
        have                                       \
     where                                         \
        value <> 0                                  \
     order                                         \
        by year                                    \
    ''')
    print(want);
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /*  SAME OUTPUT                                                                                                           */
    /**************************************************************************************************************************/

    /*  _                     _        _       _       _
    | || |    ___  __ _ ___  | |_ _ __(_)_   _(_) __ _| |
    | || |_  / __|/ _` / __| | __| `__| \ \ / / |/ _` | |
    |__   _| \__ \ (_| \__ \ | |_| |  | |\ V /| | (_| | |
       |_|   |___/\__,_|___/  \__|_|  |_| \_/ |_|\__,_|_|

    */
    data want;
      retain inc 0;
      set sd1.have;
      if value=0 then do
         inc   = inc+0.001;
         value = inc;
      end;
    run;quit;

    /**************************************************************************************************************************/
    /*  SAME OUTPUT                                                                                                           */
    /**************************************************************************************************************************/

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(tidyverse)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    want<-have %>%
      filter(VALUE!= 0) %>%
      rbind(.,
        have %>%
          filter(VALUE == 0) %>%
          mutate(VALUE =
           cumsum(VALUE + .001))) %>%
      arrange(YEAR)
    want;
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="rwant"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.rwant;
    format value 8.3;
    run;quit;


    /**************************************************************************************************************************/
    /*  SAME OUTPUT                                                                                                           */
    /**************************************************************************************************************************/

    /*___           _   _     _
    | ___|   _ __  | |_(_) __| |_   ___   _____ _ __ ___  ___
    |___ \  | `__| | __| |/ _` | | | \ \ / / _ \ `__/ __|/ _ \
     ___) | | |    | |_| | (_| | |_| |\ V /  __/ |  \__ \  __/
    |____/  |_|     \__|_|\__,_|\__, | \_/ \___|_|  |___/\___|
                                |___/
    */
    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(tidyverse)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    inc<-0.0
    for (i in 1:nrow(have)) {
      if (have[i,2]==0.0) {
         inc = inc + 0.001
         have[i,2]=inc
      }
    }
    print(have)
    fn_tosas9x(
          inp    = have
         ,outlib ="d:/sd1/"
         ,outdsn ="rrwant"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.rrwant;
    format value 8.3;
    run;quit;

    /**************************************************************************************************************************/
    /*  SAME OUTPUT                                                                                                           */
    /**************************************************************************************************************************/

    /*__             __              _
     / /_    _ __   / _| ___  _ __  | | ___   ___  _ __
    | `_ \  | `__| | |_ / _ \| `__| | |/ _ \ / _ \| `_ \
    | (_) | | |    |  _| (_) | |    | | (_) | (_) | |_) |
     \___/  |_|    |_|  \___/|_|    |_|\___/ \___/| .__/
                                                  |_|
    */

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(tidyverse)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    str(have)
    inc<-0.0
    for (i in 1:nrow(have)) {
      if (have[i,2]==0.0) {
         inc = inc + 0.001
         have[i,2]=inc
      }
    }
    print(have)
    fn_tosas9x(
          inp    = have
         ,outlib ="d:/sd1/"
         ,outdsn ="rrwant"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.rrwant;
    format value 8.3;
    run;quit;

    /**************************************************************************************************************************/
    /*  SAME OUTPUT                                                                                                           */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
