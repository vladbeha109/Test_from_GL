Script created in order to take metrics from the OS about CPU% and Memory% usage

-------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------
Where we can use this sript?
-------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------


Linux/Unix OS


-------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------
How you can run the script?
-------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------


The easy way to run the scrip is copy code from my from git https://github.com/vladbeha109/Test_from_GL and run it on your 
machine, but before that do not forget to add permissions - chmod +x <file_with_my_script>, the examples of using is below:

./<file_with_my_script> cpu

or

./<file_with_my_script> mem

- If you choose cpu you will see next output:

system.cpu.idle:99.2
system.cpu.user:0.27
system.cpu.guest:0.00
system.cpu.iowait:0.02
system.cpu.stolen:0.03
system.cpu.system:0.44

Where values in %

- If you choose mem you will see next output:

virtual.total:983M
virtual.used:113M
virtual.free:303M
virtual.shared:760K
swap.total:0B
swap.used:0B
swap.free:0B

-------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------
What behind of the scene?
-------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------

###I named the variables and assigned values to them:

- "a" value, it is parameter which user pass to the script, it can be cpu or mem

- "b" and "c" values are using for validation purpose, I compare the value in the "a" variable with "b" and "c" to be sure that user writes the correct parameter.

a=$1
b=[Cc][Pp][Uu]
c=[Mm][Ee][Mm]

##then I am getting metrics from /proc/stat for each value

y=`head -1 /proc/stat |  awk '{print $2}'`

##I assemble all values in order to get value in % for  user, system, guest...etc 

let sum=$y+$k+$l+$m+$n+$o+$p+$s+$l+$k

#below formula for calculation CPU per different type of usage(system,user...), I make division for every variable to sum it 
##variables and then I do multiplication on 100, so that output will be in percentege

result=`echo "${i}/${sum}*100" | bc -l | xargs printf %.2f`

##all values I send in /tmp/result(temporary solution, so that I can edit outputs), then I edit data with sed and awk command 

sed ':a;N;$!ba;s/\n/,/g' /tmp/result | awk -F ',' '{print " system.cpu.idle:" substr($4,1,4) " system.cpu.user:" substr($1,1,5) " system.cpu.guest:" substr($9,1,4) " system.cpu.iowait:" substr($5,1,4) " system.cpu.stolen:" substr($8,1,4) " system.cpu.system:"  substr($3,1,4)}' | tr ' ' '\n' && rm /tmp/result

##When you write mem in "a" scenarious the next:

elif [[ $a == $c ]]; then
free -mh | sed ':a;N;$!ba;s/\n/,/g' | awk -F ' ' '{print " virtual.total:" substr($7,1,7) " virtual.used:" substr($8,1,7) " virtual.free:" substr($9,1,7) " virtual.shared:" substr($10,1,7) " swap.total:" substr($13,1,7) " swap.used:" substr($14,1,7) " swap.free:" substr($15,1,7)}' | tr ' ' '\n'

##above I edit output of command free -mh, if in "a" value not "cpu" or "mem" you will receive advice what should be written

else echo "You should use './script Cpu' or './script Mem'"


