# utl-determinating-gender-from-firstname-AI-sas-r-and-python
Determinating gender from firstname AI sas r and python
    %let pgm=utl-determinating-gender-from-firstname-AI-sas-r-and-python;

    Determinating gender from firstname AI sas r and python

             Two solutions
                1 manually download nam2sex.sas7bdat and class.sas7bdat from github
                2 programatically download nam2sex.csv and class.csv from github and convert to sas datasets then run step 1
                  (only after problem statement)


    github
    https://tinyurl.com/yhyntbnt
    https://github.com/rogerjdeangelis/utl-determinating-gender-from-firstname-AI-sas-r-and-python

    file in github you can use the csvs or the sas datasets(created by stattrasnfer)

      class.csv     ( I provide code to programatically download the csv.so you don't have to open github
      nam2sex.csv

      class.sas7bat (nterestingly you canot create a panda dataframe call class using class=class,meta pyreadstat)
      nam2sex.sas7bdat

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    You have to run all the processes left justified,because of parmcards or use the programs after the problem section.

    /***************************************************************************************************************************** */
    /*                                      |                                              |                                       */
    /*  ___  __ _ ___                       |                                              |                                       */
    /* / __|/ _` / __|                      |                                              |                                       */
    /* \__ \ (_| \__ \                      |                                              |                                       */
    /* |___/\__,_|___/                      |                                              |                                       */
    /*                                      |                                              |                                       */
    /*             INPUT                    |              PROCESS                         |              OUTPUT                   */
    /*                                      |                                              |                       LOOKUP          */
    /*  libname tmp "d:/temp";              |  You will need to condition your first names | NAME    FIRSTNAME SEX GENDER MATCH    */
    /*  data tmp.class;                     |                                              |                                       */
    /*    set sashelp.class(keep=name sex); |  upcase and keep 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'| Alfred             M    M      1      */
    /*  run;quit;                           |  firstname must be between 3 and 11 letters  | Alice    ALICE     F    F      1      */
    /*                                      |  Conditioning not shown                      | Barbara  BARBARA   F    F      1      */
    /*  SD1.CLASS total obs=19              |                                              | Carol    CAROL     F    F      1      */
    /*                                      |  proc sql;                                   | Henry              M    M      1      */
    /*    NAME       SEX                    |    create                                    | James              M    M      1      */
    /*                                      |      table want as                           | Jane     JANE      F    F      1      */
    /*    Alfred      M                     |    select                                    | Janet    JANET     F    F      1      */
    /*    Alice       F                     |       l.name                                 | Jeffrey            M    M      1      */
    /*    Barbara     F                     |      ,l.sex                                  | John               M    M      1      */
    /*    Carol       F                     |      ,r.firstname                            | Joyce    JOYCE     F    F      1      */
    /*    Henry       M                     |      ,case (r.sex)                           | Judy     JUDY      F    F      1      */
    /*    James       M                     |         when ( ' ' ) then 'M'                | Louise   LOUISE    F    F      1      */
    /*    Jane        F                     |         else 'F'                             | Mary     MARY      F    F      1      */
    /*    Janet       F                     |       end as sex_match                       | Philip             M    M      1      */
    /*    Jeffrey     M                     |      ,(l.sex = calculated sex_match) as match| Robert             M    M      1      */
    /*    John        M                     |    from                                      | Ronald             M    M      1      */
    /*    Joyce       F                     |       tmp.class as l                         | Thomas             M    M      1      */
    /*    Judy        F                     |    left                                      |                                       */
    /*    Louise      F                     |      join tmp.nam2sex as r                   |                                       */
    /*    Mary        F                     |    on                                        | William  WILLIAM   M    F      0**    */
    /*    Philip      M                     |      upcase(l.name)  = r.firstname           |                                       */
    /*    Robert      M                     | ;quit;                                       |      * SAS HAS GENDER WRONG?          */
    /*    Ronald      M                     |                                              |                                       */
    /*    Thomas      M                     |                                              |                                       */
    /*    William     M                     |                                              |                                       */
    /*                                      |                                              |                                       */
    /*    tmp.nam2sex obs=5,053             |                                              |                                       */
    /*    FIRSTNAME                         |                                              |                                       */
    /*                                      |                                              |                                       */
    /*    ABAGAIL     F                     |                                              |                                       */
    /*    ABAGIL      F                     |                                              |                                       */
    /*    ABBEY       F                     |                                              |                                       */
    /*    ABBIE       F                     |                                              |                                       */
    /*    ABBIGALE    F                     |                                              |                                       */
    /*    ....                              |                                              |                                       */
    /*    ZOIE        F                     |                                              |                                       */
    /*    ZOLA        F                     |                                              |                                       */
    /*    ZOLLIE      F                     |                                              |                                       */
    /*    ZONA        F                     |                                              |                                       */
    /*    ZONIA       F                     |                                              |                                       */
    /*    ZORA        F                     |                                              |                                       */
    /*    ZORAH       F                     |                                              |                                       */
    /*                                      |                                              |                                       */
    /*--------------------------------------|----------------------------------------------|-------------------------------------- */
    /*                                      |                                              |                                       */
    /*   _ __                               |                                              |                                       */
    /*  | `__|                              | %utl_rbegin;                                 |                                       */
    /*  | |                                 | parmcards4;                                  |     NAME FIRSTNAME SEX MAT MATCHED    */
    /*  |_|                                 | library(haven)                               |                                       */
    /*                                      | library(sqldf)                               |   Alfred             M   M       1    */
    /*                                      | source("c:/temp/fn_tosas9.R");               |    Alice     ALICE   F   F       1    */
    /*  SAMEINPUT                           | nam2sex<-read_sas("c:/temp/nam2sex.sas7bdat")|  Barbara   BARBARA   F   F       1    */
    /*                                      | class <- read_sas("c:/temp/class.sas7bdat")  |    Carol     CAROL   F   F       1    */
    /*                                      | want<-sqldf('                                |    Henry             M   M       1    */
    /*                                      |   select                                     |    James             M   M       1    */
    /*                                      |      l.name                                  |     Jane      JANE   F   F       1    */
    /*                                      |     ,r.firstname                             |    Janet     JANET   F   F       1    */
    /*                                      |     ,l.sex                                   |  Jeffrey             M   M       1    */
    /*                                      |     ,case                                    |     John             M   M       1    */
    /*                                      |        when (r.sex = "F") then "F"           |    Joyce     JOYCE   F   F       1    */
    /*                                      |        else "M"                              |     Judy      JUDY   F   F       1    */
    /*                                      |      end as mat                              |   Louise    LOUISE   F   F       1    */
    /*                                      |     ,l.sex =                                 |     Mary      MARY   F   F       1    */
    /*                                      |        case                                  |   Philip             M   M       1    */
    /*                                      |          when (r.sex = "F") then "F"         |   Robert             M   M       1    */
    /*                                      |          else "M"                            |   Ronald             M   M       1    */
    /*                                      |       end as matched                         |   Thomas             M   M       1    */
    /*                                      |   from                                       |  William   WILLIAM   M   F       0    */
    /*                                      |      class as l                              |                                       */
    /*                                      |   left                                       |                                       */
    /*                                      |     join nam2sex as r                        |                                       */
    /*                                      |   on                                         |                                       */
    /*                                      |     upper(l.name)  = r.firstname             |                                       */
    /*                                      |   ');                                        |                                       */
    /*                                      | want;                                        |                                       */
    /*                                      | fn_tosas9(dataf=want);                       |                                       */
    /*                                      | ;;;;                                         |                                       */
    /*                                      | %utl_rend;                                   |                                       */
    /*                                      |                                              |                                       */
    /*                                      | libname tmp "c:/temp";                       |                                       */
    /*                                      | proc print data=tmp.want;                    |                                       */
    /*                                      | run;quit;                                    |                                       */
    /*                                      |                                              |                                       */
    /*--------------------------------------|------------------------------------------------------------------------------------- */
    /*                                      |                                                               |                      */
    /*              _   _                   |                                                               |    NAME SEX MAT  EST */
    /*  _ __  _   _| |_| |__   ___  _ __    |                                                               |                      */
    /* | `_ \| | | | __| `_ \ / _ \| `_ \   | %utl_pybegin;                                                 |  Alfred   M   M   1  */
    /* | |_) | |_| | |_| | | | (_) | | | |  | parmcards4;                                                   |   Alice   F   F   1  */
    /* | .__/ \__, |\__|_| |_|\___/|_| |_|  | import os;                                                    | Barbara   F   F   1  */
    /* |_|    |___/                         | import subprocess                                             |   Carol   F   F   1  */
    /*                                      | import time                                                   |   Henry   M   M   1  */
    /*  SAME INPUT                          | import pandas as pd;                                          |   James   M   M   1  */
    /*  SAME SQL CODE AS R                  | import numpy as np;                                           |    Jane   F   F   1  */
    /*                                      | from pandasql import sqldf;                                   |   Janet   F   F   1  */
    /*                                      | mysql = lambda q: sqldf(q, globals());                        | Jeffrey   M   M   1  */
    /*                                      | from pandasql import PandaSQL;                                |    John   M   M   1  */
    /*                                      | pdsql = PandaSQL(persist=True);                               |   Joyce   F   F   1  */
    /*                                      | sqlite3conn = next(pdsql.conn.gen).connection.connection;     |    Judy   F   F   1  */
    /*                                      | sqlite3conn.enable_load_extension(True);                      |  Louise   F   F   1  */
    /*                                      | sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll'); |    Mary   F   F   1  */
    /*                                      | mysql = lambda q: sqldf(q, globals());                        |  Philip   M   M   1  */
    /*                                      | import pyreadstat as ps                                       |  Robert   M   M   1  */
    /*                                      | clas,meta=ps.read_sas7bdat("c:/temp/class.sas7bdat");         |  Ronald   M   M   1  */
    /*                                      | nam2sex,meta=ps.read_sas7bdat("c:/temp/nam2sex.sas7bdat");    |  Thomas   M   M   1  */
    /*                                      | want = pdsql('''                                              | William   M   F   0  */
    /*                                      |   select                                                      |                      */
    /*                                      |      l.name                                                   |                      */
    /*                                      |     ,r.firstname                                              |                      */
    /*                                      |     ,l.sex                                                    |                      */
    /*                                      |     ,case                                                     |                      */
    /*                                      |        when (r.sex = "F") then "F"                            |                      */
    /*                                      |        else "M"                                               |                      */
    /*                                      |      end as mat                                               |                      */
    /*                                      |     ,l.sex =                                                  |                      */
    /*                                      |        case                                                   |                      */
    /*                                      |          when (r.sex = "F") then "F"                          |                      */
    /*                                      |          else "M"                                             |                      */
    /*                                      |       end as matched                                          |                      */
    /*                                      |   from                                                        |                      */
    /*                                      |      clas as l                                                |                      */
    /*                                      |   left                                                        |                      */
    /*                                      |     join nam2sex as r                                         |                      */
    /*                                      |   on                                                          |                      */
    /*                                      |     upper(l.name)  = r.firstname                              |                      */
    /*                                      |   ''');                                                       |                      */
    /*                                      | want.info();                                                  |                      */
    /*                                      | print(want);                                                  |                      */
    /*                                      | exec(open('c:/temp/fn_tosas9.py').read())                     |                      */
    /*                                      | fn_tosas9(                                                    |                      */
    /*                                      |    want                                                       |                      */
    /*                                      |    ,dfstr="want"                                              |                      */
    /*                                      |    ,timeest=4                                                 |                      */
    /*                                      |    );                                                         |                      */
    /*                                      | ;;;;                                                          |                      */
    /*                                      | %utl_pyend;                                                   |                      */
    /*                                      |                                                               |                      */
    /*                                      | libname tmp "c:/temp";                                        |                      */
    /*                                      | proc print data=tmp.want;                                     |                      */
    /*                                      | run;quit;                                                     |                      */
    /*                                      |                                                               |                      */
    /*******************************************************************************************************|***********************/

    /*
     _                                     _       _                     _                 _                                                 _
    / |  _ __ ___   __ _ _ __  _   _  __ _| |   __| | _____      ___ __ | | ___   __ _  __| |  ___  __ _ ___   _ __  _ __  _   _   ___  __ _| |
    | | | `_ ` _ \ / _` | `_ \| | | |/ _` | |  / _` |/ _ \ \ /\ / / `_ \| |/ _ \ / _` |/ _` | / __|/ _` / __| | `__|| `_ \| | | | / __|/ _` | |
    | | | | | | | | (_| | | | | |_| | (_| | | | (_| | (_) \ V  V /| | | | | (_) | (_| | (_| | \__ \ (_| \__ \ | |   | |_) | |_| | \__ \ (_| | |
    |_| |_| |_| |_|\__,_|_| |_|\__,_|\__,_|_|  \__,_|\___/ \_/\_/ |_| |_|_|\___/ \__,_|\__,_| |___/\__,_|___/ |_|   | .__/ \__, | |___/\__, |_|
                _           _ _   _           _       _                   _                                         |_|    |___/          |_|
      __ _  ___| |_    __ _(_) |_| |__  _   _| |__   (_)_ __  _ __  _   _| |_ ___
     / _` |/ _ \ __|  / _` | | __| `_ \| | | | `_ \  | | `_ \| `_ \| | | | __/ __|
    | (_| |  __/ |_  | (_| | | |_| | | | |_| | |_) | | | | | | |_) | |_| | |_\__ \
     \__, |\___|\__|  \__, |_|\__|_| |_|\__,_|_.__/  |_|_| |_| .__/ \__,_|\__|___/
     |___/            |___/                                  |_|
    */

    Go to and click on the raw tab this will download female_lookup.sas7bdat

    https://tinyurl.com/yckavpyz
    https://github.com/rogerjdeangelis/utl-determinating-gender-from-firstname-AI-sas-r-and-python/blob/main/female_lookup.sas7bdat


    Go to and click on the raw tab this will download female_lookup.sas7bdat

    https://tinyurl.com/3etuxn5z
    https://github.com/rogerjdeangelis/utl-determinating-gender-from-firstname-AI-sas-r-and-python/blob/main/class.sas7bdat

    Copy both downloads to c:/temp

    /**************************************************************************************************************************/
    /*                          |                                                                                             */
    /* C:/temp/class.sas7bdat   |    C:/temp/NAM2SEX.sas7bdat                                                                 */
    /*                          |                                                                                             */
    /* TMP.CLASS total obs=19   |    TMP.NAM2SEX total obs=5,053                                                              */
    /*                          |                                                                                             */
    /*   NAME       SEX         |      ROWNAMES    FIRSTNAME    SEX                                                           */
    /*                          |                                                                                             */
    /*   Alfred      M          |          1       ABAGAIL       F                                                            */
    /*   Alice       F          |          2       ABAGIL        F                                                            */
    /*   Barbara     F          |          3       ABBEY         F                                                            */
    /*   Carol       F          |          4       ABBIE         F                                                            */
    /*   Henry       M          |          5       ABBIGALE      F                                                            */
    /*   James       M          |       ....                                                                                  */
    /*   Jane        F          |       5049       ZOWIE         F                                                            */
    /*   Janet       F          |       5050       ZUBA          F                                                            */
    /*   Jeffrey     M          |       5051       ZULA          F                                                            */
    /*   John        M          |       5052       ZYLLA         F                                                            */
    /*   Joyce       F          |       5053       ZYLPHA        F                                                            */
    /*   Judy        F          |                                                                                             */
    /*   Louise      F          |                                                                                             */
    /*   Mary        F          |                                                                                             */
    /*   Philip      M          |                                                                                             */
    /*   Robert      M          |                                                                                             */
    /*   Ronald      M          |                                                                                             */
    /*   Thomas      M          |                                                                                             */
    /*   William     M          |                                                                                             */
    /*                          |                                                                                             */
    /**************************************************************************************************************************/

    /*
     ___  __ _ ___   _ __  _ __ ___   ___ ___  ___ ___
    / __|/ _` / __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    \__ \ (_| \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
    |___/\__,_|___/ | .__/|_|  \___/ \___\___||___/___/
                    |_|
    */

    /*----                                                                   ----*/
    /*---- You will need to condition your first names                       ----*/
    /*----                                                                   ----*/
    /*---- upcase and keep 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'                      ----*/
    /*---- firstname must be between 3 and 11 letters                        ----*/
    /*---- Conditioning not shown                                            ----*/
    /*----                                                                   ----*/
    /*----                                                                   ----*/

    proc sql;
      create
        table want as
      select
         l.name
        ,l.sex
        ,r.firstname
        ,case (r.sex)
           when ( ' ' ) then 'M'
           else 'F'
         end as sex_match
        ,(l.sex = calculated sex_match) as match
      from
         tmp.class as l
      left
        join tmp.nam2sex as r
      on
        upcase(l.name)  = r.firstname
    ;quit;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  WORK.WANT                                                                                                             */
    /*                          SEX                                                                                           */
    /*  NAME    FIRSTNAME SEX  MATCH MATCH                                                                                    */
    /*                                                                                                                        */
    /*  Alfred             M    M      1                                                                                      */
    /*  Alice    ALICE     F    F      1                                                                                      */
    /*  Barbara  BARBARA   F    F      1                                                                                      */
    /*  Carol    CAROL     F    F      1                                                                                      */
    /*  Henry              M    M      1                                                                                      */
    /*  James              M    M      1                                                                                      */
    /*  Jane     JANE      F    F      1                                                                                      */
    /*  Janet    JANET     F    F      1                                                                                      */
    /*  Jeffrey            M    M      1                                                                                      */
    /*  John               M    M      1                                                                                      */
    /*  Joyce    JOYCE     F    F      1                                                                                      */
    /*  Judy     JUDY      F    F      1                                                                                      */
    /*  Louise   LOUISE    F    F      1                                                                                      */
    /*  Mary     MARY      F    F      1                                                                                      */
    /*  Philip             M    M      1                                                                                      */
    /*  Robert             M    M      1                                                                                      */
    /*  Ronald             M    M      1                                                                                      */
    /*  Thomas             M    M      1                                                                                      */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  William  WILLIAM   M    F      0**                                                                                    */
    /*                                                                                                                        */
    /*  * SAS HAS GENDER WRONG?                                                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __   _ __  _ __ ___   ___ ___  ___ ___
    | `__| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |    | |_) | | | (_) | (_|  __/\__ \__ \
    |_|    | .__/|_|  \___/ \___\___||___/___/
           |_|
    */

    %utl_rbegin;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/temp/fn_tosas9.R");
    nam2sex <- read_sas("c:/temp/nam2sex.sas7bdat")
    class   <- read_sas("c:/temp/class.sas7bdat")
    want<-sqldf('
      select
         l.name
        ,r.firstname
        ,l.sex
        ,case
           when (r.sex = "F") then "F"
           else "M"
         end as mat
        ,l.sex =
           case
             when (r.sex = "F") then "F"
             else "M"
          end as matched
      from
         class as l
      left
        join nam2sex as r
      on
        upper(l.name)  = r.firstname
      ');
    want;
    fn_tosas9(dataf=want);
    ;;;;
    %utl_rend;

    libname tmp "c:/temp";
    proc print data=tmp.want;
    run;quit;

     /**************************************************************************************************************************/
     /*                                      |                                                                                 */
     /*  R WANT DATAFRAME                    |  SAS TMP.WANT total obs=19 08APR2024:13:11:31                                   */
     /*                                      |                                                                                 */
     /*    NAME FIRSTNAME SEX MAT MATCHED    |   ROWNAMES    NAME       FIRSTNAME    SEX    MAT    MATCHED                     */
     /*                                      |                                                                                 */
     /*  Alfred      <NA>   M   M       1    |       1       Alfred                   M      M        1                        */
     /*   Alice     ALICE   F   F       1    |       2       Alice       ALICE        F      F        1                        */
     /* Barbara   BARBARA   F   F       1    |       3       Barbara     BARBARA      F      F        1                        */
     /*   Carol     CAROL   F   F       1    |       4       Carol       CAROL        F      F        1                        */
     /*   Henry      <NA>   M   M       1    |       5       Henry                    M      M        1                        */
     /*   James      <NA>   M   M       1    |       6       James                    M      M        1                        */
     /*    Jane      JANE   F   F       1    |       7       Jane        JANE         F      F        1                        */
     /*   Janet     JANET   F   F       1    |       8       Janet       JANET        F      F        1                        */
     /* Jeffrey      <NA>   M   M       1    |       9       Jeffrey                  M      M        1                        */
     /*    John      <NA>   M   M       1    |      10       John                     M      M        1                        */
     /*   Joyce     JOYCE   F   F       1    |      11       Joyce       JOYCE        F      F        1                        */
     /*    Judy      JUDY   F   F       1    |      12       Judy        JUDY         F      F        1                        */
     /*  Louise    LOUISE   F   F       1    |      13       Louise      LOUISE       F      F        1                        */
     /*    Mary      MARY   F   F       1    |      14       Mary        MARY         F      F        1                        */
     /*  Philip      <NA>   M   M       1    |      15       Philip                   M      M        1                        */
     /*  Robert      <NA>   M   M       1    |      16       Robert                   M      M        1                        */
     /*  Ronald      <NA>   M   M       1    |      17       Ronald                   M      M        1                        */
     /*  Thomas      <NA>   M   M       1    |      18       Thomas                   M      M        1                        */
     /* William   WILLIAM   M   F       0    |      19       William     WILLIAM      M      F        0                        */
     /*                                      |                                                                                 */
     /**************************************************************************************************************************/

    %utl_pybegin;
    parmcards4;
    import os;
    import subprocess
    import time
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll');
    mysql = lambda q: sqldf(q, globals());
    import pyreadstat as ps
    clas,meta=ps.read_sas7bdat("c:/temp/class.sas7bdat");
    nam2sex,meta=ps.read_sas7bdat("c:/temp/nam2sex.sas7bdat");
    want = pdsql('''
      select
         l.name
        ,r.firstname
        ,l.sex
        ,case
           when (r.sex = "F") then "F"
           else "M"
         end as mat
        ,l.sex =
           case
             when (r.sex = "F") then "F"
             else "M"
          end as matched
      from
         clas as l
      left
        join nam2sex as r
      on
        upper(l.name)  = r.firstname
      ''');
    want.info();
    print(want);
    exec(open('c:/temp/fn_tosas9.py').read())
    fn_tosas9(
       want
       ,dfstr="want"
       ,timeest=4
       );
    ;;;;
    %utl_pyend;

    libname tmp "c:/temp";
    proc print data=tmp.want;
    run;quit;


    /*************************************************************************************************************************/
    /*                                          |                                                                            */
    /*                  PYTHON                  |                       SAS                                                  */
    /*                                          |                                                                            */
    /*        NAME FIRSTNAME SEX mat  matched   |    NAME       FIRSTNAME    SEX    MAT    MATCHED                           */
    /*                                          |                                                                            */
    /* 0    Alfred      None   M   M        1   |    Alfred                   M      M        1                              */
    /* 1     Alice     ALICE   F   F        1   |    Alice       ALICE        F      F        1                              */
    /* 2   Barbara   BARBARA   F   F        1   |    Barbara     BARBARA      F      F        1                              */
    /* 3     Carol     CAROL   F   F        1   |    Carol       CAROL        F      F        1                              */
    /* 4     Henry      None   M   M        1   |    Henry                    M      M        1                              */
    /* 5     James      None   M   M        1   |    James                    M      M        1                              */
    /* 6      Jane      JANE   F   F        1   |    Jane        JANE         F      F        1                              */
    /* 7     Janet     JANET   F   F        1   |    Janet       JANET        F      F        1                              */
    /* 8   Jeffrey      None   M   M        1   |    Jeffrey                  M      M        1                              */
    /* 9      John      None   M   M        1   |    John                     M      M        1                              */
    /* 10    Joyce     JOYCE   F   F        1   |    Joyce       JOYCE        F      F        1                              */
    /* 11     Judy      JUDY   F   F        1   |    Judy        JUDY         F      F        1                              */
    /* 12   Louise    LOUISE   F   F        1   |    Louise      LOUISE       F      F        1                              */
    /* 13     Mary      MARY   F   F        1   |    Mary        MARY         F      F        1                              */
    /* 14   Philip      None   M   M        1   |    Philip                   M      M        1                              */
    /* 15   Robert      None   M   M        1   |    Robert                   M      M        1                              */
    /* 16   Ronald      None   M   M        1   |    Ronald                   M      M        1                              */
    /* 17   Thomas      None   M   M        1   |    Thomas                   M      M        1                              */
    /* 18  William   WILLIAM   M   F        0   |    William     WILLIAM      M      F        0                              */
    /*                                          |                                                                            */
    /*************************************************************************************************************************/

    /*___        _                     _                 _   _ __  _ __ ___   __ _ _ __ __ _ _ __ ___
    |___ \    __| | _____      ___ __ | | ___   __ _  __| | | `_ \| `__/ _ \ / _` | `__/ _` | `_ ` _ \
      __) |  / _` |/ _ \ \ /\ / / `_ \| |/ _ \ / _` |/ _` | | |_) | | | (_) | (_| | | | (_| | | | | | |
     / __/  | (_| | (_) \ V  V /| | | | | (_) | (_| | (_| | | .__/|_|  \___/ \__, |_|  \__,_|_| |_| |_|
    |_____|  \__,_|\___/ \_/\_/ |_| |_|_|\___/ \__,_|\__,_| |_|              |___/
     _                   _         ___ __  __
    (_)_ __  _ __  _   _| |_ ___  | _||__ \_ |
    | | `_ \| `_ \| | | | __/ __| | |  __) | |
    | | | | | |_) | |_| | |_\__ \ | | / __/| |
    |_|_| |_| .__/ \__,_|\__|___/ | ||_____| |
            |_|           _       |__| _   |__|      _                 _        _     _
      __ _  ___ _ __   __| | ___ _ __  | | ___   ___ | | ___   _ _ __  | |_ __ _| |__ | | ___
     / _` |/ _ \ `_ \ / _` |/ _ \ `__| | |/ _ \ / _ \| |/ / | | | `_ \ | __/ _` | `_ \| |/ _ \
    | (_| |  __/ | | | (_| |  __/ |    | | (_) | (_) |   <| |_| | |_) || || (_| | |_) | |  __/
     \__, |\___|_| |_|\__,_|\___|_|    |_|\___/ \___/|_|\_\\__,_| .__/  \__\__,_|_.__/|_|\___|
     |___/                                                      |_|
    */

    /*---- github csv file on github  female_lookup.csv                      ----*/

    github
    female_lookup.csv
    https://tinyurl.com/2upbsxu6
    https://raw.githubusercontent.com/rogerjdeangelis/utl-determinating-gender-from-firstname-AI-sas-r-and-python/main/nam2sex.csv


    /*----                                                                   ----*/
    /*---- download github gender lookup csv & make c:/temp/nam2sex.sas7bdat ----*/
    /*----
    %utlfkil(c:/temp/females.csv);
    %utlfkil(c:/temp/nam2sex.sas7bdat);

    %utl_rbegin;
    parmcards4;
    library(sqldf)
    source("c:/temp/fn_tosas9.R");
    download.file("https://tinyurl.com/2upbsxu6","c:/temp/females.csv",mode="wb");
    data <- read.csv("c:/temp/females.csv", stringsAsFactors = FALSE);
    nam2sex<-sqldf('
      select
        firstname
       ,"F" as sex
      from
        data
      ');
    fn_tosas9(dataf=nam2sex);
    ;;;;
    %utl_rend;

    libname tmp "c:/temp";
    proc print data=tmp.nam2sex(obs=10) noobs;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* TMP.NAM2SEX total obs=5,053 (scraped from baby name files)                                                             */
    /*                                                                                                                        */
    /*  ROWNAMES    FIRSTNAME    SEX                                                                                          */
    /*                                                                                                                        */
    /*      1       ABAGAIL       F                                                                                           */
    /*      2       ABAGIL        F                                                                                           */
    /*      3       ABBEY         F                                                                                           */
    /*      4       ABBIE         F                                                                                           */
    /*      5       ABBIGALE      F                                                                                           */
    /*      9       ABIGAIL       F                                                                                           */
    /*   ....                                                                                                                 */
    /*   5047       ZOIE          F                                                                                           */
    /*   5048       ZOLA          F                                                                                           */
    /*   5049       ZOLLIE        F                                                                                           */
    /*   5050       ZONA          F                                                                                           */
    /*   5051       ZONIA         F                                                                                           */
    /*   5052       ZORA          F                                                                                           */
    /*   5053       ZORAH         F                                                                                           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                         _            _       _
     ___  __ _ _ __ ___  _ __ | | ___    __| | __ _| |_ __ _
    / __|/ _` | `_ ` _ \| `_ \| |/ _ \  / _` |/ _` | __/ _` |
    \__ \ (_| | | | | | | |_) | |  __/ | (_| | (_| | || (_| |
    |___/\__,_|_| |_| |_| .__/|_|\___|  \__,_|\__,_|\__\__,_|
                        |_|
    */
    %utlfkil(c:/temp/class.csv);
    %utlfkil(c:/temp/class.sas7bdat);

    /*---- github csv file on github class.csv                               ----*/

    github
    class.csv
    https://tinyurl.com/5a2948jb
    https://raw.githubusercontent.com/rogerjdeangelis/utl-determinating-gender-from-firstname-AI-sas-r-and-python/main/class.csv

    /*----                                                                   ----*/
    /*---- download github sample class.csv  & make c:/temp/class.sas7bdat   ----*/
    /*----                                                                   ----*/

    %utl_rbegin;
    parmcards4;
    library(sqldf)
    source("c:/temp/fn_tosas9.R");
    download.file("https://tinyurl.com/5a2948jb","c:/temp/class.csv",mode="wb");
    class <- read.csv("c:/temp/class.csv", stringsAsFactors = FALSE);
    str(class);
    fn_tosas9(dataf=class);
    ;;;;
    %utl_rend;

    libname tmp "c:/temp";
    proc print data=tmp.class noobs;
    run;quit;


    libname sd1 "d:/sd1";
    data sd1.class;
      set sashelp.class(keep=name sex);
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.CLASS total obs=19                                                                                                 */
    /*                                                                                                                        */
    /*   NAME       SEX                                                                                                       */
    /*                                                                                                                        */
    /*   Alfred      M                                                                                                        */
    /*   Alice       F                                                                                                        */
    /*   Barbara     F                                                                                                        */
    /*   Carol       F                                                                                                        */
    /*   Henry       M                                                                                                        */
    /*   James       M                                                                                                        */
    /*   Jane        F                                                                                                        */
    /*   Janet       F                                                                                                        */
    /*   Jeffrey     M                                                                                                        */
    /*   John        M                                                                                                        */
    /*   Joyce       F                                                                                                        */
    /*   Judy        F                                                                                                        */
    /*   Louise      F                                                                                                        */
    /*   Mary        F                                                                                                        */
    /*   Philip      M                                                                                                        */
    /*   Robert      M                                                                                                        */
    /*   Ronald      M                                                                                                        */
    /*   Thomas      M                                                                                                        */
    /*   William     M                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
