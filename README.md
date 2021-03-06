This repository is related to a [SESUG 2016 paper](https://analytics.ncsu.edu/sesug/2016/CC-269_Final_PDF.pdf) titled: 

- *Cover Your Assumptions with Custom %str(W)ARNING Messages*

The central concept is not novel: similar material was presented at SGF in [2008](http://www2.sas.com/proceedings/forum2008/106-2008.pdf) and [2013](http://support.sas.com/resources/papers/proceedings13/350-2013.pdf). 
The only part that will be truly new in this paper is that many more potential applications of custom WARNING messages will be described (covered in the [Wiki tab](https://github.com/srosanba/sas-customwarnings/wiki)). 

## The Basic Idea
Suppose you need to convert a numeric variable `GENDER` into a character variable `SEX`.
```
data dm;
   set raw.demo;
   if gender = 1 then sex = 'M';
   else if gender = 2 then sex = 'F';
run;
```
This code does not account for values of `GENDER` other than `1` and `2`. But, other values do show up in the data from time to time: *missing*, `3`, `99`, etc. The question is, how do we *easily* protect ourselves from such dastardly data? With custom WARNING messages, of course!
```
data dm;
   set raw.demo;
   if gender = 1 then sex = 'M';
   else if gender = 2 then sex = 'F';
   else put 'W' 'ARNING: unaccounted for value of ' gender=;
run;
```
If any values of `GENDER` other than `1` or `2` shows up in the data, this second `ELSE` will generate a WARNING in the log. Something like, 
```
WARNING: unaccounted for value of gender=99
```
As long as you are using log checking programs (and you *should* be using log checking programs), the dastardly data value is quickly identified and dealt with. 

## What's with the 'W' 'ARNING'?
Suppose we were to write the second `ELSE` as follows:
```
else put 'WARNING: unaccounted for value of ' gender=;
```
This would guarantee that the word WARNING always appears in our logs. This is not what we want. We only wish to have the word WARNING appear when there is actually something henke going on in our data. So, we break the word up into chunks which are only ever put back together when there is data worthy of being warned about. 

## Something Similar in Macro Land
Because macros just write text, the following naive macro equivalent will not work.
```
%put 'W' 'ARNING: something bad happened';
```
When this line executes what will appear in the log is:
```
'W' 'ARNING: something bad happened'
```
The macro processor didn't see the `'` symbols as anything special and faithfully reproduced them in the log. So, how do we achieve the `'W' 'ARNING'` effect in macro land? There are multiple solutions, but my personal favorite is:
```
%put %str(W)ARNING: something bad happened;
```
When this line executes what will appear in the log is:
```
WARNING: something bad happened
```

## Closing Remarks
There are many potential applications of custom warning messages (covered in the [Wiki tab](https://github.com/srosanba/sas-customwarnings/wiki)). Typing out the first few keystrokes of code to generate a custom WARNING messages can be a bit tedious (quotes, percents, and parens), so I encourage you to turn these fragments into SAS abbreviations (Ctrl+Shift+A). Maybe you create the following:
```
putwarn    -> put 'W' 'ARNING: unexpected value for ' ;
macputwarn -> %put %str(W)ARNING: ;
ohbother   -> %put %str(W)ARNING: someone interrupted me in the middle of something';
```
Having these abbreviations at the ready will cause you to put more custom warning messages in your programs, making you a safer and faster programmer. 
