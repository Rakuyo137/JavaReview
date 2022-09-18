JVM调优



linux 

top



jps

jinfo

jstack

jstat  -gc

jmap



oom定位

jmap -histo 15380  类产生的对象,及占用的内存



分析工具

导出hprof

jmap -dump:format=b,file=2022822.hprof 15380

工具

mat   jhat  jprofile

jvisualvm



在线定位工具

arthas



系统cup高,如何定位问题

top -hp  看那个进程在占cpu

