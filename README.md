# utl_nice_hash_example_of_rolling_count_of_dates_plus-minus_2_days_of_current_date
Nice hash example of rolling count of dates plus-minus 2 days of current date. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Nice hash example of rolling count of dates plus-minus 2 days of current date

    Same results WPS/SAS

    see Paul Dorfmans reply on end of post
    
    github
    https://tinyurl.com/ybn2qjms
    https://github.com/rogerjdeangelis/utl_nice_hash_example_of_rolling_count_of_dates_plus-minus_2_days_of_current_date

    see
    https://tinyurl.com/yc4k4qs5
    https://communities.sas.com/t5/Base-SAS-Programming/Count-observation-by-ID-over-rolling-3-day-time-period/m-p/455977

    Novinosrin profile
    https://communities.sas.com/t5/user/viewprofilepage/user-id/138205

    Rolling count of dates within +/- 2 day window ofor each date.  date2,date1,date   and   dat,date1,date2

    INPUT
    =====

    WORK.HAVE total obs=18

           INPUT
    =======================  |  Numeric      RULES
    Obs     ID     DATE      |  Date         =====
                             |
      1    208    06DEC2017  |  21159  1   singleton no other odates within +/- 2 days
      2     45    12DEC2013  |  19704  1   singleton
      3     45    03JAN2014  |  19726  2   one additional data within +/- 2 date
      4     45    03JAN2014  |  19726  2   one additional data within +/- 2 date
      5     45    19JAN2014  |  19742  1   singleton
      6    185    15NOV2017  |  21138  1   singleton
      7    185    04DEC2017  |  21157  1   singleton
      8    185    11JAN2018  |  21195  4   This one, 21195(next), 21196, 21197 (21198 is out of window)
      9    185    11JAN2018  |  21195  4   This one, 21195(backwards), 21196, 21197 (21198 is out of window)
     10    185    12JAN2018  |  21196  5   This one, 21195(backwards),21195(backwards),21197,21198
     11    185    13JAN2018  |  21197  5   This one, 21196(backwards),21195(backwards),21195(backwards),21198
     12    185    14JAN2018  |  21198  3   This one, 21197(backwards),21196(backwards)
     13    185    13MAR2018  |  21256  1   singleton
     14     56    14JUN2016  |  20619  1   singleton
     15     56    07JUL2016  |  20642  2   This one and 20642(next)
     16     56    07JUL2016  |  20642  2   This one and 20642(backwards)
     17    214    16JUL2012  |  19190  2   This and 19191(next)
     18    214    17JUL2012  |  19191  2   This and 19190(backwards)


    PROCESS
    =======

    * Simple and elegant;

    data want;
    if _n_=1 then do;
    if 0 then set have;
    dcl hash H (dataset:'have',multidata:'y') ;
       h.definekey  ("Id") ;
       h.definedata ("date") ;
       h.definedone () ;
    end;
    set have(rename=(date=_date));
    count=0;
    rc = h.find();
    do while(rc = 0);
      if intnx('days',_date,-3+1)<=date<=intnx('days',_date,+3-1) then count+1;
      rc = h.find_next();
    end;
    drop rc date;
    run;quit;

    OUTPUT
    ======

    WORK.WANT total obs=18

    Obs     ID    _DATE    COUNT

      1    208    21159      1
      2     45    19704      1
      3     45    19726      2
      4     45    19726      2
      5     45    19742      1
      6    185    21138      1
      7    185    21157      1
      8    185    21195      4
      9    185    21195      4
     10    185    21196      5
     11    185    21197      5
     12    185    21198      3
     13    185    21256      1
     14     56    20619      1
     15     56    20642      2
     16     56    20642      2
     17    214    19190      2
     18    214    19191      2

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    data have;
      input id date date9.;
    cards4;
    208 06DEC2017
    045 12DEC2013
    045 03JAN2014
    045 03JAN2014
    045 19JAN2014
    185 15NOV2017
    185 04DEC2017
    185 11JAN2018
    185 11JAN2018
    185 12JAN2018
    185 13JAN2018
    185 14JAN2018
    185 13MAR2018
    056 14JUN2016
    056 07JUL2016
    056 07JUL2016
    214 16JUL2012
    214 17JUL2012
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    data wrk.wantwps;
    if _n_=1 then do;
    if 0 then set wrk.have;
    dcl hash H (dataset:"wrk.have",multidata:"y") ;
       h.definekey  ("Id") ;
       h.definedata ("date") ;
       h.definedone () ;
    end;
    set wrk.have(rename=(date=_date));
    count=0;
    rc = h.find();
    do while(rc = 0);
      if intnx("days",_date,-3+1)<=date<=intnx("days",_date,+3-1) then count+1;
      rc = h.find_next();
    end;
    drop rc date;
    run;
    run;quit;
    ');
    
        Paul Dorfman <sashole@bellsouth.net>
    9:29 PM (13 hours ago)
    to SAS-L, me
    Roger,

    Simple and elegant indeed. Note that the "keynumerate" operation loop can be coded more concisely (and without using a return code variable) as follows:

    if h.find() = 0 then do until (h.find_next() ne 0);
      if ... then ... count ... ;
    end;

    That works in both 9.2/9.3 and 9.4. Or, if you're running 9.4, it is even simpler since you can ditch the FIND+FIND_NEXT combo and just use the DO_OVER method:

    do while (h.do_over() = 0) ;
      if ... then ... count ... ;
    end ;

    Your solution will work with data neither sorted nor grouped by ID. That means, however, that the hash table will end up with as many items as there are input records. But if it is intrinsically grouped (as in your sample file), it can be re-coded to r
    . The I/O will remain the same (2 passes through the input file).

    data want (drop=date) ;
      if _n_=1 then do;
        dcl hash H (multidata:'y') ;
         h.definekey  ("date") ;
         h.definedone () ;
        dcl hiter ih ("h") ;
      end ;
      do _n_ = 1 by 1 until (last.id) ;
        set have ;
        by ID notsorted ;
        h.add() ;
      end ;
      do _n_ = 1 to _n_ ;
        set have (rename=(date=_date)) ;
        do count = 0 by 0 while (ih.next() = 0) ;
          if _date - 2 <= date <= _date + 2 then count + 1 ;
        end ;
        output ;
      end ;
      h.clear() ;
    run;

    Best regards



