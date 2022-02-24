# utl-using-the-regression-equation-to-score-a-table-like_a_weighted-sum
Using the regression equation to score a table
    %let pgm=utl-using-the-regression-equation-to-score-a-table-like_a_weighted-sum;

       Using the regression equation to score a table

           1. sas proc score
           2. sas datastep
           3. sas sql
           4  R rquation (I gave up on using matrix algebra due to both datatypes and data structures).
           5. R SQL
           6. Python equation
           7. Python SQL (I gave uo on matrix algebra due to both datatypes and data structures).

      Macros on end
      
      github
      https://tinyurl.com/yrm2jsrk                                                                             
      https://github.com/rogerjdeangelis/utl-using-the-regression-equation-to-score-a-table-like_a_weighted-sum

      I really think SQL with minor integration with with R and Python is the best solution outside SAS.
      Minimize R and Python in favor of SQL. However use the packages.
      The SQL code is pretty much the same in R, Python and SAS.
      The myriad of datatypes and data structures make it very
      difficult write R or Python code. Datatypes and data structures in and out of packages are also an issue.
      SQL keeps objects in dataframes which greatly simplifies programming.
      Exporting data out of R and Python is a nightmare.
      R and Python are legends in there own minds?

    /*
     _                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    libname sd1 "d:/sd1";
    Options validvarname=upcase;


    DATA sd1.mat ;
        input m1 m2 m3 ;
    cards4;
    1.  2.   3.
    2.  4.   6.
    3.  6.   9.
    4.  8.  12.
    5. 10.  15 .
    ;;;;
    run;quit;

    /************************************************************/
    /*                                                          */
    /* Up to 40 obs from SD1.MAT total obs=5 23FEB2022:13:25:37 */
    /*                                                          */
    /* Obs    M1    M2    M3                                    */
    /*                                                          */
    /*  1      1     2     3                                    */
    /*  2      2     4     6                                    */
    /*  3      3     6     9                                    */
    /*  4      4     8    12                                    */
    /*  5      5    10    15                                    */
    /*                                                          */
    /************************************************************/

    DATA sd1.wgt ;
        _type_='SCORE';
        _model_='MODEL';
        input m1 m2 m3 ;
    cards4;
    0.1 0.2 0.3
    ;;;;
    run;quit;

    /************************************************************/
    /*                                                          */
    /* Up to 40 obs from SD1.WGT total obs=1 23FEB2022:13:27:29 */
    /*                                                          */
    /* Obs    _TYPE_    _MODEL_     M1     M2     M3            */
    /*                                                          */
    /*  1     SCORE      MODEL     0.1    0.2    0.3            */
    /*                                                          */
    /************************************************************/

    /*          _
     _ __ _   _| | ___  ___
    | `__| | | | |/ _ \/ __|
    | |  | |_| | |  __/\__ \
    |_|   \__,_|_|\___||___/

    */
    /************************************************************/
    /*                                                          */
    /* Up to 40 obs from SD1.MAT total obs=5 23FEB2022:13:25:37 */
    /*                                                          */
    /* Obs    M1    M2    M3                      Weighted Sum  */
    /*                                                          */
    /*  1      1     2     3     .01*1+.02*2+.03*3   =1.4       */
    /*  2      2     4     6                                    */
    /*  3      3     6     9                                    */
    /*  4      4     8    12                                    */
    /*  5      5    10    15     .01*5+.02*10+.03*15 =7.0       */
    /*                                                          */
    /************************************************************/


    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /************************************************************/
    /*                                                          */
    /* Up to 40 obs WORK.SCORED total obs=5 23FEB2022:13:34:27  */
    /*                                                          */
    /* Obs    M1    M2    M3    MODEL                           */
    /*                                                          */
    /*  1      1     2     3     1.4  see above                 */
    /*  2      2     4     6     2.8                            */
    /*  3      3     6     9     4.2                            */
    /*  4      4     8    12     5.6                            */
    /*  5      5    10    15     7.0  see above                 */
    /*                                                          */
    /************************************************************/

    /*
     ___  __ _ ___   _ __  _ __ ___   ___ ___  ___ ___  ___  ___
    / __|/ _` / __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|/ _ \/ __|
    \__ \ (_| \__ \ | |_) | | | (_) | (_|  __/\__ \__ \  __/\__ \
    |___/\__,_|___/ | .__/|_|  \___/ \___\___||___/___/\___||___/
                    |_|
     _
    / |    _ __  _ __ ___   ___   ___  ___ ___  _ __ ___
    | |   | `_ \| `__/ _ \ / __| / __|/ __/ _ \| `__/ _ \
    | |_  | |_) | | | (_) | (__  \__ \ (_| (_) | | |  __/
    |_(_) | .__/|_|  \___/ \___| |___/\___\___/|_|  \___|
          |_|
    */

    proc score data=sd1.mat score=sd1.wgt out=scored ;
       var m1 m2 m3;
    run;quit;

    /*___                          _       _            _
    |___ \     ___  __ _ ___    __| | __ _| |_ __ _ ___| |_ ___ _ __
      __) |   / __|/ _` / __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \
     / __/ _  \__ \ (_| \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) |
    |_____(_) |___/\__,_|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/
                                                               |_|
    */

    data log(drop=rc _type_ _model_);

     set sd1.wgt;

     call symputx('w1',m1);
     call symputx('w2',m2);
     call symputx('w3',m3);

     rc=dosubl('

        data scored;
          set sd1.mat;
          scored=m1*&w1 + m2*&w2 + m3*&w3;
        run;quit;
        %let cc=&syserr;

        ');

    if symgetn('cc')=0 then status="Model table scored";
    else status="Not able to score table";

    run;quit;

    /*********************************************************/
    /*                                                       */
    /* LOG table                                             */
    /*                                                       */
    /* Up to 40 obs WORK.LOG total obs=1 23FEB2022:14:08:07  */
    /*                                                       */
    /* Obs     M1     M2     M3          STATUS              */
    /*                                                       */
    /*  1     0.1    0.2    0.3    Model table scored        */
    /*                                                       */
    /*********************************************************/

    /*____                              _
    |___ /    ___  __ _ ___   ___  __ _| |
      |_ \   / __|/ _` / __| / __|/ _` | |
     ___) |  \__ \ (_| \__ \ \__ \ (_| | |
    |____(_) |___/\__,_|___/ |___/\__, |_|
                                     |_|
    */

    proc sql;
        create
           table scored as
        select
           l.*
           ,(l.m1*r.m1 + l.m2*r.m2 + l.m3*r.m3) as model
        from
           sd1.mat as l,  sd1.wgt as r
    ;quit;

    /*___
    |  _ \
    | |_) |
    |  _ <
    |_| \_\
     _  _      ____                           _   _
    | || |    |  _ \    ___  __ _ _   _  __ _| |_(_) ___  _ __
    | || |_   | |_) |  / _ \/ _` | | | |/ _` | __| |/ _ \| `_ \
    |__   _|  |  _ <  |  __/ (_| | |_| | (_| | |_| | (_) | | | |
       |_|(_) |_| \_\  \___|\__, |\__,_|\__,_|\__|_|\___/|_| |_|
                               |_|
    */

    %utlfkil(d:/xpt/mat.xpt);

    %utl_rbegin;
    parmcards4;
    library(haven);
    library(SASxport);
    mat<-read_sas("d:/sd1/mat.sas7bdat")
    wgt<-read_sas("d:/sd1/wgt.sas7bdat")[,c(3,4,5)]
    mat$MODEL<-mat$M1*wgt$M1 + mat$M2*wgt$M2 + mat$M3*wgt$M3
    write.xport(mat,file="d:/xpt/mat.xpt");
    ;;;;
    %utl_rend;

    libname xpt xport "d:/xpt/mat.xpt";

    proc print data=xpt.mat;
    run;quit;

    %utlfkil(d:/xpt/mat.xpt);

    * this way of calling R allows you to pass SAS macro text and macro variables to R;

    %utl_submit_r64('
    library(haven);
    library(SASxport);
    mat<-read_sas("d:/sd1/mat.sas7bdat");
    wgt<-read_sas("d:/sd1/wgt.sas7bdat")[,c(3,4,5)];
    mat$MODEL<-mat$M1*wgt$M1 + mat$M2*wgt$M2 + mat$M3*wgt$M3;
    write.xport(mat,file="d:/xpt/mat.xpt");
    ');

    libname xpt xport "d:/xpt/mat.xpt";

    proc print data=xpt.mat;
    run;quit;

    /*___     ____              _
    | ___|   |  _ \   ___  __ _| |
    |___ \   | |_) | / __|/ _` | |
     ___) |  |  _ <  \__ \ (_| | |
    |____(_) |_| \_\ |___/\__, |_|
                             |_|
    */

    * note this uses the exact same sas code;

    %utlfkil(d:/xpt/mat.xpt);

    %utl_submit_r64('
    library(haven);
    library(sqldf);
    library(SASxport);
    mat<-read_sas("d:/sd1/mat.sas7bdat");
    wgt<-read_sas("d:/sd1/wgt.sas7bdat");
    mat<-sqldf("
           select
              l.*
              ,(l.m1*r.m1 + l.m2*r.m2 + l.m3*r.m3) as MODEL
           from
              mat as l, wgt as r
           ");
    write.xport(mat,file="d:/xpt/mat.xpt");
    ');

    libname xpt xport "d:/xpt/mat.xpt";

    proc print data=xpt.mat;
    run;quit;

    /*_____   _______ _   _  ___  _   _
    |  _ \ \ / /_   _| | | |/ _ \| \ | |
    | |_) \ V /  | | | |_| | | | |  \| |
    |  __/ | |   | | |  _  | |_| | |\  |
    |_|    |_|   |_| |_| |_|\___/|_| \_|

    */

    /*__                                           _   _
     / /_     _ __  _   _    ___  __ _ _   _  __ _| |_(_) ___  _ __
    | `_ \   | `_ \| | | |  / _ \/ _` | | | |/ _` | __| |/ _ \| `_ \
    | (_) |  | |_) | |_| | |  __/ (_| | |_| | (_| | |_| | (_) | | | |
     \___(_) | .__/ \__, |  \___|\__, |\__,_|\__,_|\__|_|\___/|_| |_|
             |_|    |___/           |_|
    */

    %utlfkil(d:/xpt/mat.xpt);

    %utl_submit_py64_39('
    import pandas as pd;
    import pyreadstat;
    import xport;
    import xport.v56;
    wgt, meta = pyreadstat.read_sas7bdat("d:/sd1/wgt.sas7bdat");
    wgt.drop("_TYPE_", axis=1, inplace=True);
    wgt.drop("_MODEL_", axis=1, inplace=True);
    print(wgt);
    mat, meta = pyreadstat.read_sas7bdat("d:/sd1/mat.sas7bdat");
    print(mat);
    mat["MODEL"] = (mat * wgt.iloc[0,]).sum(axis=1);
    print(mat);
    ds = xport.Dataset(mat, name="mat");
    with open("d:/xpt/mat.xpt", "wb") as f:;
    .    xport.v56.dump(ds, f);
    ');

    libname xpt xport "d:/xpt/mat.xpt";

    proc print data=xpt.mat;
    run;quit;

    /*____               _   _                             _
    |___  |  _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
       / /  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
      / /_  | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
     /_/(_) | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    %utl_pybegin39;
    parmcards4;
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
    wgt, meta = pyreadstat.read_sas7bdat("d:/sd1/wgt.sas7bdat");
    wgt.drop("_TYPE_", axis=1, inplace=True);
    wgt.drop("_MODEL_", axis=1, inplace=True);
    mat, meta = pyreadstat.read_sas7bdat("d:/sd1/mat.sas7bdat");
    print(mat);
    print(wgt);
    wgt.info();
    res = pdsql("""
           select
              l.*
              ,(l.m1*r.m1 + l.m2*r.m2 + l.m3*r.m3) as MODEL
           from
              mat as l inner join wgt as r
           """)
    print(res);
    ds = xport.Dataset(res, name='CLASS')
    with open('c:/temp/class.xpt', 'wb') as f:
        xport.v56.dump(ds, f)
    ;;;;
    %utl_pyend39;

    libname xpt xport "d:/xpt/mat.xpt";

    proc print data=xpt.mat;
    run;quit;

    /*
     _ __ ___   __ _  ___ _ __ ___  ___
    | `_ ` _ \ / _` |/ __| `__/ _ \/ __|
    | | | | | | (_| | (__| | | (_) \__ \
    |_| |_| |_|\__,_|\___|_|  \___/|___/

    */

    %macro utl_submit_py64_39(
          pgm
         ,return=  /* name for the macro variable from Python */
         )/des="Semi colon separated set of python commands - drop down to python";

      * delete temporary files;
      %utlfkil(%sysfunc(pathname(work))/py_pgm.py);
      %utlfkil(%sysfunc(pathname(work))/stderr.txt);
      %utlfkil(%sysfunc(pathname(work))/stdout.txt);

      filename py_pgm "%sysfunc(pathname(work))/py_pgm.py" lrecl=32766 recfm=v;
      data _null_;
        length pgm  $32755 cmd $1024;
        file py_pgm ;
        pgm=&pgm;
        semi=countc(pgm,";");
          do idx=1 to semi;
            cmd=cats(scan(pgm,idx,";"));
            if cmd=:". " then
               cmd=trim(substr(cmd,2));
             put cmd $char384.;
             putlog cmd ;
          end;
      run;quit;
      %let _loc=%sysfunc(pathname(py_pgm));
      %let _stderr=%sysfunc(pathname(work))/stderr.txt;
      %let _stdout=%sysfunc(pathname(work))/stdout.txt;
      filename rut pipe  "c:\Python39\python.exe &_loc 2> &_stderr";
      data _null_;
        file print;
        infile rut;
        input;
        put _infile_;
      run;
      filename rut clear;
      filename py_pgm clear;

    data _null_;
        file print;
        infile "%sysfunc(pathname(work))/stderr.txt";
        input;
        put _infile_;
      run;
      filename rut clear;
      filename py_pgm clear;

      * use the clipboard to create macro variable;
      %if "&return" ^= "" %then %do;
        filename clp clipbrd ;
        data _null_;
         length txt $200;
         infile clp;
         input;
         putlog "*******  " _infile_;
         call symputx("&return",_infile_,"G");
        run;quit;
      %end;

    %mend utl_submit_py64_39;


    %macro utl_rbegin;
    %utlfkil(c:/temp/r_pgm.r);
    %utlfkil(c:/temp/r_pgm.log);
    filename ft15f001 "c:/temp/r_pgm.r";
    %mend utl_rbegin;


    %macro utl_rend(returnvar=N);
    * EXECUTE THE R PROGRAM;
    options noxwait noxsync;
    filename rut pipe "D:\r412\R\R-4.1.2\bin\R.exe --vanilla --quiet --no-save < c:/temp/r_pgm.r 2> c:/temp/r_pgm.log";
    run;quit;
      data _null_;
        file print;
        infile rut recfm=v lrecl=32756;
        input;
        put _infile_;
        putlog _infile_;
      run;
      filename ft15f001 clear;
      * use the clipboard to create macro variable;
      %if %upcase(%substr(&returnVar.,1,1)) ne N %then %do;
        filename clp clipbrd ;
        data _null_;
         length txt $200;
         infile clp;
         input;
         putlog "macro variable &returnVar = " _infile_;
         call symputx("&returnVar.",_infile_,"G");
        run;quit;
      %end;
    data _null_;
      file print;
      infile rut;
      input;
      put _infile_;
      putlog _infile_;
    run;quit;
    data _null_;
      infile "c:/temp/r_pgm.log";
      input;
      putlog _infile_;
    run;quit;
    %mend utl_rend;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
