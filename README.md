# utl-add-zero-counts-for-missing-category-combinations-sas-sparse-completetypes-sql-r-python
Add zero counts for missing category combinations sas sparse completetypes sql r python 
    %let pgm=utl-add-zero-counts-for-missing-category-combinations-sas-sparse-completetypes-sql-r-python;

    Add zero counts for missing category combinations sas sparse completetypes sql r python

    %stop_submission;

       Solutions
          1 proc freq sparse
          2 proc summary completetypes
          3 proc sql
          4 r sql
          5 python sql

    github
    http://tinyurl.com/4e9j6mcc
    https://github.com/rogerjdeangelis/utl-add-zero-counts-for-missing-category-combinations-sas-sparse-completetypes-sql-r-python

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*          |                                                        |                                                    */
    /* INPUT    |    PROCESS                                             |         OUTPUT                                     */
    /*          |                                                        |                                                    */
    /* AGE SEX  |    PROC FREQ                                           |        AGE SEX CNT                                 */
    /*          |    ==========                                          |                                                    */
    /*  18  M   |    proc freq data=have;                                |      1  18   M   1                                 */
    /*  21  F   |    tables age*sex / SPARSE out=want;                   |      2  18   F   1                                 */
    /*  18  F   |    run;quit;                                           |      3  18   U   1                                 */
    /*  18  U   |                                                        |      4  21   M   0  Not in raw data                */
    /*  21  F   |    PROC SUMMARY                                        |      5  21   F   3                                 */
    /*  21  F   |    ============                                        |      6  21   U   0  Not in raw data                */
    /*  16  F   |    proc summary data=SD1.have COMPLETETYPES nway;      |      7  16   M   0  Not in raw data                */
    /*          |     class age sex ;                                    |      8  16   F   1                                 */
    /*          |     output out= want                                   |      9  16   U   0  Not in raw data                */
    /*          |       (drop=_type_ rename=(_freq_=COUNT));             |                                                    */                                               */
    /*          |    run;quit;                                           |                                                    */
    /*          |                                                        |                                                    */
    /*          |    PROC SQL                                            |                                                    */
    /*          |    ========                                            |                                                    */
    /*          |    proc sql;                                           |                                                    */
    /*          |    create table want as                                |                                                    */
    /*          |    select                                              |                                                    */
    /*          |      distinct                                          |                                                    */
    /*          |         age                                            |                                                    */
    /*          |        ,sex                                            |                                                    */
    /*          |        ,count(l.age) as count                          |                                                    */
    /*          |      from                                              |                                                    */
    /*          |         sd1.have l                                     |                                                    */
    /*          |      natural right join                                |                                                    */
    /*          |         (select                                        |                                                    */
    /*          |             distinct *                                 |                                                    */
    /*          |          from                                          |                                                    */
    /*          |             sd1.have(keep=age), sd1.have(drop=age))    |                                                    */
    /*          |      group                                             |                                                    */
    /*          |         by  sex, age                                   |                                                    */
    /*          |    ;quit;                                              |                                                    */
    /*          |                                                        |                                                    */
    /*          |     SQLLITE R & PYTHON (SAME CODE)                     |                                                    */
    /*          |     ==============================                     |                                                    */
    /*          |     want <-sqldf('                                     |                                                    */
    /*          |        select                                          |                                                    */
    /*          |           l.age                                        |                                                    */
    /*          |          ,l.sex                                        |                                                    */
    /*          |          ,coalesce(r.cnt,0) as cnt                     |                                                    */
    /*          |        from                                            |                                                    */
    /*          |          (select                                       |                                                    */
    /*          |             distinct                                   |                                                    */
    /*          |                l.age                                   |                                                    */
    /*          |               ,r.sex                                   |                                                    */
    /*          |           from                                         |                                                    */
    /*          |             (select                                    |                                                    */
    /*          |                 distinct                               |                                                    */
    /*          |                    age                                 |                                                    */
    /*          |              from                                      |                                                    */
    /*          |                 have) as l, have as r) as l            |                                                    */
    /*          |        left join                                       |                                                    */
    /*          |            (select                                     |                                                    */
    /*          |               age                                      |                                                    */
    /*          |              ,sex                                      |                                                    */
    /*          |              ,count(*) as cnt                          |                                                    */
    /*          |            from                                        |                                                    */
    /*          |               have                                     |                                                    */
    /*          |            group                                       |                                                    */
    /*          |               by age, sex                              |                                                    */
    /*          |            ) as r                                      |                                                    */
    /*          |         on                                             |                                                    */
    /*          |            l.age = r.age and l.sex=r.sex;              |                                                    */
    /*          |     ')                                                 |                                                    */
    /*          |                                                        |                                                    */
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

    data sd1.have ;
    input age sex$;
    cards4;
    18 M
    21 F
    18 F
    18 U
    21 F
    21 F
    16 F
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Obs    AGE    SEX                                                                                                      */
    /*                                                                                                                        */
    /*  1      18     M                                                                                                       */
    /*  2      21     F                                                                                                       */
    /*  3      18     F                                                                                                       */
    /*  4      18     U                                                                                                       */
    /*  5      21     F                                                                                                       */
    /*  6      21     F                                                                                                       */
    /*  7      16     F                                                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                           __
    / |  _ __  _ __ ___   ___   / _|_ __ ___  __ _   ___ _ __   __ _ _ __ ___  ___
    | | | `_ \| `__/ _ \ / __| | |_| `__/ _ \/ _` | / __| `_ \ / _` | `__/ __|/ _ \
    | | | |_) | | | (_) | (__  |  _| | |  __/ (_| | \__ \ |_) | (_| | |  \__ \  __/
    |_| | .__/|_|  \___/ \___| |_| |_|  \___|\__, | |___/ .__/ \__,_|_|  |___/\___|
        |_|                                     |_|     |_|
    */
     proc freq data=sd1.have;
     tables age*sex / sparse out=want;
     run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Obs    AGE    SEX    COUNT    PERCENT                                                                                  */
    /*                                                                                                                        */
    /*  1      16     F       1      14.2857                                                                                  */
    /*  2      16     M       0       0.0000                                                                                  */
    /*  3      16     U       0       0.0000                                                                                  */
    /*  4      18     F       1      14.2857                                                                                  */
    /*  5      18     M       1      14.2857                                                                                  */
    /*  6      18     U       1      14.2857                                                                                  */
    /*  7      21     F       3      42.8571                                                                                  */
    /*  8      21     M       0       0.0000                                                                                  */
    /*  9      21     U       0       0.0000                                                                                  */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___
    |___ \  _ __  _ __ ___   ___   ___ _   _ _ __ ___  _ __ ___   __ _ _ __ _   _
      __) || `_ \| `__/ _ \ / __| / __| | | | `_ ` _ \| `_ ` _ \ / _` | `__| | | |
     / __/ | |_) | | | (_) | (__  \__ \ |_| | | | | | | | | | | | (_| | |  | |_| |
    |_____|| .__/|_|  \___/ \___| |___/\__,_|_| |_| |_|_| |_| |_|\__,_|_|   \__, |
           |_|                 _      _       _   _                          |___/
      ___ ___  _ __ ___  _ __ | | ___| |_ ___| |_| |_ _   _ _ __   ___  ___
     / __/ _ \| `_ ` _ \| `_ \| |/ _ \ __/ _ \ __| __| | | | `_ \ / _ \/ __|
    | (_| (_) | | | | | | |_) | |  __/ ||  __/ |_| |_| |_| | |_) |  __/\__ \
     \___\___/|_| |_| |_| .__/|_|\___|\__\___|\__|\__|\__, | .__/ \___||___/
                        |_|                           |___/|_|
    */

    proc summary data=SD1.have COMPLETETYPES nway;
     class age sex ;
     output out= want
       (drop=_type_ rename=(_freq_=COUNT));
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  bs    AGE    SEX    COUNT                                                                                             */
    /*                                                                                                                        */
    /*  1      16     F       1                                                                                               */
    /*  2      16     M       0                                                                                               */
    /*  3      16     U       0                                                                                               */
    /*  4      18     F       1                                                                                               */
    /*  5      18     M       1                                                                                               */
    /*  6      18     U       1                                                                                               */
    /*  7      21     F       3                                                                                               */
    /*  8      21     M       0                                                                                               */
    /*  9      21     U       0                                                                                               */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____                                    _
    |___ /   _ __  _ __ ___   ___   ___  __ _| |
      |_ \  | `_ \| `__/ _ \ / __| / __|/ _` | |
     ___) | | |_) | | | (_) | (__  \__ \ (_| | |
    |____/  | .__/|_|  \___/ \___| |___/\__, |_|
            |_|                            |_|
    */

    proc sql;
    create
       table want as
    select
      distinct
         age
        ,sex
        ,count(l.age) as count
      from
         sd1.have l
      natural right join
         (select
             distinct *
          from
             sd1.have(keep=age), sd1.have(drop=age))
      group
         by  sex, age
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Obs    AGE    SEX    COUNT                                                                                             */
    /*                                                                                                                        */
    /*  1      16     F       1                                                                                               */
    /*  2      16     M       0                                                                                               */
    /*  3      16     U       0                                                                                               */
    /*  4      18     F       1                                                                                               */
    /*  5      18     M       1                                                                                               */
    /*  6      18     U       1                                                                                               */
    /*  7      21     F       3                                                                                               */
    /*  8      21     M       0                                                                                               */
    /*  9      21     U       0                                                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*  _                      _
    | || |    _ __   ___  __ _| |
    | || |_  | `__| / __|/ _` | |
    |__   _| | |    \__ \ (_| | |
       |_|   |_|    |___/\__, |_|
                            |_|
    */

    %utlfkil("d:/xpt/want.xpt");

    %utl_rbegin;
    parmcards4;
    library(haven)
    library(sqldf)
    library(SASxport)
    have<-read_sas("d:/sd1/have.sas7bdat")
    have;
    want <-sqldf('
       select
          l.age
         ,l.sex
         ,coalesce(r.cnt,0) as cnt
       from
         (select
            distinct
               l.age
              ,r.sex
          from
            (select
                distinct
                   age
             from
                have) as l, have as r) as l
       left join
           (select
              age
             ,sex
             ,count(*) as cnt
           from
              have
           group
              by age, sex
           ) as r
        on
           l.age = r.age and l.sex=r.sex;

        ')
    want
    for (i in 1:ncol(want)) {
      label(want[,i])<-colnames(want)[i]
      print(label(want[,i]))
      }
    write.xport(want,file="d:/xpt/want.xpt")
    ;;;;
    %utl_rend;

    /*--- handles long variable names by using the label to rename the variables  ----*/

    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    /*---- in case you rerun                                                       ----*/
    proc datasets lib=work nolist mt=data mt=view nodetails;delete want want_r_long_names; run;quit;

    data want_r_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  R                                                                                                                     */
    /*                                                                                                                        */
    /*    age sex cnt                                                                                                         */
    /*  1  18   M   1                                                                                                         */
    /*  2  18   F   1                                                                                                         */
    /*  3  18   U   1                                                                                                         */
    /*  4  21   M   0                                                                                                         */
    /*  5  21   F   3                                                                                                         */
    /*  6  21   U   0                                                                                                         */
    /*  7  16   M   0                                                                                                         */
    /*  8  16   F   1                                                                                                         */
    /*  9  16   U   0                                                                                                         */
    /*                                                                                                                        */
    /*  SAS export                                                                                                            */
    /*                                                                                                                        */
    /*  Obs    AGE    SEX    CNT                                                                                              */
    /*                                                                                                                        */
    /*   1      18     M      1                                                                                               */
    /*   2      18     F      1                                                                                               */
    /*   3      18     U      1                                                                                               */
    /*   4      21     M      0                                                                                               */
    /*   5      21     F      3                                                                                               */
    /*   6      21     U      0                                                                                               */
    /*   7      16     M      0                                                                                               */
    /*   8      16     F      1                                                                                               */
    /*   9      16     U      0                                                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*___                _   _                             _
    | ___|   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    |___ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    %utlfkil("d:/xpt/want.xpt");

    %utl_pybegin;
    parmcards4;
    import pyreadstat as ps;
    from os import path
    import pandas as pd
    import xport
    import xport.v56
    import pyreadstat
    import numpy as np
    import pandas as pd
    from pandasql import sqldf
    mysql = lambda q: sqldf(q, globals())
    from pandasql import PandaSQL
    pdsql = PandaSQL(persist=True)
    sqlite3conn = next(pdsql.conn.gen).connection.connection
    sqlite3conn.enable_load_extension(True)
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll')
    mysql = lambda q: sqldf(q, globals())
    have, meta = pyreadstat.read_sas7bdat("d:/sd1/have.sas7bdat")
    print(have);
    want = pdsql("""
       select
          l.age
         ,l.sex
         ,coalesce(r.cnt,0) as cnt
       from
         (select
            distinct
               l.age
              ,r.sex
          from
            (select
                distinct
                   age
             from
                have) as l, have as r) as l
       left join
           (select age, sex, count(*) as cnt from have  group by age, sex) as r
       on
           l.age = r.age and l.sex=r.sex;
    """)
    print(want)
    ps.write_xport(want,'d:\\xpt\\want.xpt',table_name='want',file_format_version=5
    ,column_labels=['AGE','SEX','CNT'])
    ;;;;
    %utl_pyend;

    /*--- handles long variable names by using the label to rename the variables  ----*/

    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    proc datasets lib=work nolist mt=data mt=view nodetails;delete want want_r_long_names; run;quit;

    data want_py_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print data=want_py_long_names;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Python                                                                                                                 */
    /*     age sex  cnt                                                                                                       */
    /*                                                                                                                        */
    /* 0  18.0   M    1                                                                                                       */
    /* 1  18.0   F    1                                                                                                       */
    /* 2  18.0   U    1                                                                                                       */
    /* 3  21.0   M    0                                                                                                       */
    /* 4  21.0   F    3                                                                                                       */
    /* 5  21.0   U    0                                                                                                       */
    /* 6  16.0   M    0                                                                                                       */
    /* 7  16.0   F    1                                                                                                       */
    /* 8  16.0   U    0                                                                                                       */
    /*                                                                                                                        */
    /* SAS                                                                                                                    */
    /*                                                                                                                        */
    /* Obs    AGE    SEX    CNT                                                                                               */
    /*                                                                                                                        */
    /*  1      18     M      1                                                                                                */
    /*  2      18     F      1                                                                                                */
    /*  3      18     U      1                                                                                                */
    /*  4      21     M      0                                                                                                */
    /*  5      21     F      3                                                                                                */
    /*  6      21     U      0                                                                                                */
    /*  7      16     M      0                                                                                                */
    /*  8      16     F      1                                                                                                */
    /*  9      16     U      0                                                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
