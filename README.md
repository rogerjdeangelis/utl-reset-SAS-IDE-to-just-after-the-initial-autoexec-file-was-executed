# utl-reset-SAS-IDE-to-just-after-the-initial-autoexec-file-was-executed
Reset-sas-IDE-to-just after the initial autoexec file was executed 
   Reset sas IDE to just after the initial autoexec file was executed

    This very similar to repeated batch processing in an interactive environment.

    GitHub
    https://tinyurl.com/5emr5kkr
    https://github.com/rogerjdeangelis/utl-reset-SAS-IDE-to-just-after-the-initial-autoexec-file-was-executed


    /********************************************************************************/
    /*                                                                              */
    /* DESCRIPTION: Reset SAS Interactive Development Environment                   */
    /*              to just after autoexec.sas                                      */
    /*                                                                              */
    /* Best with the 1980s Original Display Manager Environment                     */
    /*                                                                              */
    /* This code attempts to restore the state of SAS display manager to the state  */
    /* right after the autoexec was executed.                                       */
    /*                                                                              */
    /*  _                   _                                                       */
    /* (_)_ __  _ __  _   _| |_                                                     */
    /* | | `_ \| `_ \| | | | __|                                                    */
    /* | | | | | |_) | |_| | |_                                                     */
    /* |_|_| |_| .__/ \__,_|\__|                                                    */
    /*         |_|                                                                  */
    /*                                                                              */
    /* TEST CASE                                                                    */
    /*                                                                              */
    /*      proc format;                                                            */
    /*        value $indentified_gender                                             */
    /*        'M'='MALE'                                                            */
    /*        'F'='FEMALE'                                                          */
    /*        'O'='OTHER'                                                           */
    /*      ;run;                                                                   */
    /*                                                                              */
    /*      %macro temp;                                                            */
    /*        %put +>>>>>>>>>>>>>>>><<<<<<<<<<<<<<<<+;                              */
    /*        %put | USER MACRO                     |;                              */
    /*        %put +>>>>>>>>>>>>>>>><<<<<<<<<<<<<<<<+;                              */
    /*      %mend temp;                                                             */
    /*      %temp;                                                                  */
    /*                                                                              */
    /*      libname here '.';                                                       */
    /*                                                                              */
    /*      filename mine 'mine.txt';                                               */
    /*      filename your 'your.txt';                                               */
    /*                                                                              */
    /*      %let one=first;                                                         */
    /*      %let two=second;                                                        */
    /*      %let thr=third;                                                         */
    /*                                                                              */
    /*      data odd even;                                                          */
    /*        do x = 1 to 10;                                                       */
    /*          if mod(x,2) then output odd;                                        */
    /*          else output even;                                                   */
    /*        end;                                                                  */
    /*      run;                                                                    */
    /*                                                                              */
    /*                                                                              */
    /*     options cmplib=(work.functions);                                         */
    /*     * functions in formats Rick Langston;                                    */
    /*                                                                              */
    /*     proc fcmp outlib=work.functions.charRight;                               */
    /*       function charRight(txt $) $32;                                         */
    /*         ryght=right(txt);                                                    */
    /*       return(ryght);                                                         */
    /*     endsub;                                                                  */
    /*     run;quit;                                                                */
    /*                                                                              */
    /*     * put the function in a format;                                          */
    /*     proc format;                                                             */
    /*       value $charRight other=[charRight()];                                  */
    /*     run;quit;                                                                */
    /*                                                                              */
    /*                                                                              */
    /*                                                                              */
    /*   OBJECTS CREATED BY PROGRAM                                                 */
    /*   ==========================                                                 */
    /*                                                                              */
    /*    proc catalog cat=work.formats;                                            */
    /*    content;                                                                  */
    /*    run;quit;                                                                 */
    /*                                                                              */
    /*              Contents of Catalog WORK.FORMATS                                */
    /*                                                                              */
    /*    #    Name                  Type               Create Date                 */
    /*    ----------------------------------------------------------                */
    /*    1    INDENTIFIED_GENDER    FORMATC    04/24/2021 18:11:57                 */
    /*                                                                              */
    /*                                                                              */
    /*    proc catalog cat=work.sasmacr;                                            */
    /*    content;                                                                  */
    /*    run;quit;                                                                 */
    /*                                                                              */
    /*                            Contents of Catalog WORK.SASMACR                  */
    /*              */                                                              */
    /*    #    Name    Type             Create Date                                 */
    /*    -----------------------------------------                                 */
    /*    2    TEMP    MACRO    04/24/2021 18:11:57                                 */
    /*                                                                              */
    /*    proc contents data=work._all_;                                            */
    /*    run;quit;                                                                 */
    /*                                                                              */
    /*              Member   Obs, Entries                                           */
    /*     Name     Type      or Indexes   Vars  Label     File Size                */
    /*                                                                              */
    /*     EVEN     DATA          5         1                  128KB                */
    /*     FORMATS  CATALOG       1                             17KB                */
    /*     ODD      DATA          5         1                  128KB                */
    /*     SASGOPT  CATALOG       0                              5KB                */
    /*     SASMACR  CATALOG       2                            413KB                */
    /*     FUNCTION TABLE      ** FCMP FUNCTIONS ***                                */
    /*                                                                              */
    /*     %put _user_                                                              */
    /*                                                                              */
    /*     GLOBAL ONE first                                                         */
    /*     GLOBAL SYS_SQL_IP_ALL -1  Could not delete                               */
    /*     GLOBAL SYS_SQL_IP_STMT    Could not delete                               */
    /*     GLOBAL THR third                                                         */
    /*     GLOBAL TWO second                                                        */
    /*                                                                              */
    /*   _ __  _ __ ___   ___ ___  ___ ___                                          */
    /*  | `_ \| `__/ _ \ / __/ _ \/ __/ __|                                         */
    /*  | |_) | | | (_) | (_|  __/\__ \__ \                                         */
    /*  | .__/|_|  \___/ \___\___||___/___/                                         */
    /*  |_|                                                                         */
    /*                                                                              */
    /*  %utl_reset;                                                                 */
    /*                                                                              */
    /*  Last code that was executed was the autoexec file                           */
    /*                                                                              */
    /*  a.  reset ODS destination, path and escapechar                              */
    /*  b.  clear all libnames & filenames                                          */
    /*  c.  clear TEMP compiled macros                                              */
    /*  d.  delete user-defined global symbols                                      */
    /*  e.  clear the WORK library (all memtypes)                                   */
    /*  f.  reset system options (CONFIG TO SITE SPECS)                             */
    /*  g.  reset all graphics options                                              */
    /*  h.  initialize titles/footnotes                                             */
    /*  i.  initialize DMS interactive SAS windows                                  */
    /*  j.  launch original autoexec.sas                                            */
    /*                                                                              */
    /*                                                                              */
    /*   OBJECTS REMOVED                                                            */
    /*   ===============                                                            */
    /*                                                                              */
    /*    proc catalog cat=work.formats;                                            */
    /*    content;                                                                  */
    /*    run;quit;                                                                 */
    /*                                                                              */
    /*              Contents of Catalog WORK.FORMATS (DOES NOT EXIST)               */
    /*                                                                              */
    /*    #    Name                  Type               Create Date                 */
    /*    ----------------------------------------------------------                */
    /*                                                                              */
    /*    proc catalog cat=work.sasmacr;                                            */
    /*    content;                                                                  */
    /*    run;quit;                                                                 */
    /*                                                                              */
    /*              Contents of Catalog WORK.SASMACR                                */
    /*                                                                              */
    /*    #    Name    Type             Create Date                                 */
    /*    -----------------------------------------                                 */
    /*    UTL_RESET    MACRO    04/24/2021 18:35:30                                 */
    /*                                                                              */
    /*                                                                              */
    /*    proc contents data=work._all_;                                            */
    /*    run;quit;                                                                 */
    /*                                                                              */
    /*              Member   Obs, Entries                                           */
    /*     Name     Type      or Indexes   Vars  Label     File Size                */
    /*     ---------------------------------------------------------                */
    /*     SASGOPT  CATALOG       0    COULD NOT DELETE                             */
    /*     SASMACR  CATALOG       1                                                 */
    /*                                                                              */
    /*     %put _user_                                                              */
    /*                                                                              */
    /*     GLOBAL SYS_SQL_IP_ALL -1  Could not delete                               */
    /*     GLOBAL SYS_SQL_IP_STMT    Could not delete                               */
    /*                                                                              */
    /*                                                                              */
    /*    NOTE: Deleting WORK.FUNCS (memtype=DATA).                                 */
    /*    NOTE: Deleting WORK.FUNCTIONS (memtype=DATA).                             */
    /*                                                                              */
    /*                                                                              */
    /*                                                                              */
    /********************************************************************************/

    %macro utl_reset;

      %local work_compiled_macros;

      ods path reset;                         /* RESET ODS PATH & DEST  */
      ods _all_ close;
      ods listing;
      ods escapechar='03'x;                   /* RESET ODS escape char  */

      libname _all_ clear;                    /* CLEAR ALL LIBREFS      */
      proc printto; run;                      /* RESTORE default printto*/
      filename _all_ clear;                   /* & THEN CLEAR FILE REFS */

      proc catalog catalog=work.sasmacr;      /* CLEAR COMPILED MACROS  */
        contents out=_tempcompmacs;
      quit;
      proc sql noprint;
        select distinct(name) into :work_compiled_macros separated by ' '
        from _tempcompmacs
        where upcase(name) ne 'UTL_RESET';
      quit;

      %if &sqlobs gt 0 %then %do;
        proc catalog catalog=work.sasmacr;
          delete &work_compiled_macros / ET = macro;
        quit;
      %end;

     data _delgvars;                         /* CLEAR ALL GLOBAL VARS  */
        set sashelp.vmacro
            (where=( scope='GLOBAL' &
                     name not in ('AFTLAST' 'SYSROOT' 'SYSLEVEL' 'SYS_SQL_IP_STMT' 'SYS_SQL_IP_ALL') ));
      run;
      proc sort data=_delgvars nodupkey;
        by name;
      run;
      data _null_;
        set _delgvars;
        call execute('%symdel ' !! trim(left(name)) !! ';');
      run;

      proc datasets lib=work kill nolist nodetails;
      quit;                                   /* CLEAR TEMP DSs & FMTs  */

                                              /* RESET SYSTEM OPTIONS   */
      options mautosource mrecall;  /* nec after clearing compiled macs  */

      goptions reset=all;                     /* RESET GRAPHICS OPTIONS */

      title;
      footnote;                               /* INITIALIZE TITLES/FTNTS*/


                                              /* RUN AUTOEXEC FILE      */

      %if %length(%sysfunc(getoption(autoexec))) ne 0 %then %do;
          %include "%sysfunc(getoption(autoexec))";
      %end;


    %mend utl_reset;


    /* Test case


    proc format;
      value $indentified_gender
      'M'='MALE'
      'F'='FEMALE'
      'O'='OTHER'
    ;run;

    %macro temp;
      %put +>>>>>>>>>>>>>>>><<<<<<<<<<<<<<<<+;
      %put | USER MACRO                     |;
      %put +>>>>>>>>>>>>>>>><<<<<<<<<<<<<<<<+;
    %mend temp;
    %temp;

    libname here '.';

    filename mine 'mine.txt';
    filename your 'your.txt';

    %let one=first;
    %let two=second;
    %let thr=third;

    data odd even;
      do x = 1 to 10;
        if mod(x,2) then output odd;
        else output even;
      end;
    run;

    options cmplib=(work.functions);
    * functions in formats Rick Langston;

    proc fcmp outlib=work.functions.charRight;
      function charRight(txt $) $32;
        ryght=right(txt);
      return(ryght);
    endsub;
    run;quit;

    * put the function in a format;
    proc format;
      value $charRight other=[charRight()];
    run;quit;

    %utl_reset;

    */
