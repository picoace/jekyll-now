---
layout: post
title: OS cpu check
---

# OS cpu check

## 1. OS cpu check

```
##################################################################################################### 
#  NAME : chk_cpu_for_license.sh                                                                    # 
#  DATE : 2012  05.16                                                                               #
#  DESCRIPTION : license check shell                                                                # 
#  VERSION : REPORTER  : DATE      : PATCH INFO                                                     #
#  -------  -------   ----------  ----------------------------------------                          #    
#  V1.00   : JW.K    : 20120516  : first deployed                                                   #
#  V1.01   : KM.K    : 20120522  : fix Locale to C (print prtconf info in C locale not EUC )        #
#  V1.01   : KM.K    : 20120522  : use /usr/sbin/psrinfo not psrinfo (problem with path)            #    
#  V1.11   : JW.K    : 20120531  : check license info for HP 11.31                                  #          
#  V1.111  : JY.K    : 20120531  : shell version print, license-info print                          #          

if [ $# -ne 1 ];then
echo "Uasge :`basename $0` productname"
echo "EX> `basename $0` JEUS|WEBTOB|TMAX"
exit $BAD_ARGS
fi

PRODUCTNAME=`echo $1 |tr \"a-z\" \"A-Z\" `
VERSION=1.111

export LC_ALL=C
#komyungkok senior reporting for locale

OS=`uname`
#OS="aaa"

PHYSICAL_CNT=""
CORE_CNT=""
THREAD_CNT=""
NCPU=""
#IS_EVERY_OK="OK" #

__tmaxLogo()
{
echo "###################################################################################################"
printf "#%-97s#\n" " Check CPU INFO for license in $OS (Version:$VERSION)"
echo "#                                                                                                 #"
echo "#                                                                                                 #"
echo "#                                                                                        TMAXSOFT #"
echo "###################################################################################################"
}

__ADD()
{
echo ""
echo "###########################################"
echo "# ADDITIONAL INFO $OS"      
echo "###########################################"
}

__INFO()
{
echo ""
echo "###########################################"
echo "# CHECK RESULT INFO $OS"      
echo "###########################################"
}


__NCPU()
{
echo ""
echo "###########################################"
echo "# NCPU $OS"      
echo "###########################################"
}

__EXCEL()
{
echo ""
echo "###########################################"
echo "# EXCEL $OS"
echo "###########################################"
LEN_HW=${#1}
LEN_OS=${#2}
LEN_PRODUCT=${#3}
LEN_VERSION=${#4}
LEN_HOSTNAME=${#5}
LEN_HOSTID=${#6}
LEN_IPADDRESS=${#7}
LEN_NCPU=${#8}



#HOSTNAME@: 9+A60G 8@Z8.7N GO@Z ?V3DGa8i 8@Z8. >H5G4B  hostname@L ?C6'4B A$7D@L 1zA|
printf "%-${LEN_HW}s %-${LEN_OS}s %-${LEN_PRODUCT}s %-${LEN_VERSION}s %-8s %-${LEN_HOSTID}s %-${LEN_IPADDRESS}s %-${LEN_NCPU}s\n" "H/W" "O/S" "Product" "Version" "HOSTNAME" "HOSTID" "IPADDRESS" "NCPU" 
printf "%-${LEN_HW}s %-${LEN_OS}s %-${LEN_PRODUCT}s %-${LEN_VERSION}s %-8s %-${LEN_HOSTID}s %-${LEN_IPADDRESS}s %-${LEN_NCPU}s\n" "$1" "$2" "$3" "$4" "$5" "$6" "$7" "$8"



#printf "%-20s%-30s%-30s%-25s%-10s%-10s%-30s%-30s\n" "H/W" "O/S" "Product" "Version" "HOSTNAME" "HOSTID" "IPADDRESS" "NCPU" 
#printf "%-20s%-30s%-30s%-25s%-10s%-10s%-30s%-30s\n" "$1" "$2" "$3" "$4" "$5" "$6" "$7" "$8"

}
__GET_PRO_EDITION()
{
case $1 in
    WEBTOB)
                #20120531 change
                PRODUCTEDITION=`wsadmin -C "wi" | grep License | awk -F: '{print $2}'|sed 's/^ *//'`
                PRODUCTVERSION=`wsadmin -v | awk  '{print $1,$2,$3,$4,$5,$6}'`
                NCPURUN=ncpu
                #echo licenseinfo 
                echo ""
                echo "###############################################"
                printf "# %s\n" "WEBTOB License Information (file: $WEBTOBDIR/license/license.dat)"
                echo "###############################################"
                wsadmin -i $WEBTOBDIR/license/license.dat | grep -v "##" | grep -v "License Information" | grep -v "^$"
         
                

    ;;
    JEUS)
                PRODUCTEDITION=`jeusadmin -licenseinfo | grep EDITION | awk -F: '{print $2}'|sed 's/^ *//'`
                PRODUCTVERSION=`jeusadmin -version | grep -v prepend|sed 's/^ *//'`
                NCPURUN=ncpu
                #echo licenseinfo
                echo ""
                echo "###############################################"
                printf "# %s\n" "JEUS License Information"
                echo "###############################################"
                jeusadmin -licenseinfo

    ;;
    TMAX)
                PRODUCTEDITION=`tmadmin -i $TMAXDIR/license/license.dat | grep Edition |awk -F: '{print $2}' | sed 's/^ *//'`
                PRODUCTVERSION=`tmadmin -v | awk -F, '{print $1}'`
                NCPURUN=$TMAXDIR/license/ncpu

    ;;
    *)
        echo "error"
    ;;

esac
}



__tmaxLogo

linux_check() #@1 : $# @2 : 1bAX5G4B argc @3 : Usage descrption @4 : Example descrption
{
        #echo "============ROW DATA===="
        #cat /proc/cpuinfo
        #uname -a 


        __GET_PRO_EDITION $PRODUCTNAME
        PHYSICAL_CNT=`cat /proc/cpuinfo | grep "physical id" | sort -u | wc -l` 
        CORE_CNT=`cat /proc/cpuinfo | grep "cpu cores" | tail -1 | cut -d: -f2` 
        CORE_ID=`cat /proc/cpuinfo | grep "core id" | sort -u | head -1 | cut -d: -f2`
#       THREAD_CNT=`expr $CORE_CNT / $PHYSICAL_CNT`
        THREAD_CNT=`egrep "^core|^physical" /proc/cpuinfo | awk '{if((NR%2)==0) print $0 "," ; else print $0 }' | awk 'BEGIN {RS=","} {print $1,$2,$3,$4,$5,$6,$7,$8}' | grep ^physical | sort | uniq -c | head -1 | awk '{print $1}'`
        NCPU=`expr $PHYSICAL_CNT \* $CORE_CNT \* $THREAD_CNT`

        #for excel format#
        MODEL=`grep "model name" /proc/cpuinfo | awk -F: '{print $2}' | uniq |awk '{print $1,$2}'`
        OSVERSION=`uname -r`
        #JEUSEDITION=`jeusadmin -licenseinfo | grep EDITION | awk -F: '{print $2}'|sed 's/^ *//'`
        #JEUSVERSION=`jeusadmin -buildversion | grep -v prepend|sed 's/^ *//'`
        HOSTNAME=`hostname`
        HOSTID=`$NCPURUN | grep HOSTID | awk -F: '{print $2}'|sed 's/^ *//'`
        IPADDRESS=`$NCPURUN | grep IPADDRESS | awk -F: '{print $2}'|awk '{print $1}'|sed 's/^ *//'`
        

        

}
sun_check() #@1 : $# @2 : 1bAX5G4B argc @3 : Usage descrption @4 : Example descrption
{
        __GET_PRO_EDITION $PRODUCTNAME
        PHYSICAL_CNT=`/usr/sbin/psrinfo -p` #Komyungkok senior reporting (psrinfo -> /usr/sbin/psrinfo)
        CORE_TOT=`kstat -m cpu_info | grep -w core_id | sort -u | wc -l| awk '{print $1}'`
        CORE_CNT=`expr $CORE_TOT / $PHYSICAL_CNT`
        CORE_TOT2=`kstat -m cpu_info | grep -w core_id | wc -l `
        THREAD_CNT=`expr $CORE_TOT2 / $CORE_TOT `
        NCPU=`expr $PHYSICAL_CNT \* $CORE_CNT \* $THREAD_CNT`

        MODEL=`uname -i`
        OSVERSION=`uname -sr`
        HOSTNAME=`hostname`
        HOSTID=`$NCPURUN | grep HOSTID | awk -F: '{print $2}'|sed 's/^ *//'`
        IPADDRESS=`$NCPURUN | grep IPADDRESS | awk -F: '{print $2}'|awk '{print $1}'|sed 's/^ *//'`
}


hp_check() #@1 : $# @2 : 1bAX5G4B argc @3 : Usage descrption @4 : Example descrption
{
        __GET_PRO_EDITION $PRODUCTNAME
        #if hp-ux 11.31
        OSBUILDVERSION=`uname -r`
        if [ $OSBUILDVERSION != "B.11.31" ];then
        echo "THIS SHELL FOR ONLY FOR HP 11.31 NOT $OSBUILDVERSION !!!!"
        exit 65
        fi

        PHYSICAL_CNT=`machinfo | grep -i processor | grep -v logical | awk '{print $1}'`
        CORE_CNT=`machinfo | grep -i processor | grep  logical | awk -F'(' '{print $2}' | awk '{print $1}'`
        THREAD_INFO=`/usr/sbin/kctune | grep lcpu_attr `
        IS_THREAD_ON=`echo $THREAD_INFO | grep 1 | wc -l`
        if [ $IS_THREAD_ON -eq 1 ];then
        THREAD_CNT=2
        else
        THREAD_CNT=1
        fi
        NCPU=`expr $PHYSICAL_CNT \* $CORE_CNT \* $THREAD_CNT`

        MODEL=`machinfo | grep -i model | awk -F':' '{print $2}'|tr -d ' '`
        OSVERSION=`uname -sr`
        HOSTNAME=`hostname`
        HOSTID=`$NCPURUN | grep HOSTID | awk -F: '{print $2}'|sed 's/^ *//'`
        IPADDRESS=`$NCPURUN | grep IPADDRESS | awk -F: '{print $2}'|awk '{print $1}'|sed 's/^ *//'`
}




aix_check() #@1 : $# @2 : 1bAX5G4B argc @3 : Usage descrption @4 : Example descrption
{
        __GET_PRO_EDITION $PRODUCTNAME
        PHYSICAL_CNT=`lscfg -vp |grep -ip proc |grep PROC|grep WAY | wc -l` # if null@O<v55 @V@=
        CORE_CNT=`lscfg -vp | grep proc | wc -l` # coreD+?nF. 

        IMSI_PROC=`lscfg -vp | grep proc | head -1 | awk '{print $1}'` # @S=C GA7N<<<- 08CD(proc0)

        IS_SMT=`lsattr -El $IMSI_PROC | grep smt_enabled| awk '{print $2}'`
        SMT_CNT=`lsattr -El $IMSI_PROC | grep smt_threads| awk '{print $2}'`

        LPAR_ONLINE=`lparstat -i | grep "Online Virtual CPUs" | awk -F':' '{print $2}'`        
        LPAR_MAX=`lparstat -i | grep "Maximum Virtual CPUs" | awk -F':' '{print $2}'` 

        RATIO=$(echo "scale=2;$LPAR_MAX/$LPAR_ONLINE*100" | bc)

        if [ $IS_SMT = "true" ];then
        THREAD_CNT=$SMT_CNT
        else
        THREAD_CNT=1
        fi

        if [ "$PHYSICAL_CNT" = "" ];then
        PHYSICAL_CNT="I don't know"
        NCPU=`expr $CORE_CNT \* $THREAD_CNT`
        else
        NCPU=`expr $CORE_CNT \* $THREAD_CNT`
        fi
        
        TOT_CORES=$LPAR_MAX
        CURRENT_CORES=$LPAR_ONLINE


        MODEL=`prtconf | grep "Processor Type" | awk -F: '{print $2}'|sed 's/^ *//'`
        OSVERSION=`uname -rv`
        #PRODUCTEDITION=`jeusadmin -licenseinfo | grep EDITION | awk -F: '{print $2}'|sed 's/^ *//'`
        #PRODUECTVERSION=`jeusadmin -buildversion | grep -v prepend|sed 's/^ *//'`
        HOSTNAME=`hostname`
        HOSTID=`$NCPURUN | grep HOSTID | awk -F: '{print $2}'|sed 's/^ *//'`
        IPADDRESS=`$NCPURUN | grep IPADDRESS | awk -F: '{print $2}'|awk '{print $1}'|sed 's/^ *//'`


        __ADD
        echo  "LART STATUS"
        echo  "POSSIBLE CORES = $TOT_CORES"
        echo  "CURRENT ASSIGNED CORES = $CURRENT_CORES"
        echo  "LPART RATIO = $RATIO($LPAR_MAX(MAX)/$LPAR_ONLINE(ONLINE))"
        echo  "SMT ENABLE = $IS_SMT"
        echo  "SMT THREAD CNT = $SMT_CNT"        


        
}




case $OS in
    Linux)
        linux_check

    ;;
    HP-UX)
        hp_check

    ;;
    IBM)

    ;;
    AIX)
        aix_check

    ;;
    SunOS)
        sun_check 
    ;; 
    *)
        echo "error"
    ;;

esac

__INFO


if [ "${OS}" = "AIX" ];then
echo  "Physical CPU Count : $PHYSICAL_CNT "
echo  "Core Count : $CORE_CNT "
echo  "Thread Count per Core : $THREAD_CNT "

echo  "======================"
NCPURESULT="$CORE_CNT (cores) * $THREAD_CNT(smt) = $NCPU(NCPU)"
echo "$NCPURESULT"
NCPURESULT2=`echo "$NCPURESULT"|sed 's/^ *//'`

__NCPU
$NCPURUN
__EXCEL "$MODEL" "$OSVERSION" "$PRODUCTEDITION" "$PRODUCTVERSION" "$HOSTNAME" "$HOSTID" "$IPADDRESS" "$NCPURESULT2"

else
echo  "Physical CPU Count : $PHYSICAL_CNT "
echo  "Core Count per Physical CPU : $CORE_CNT "
echo  "Thread Count per Core : $THREAD_CNT "

echo  "======================"
NCPURESULT="$PHYSICAL_CNT(cpu) * $CORE_CNT(cores) * $THREAD_CNT(thread) = $NCPU(NCPU)"
echo "$NCPURESULT"
NCPURESULT2=`echo "$NCPURESULT"|sed 's/^ *//'`

__NCPU
$NCPURUN

__EXCEL "$MODEL" "$OSVERSION" "$PRODUCTEDITION" "$PRODUCTVERSION" "$HOSTNAME" "$HOSTID" "$IPADDRESS" "$NCPURESULT2"

fi


```



