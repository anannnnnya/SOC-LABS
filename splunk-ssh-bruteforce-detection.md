1. What i built
I set up kali + a splunk VM, splunk ingesing /var/log/auth.log

2. What i did:
* Made ssh failed logins - 2 manually and 5 hydra brute force
* Made mini password list for hydra
* Searched the failed passwords in splunk
* Made src_ip with rex pattern

3. Results:
   
Query:
....

index=authlogs "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| table _time src_ip

...

With the help of rex a field called src_ip was created by looking specified pattern. The results were demonstrated in a table with newly created src_ip field and time.

Query:
...

index=authlogs "Failed password" 
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)" 
| bin _time span=10s 
| stats count by _time src_ip

...

This query grouped the attempts into 10s buckets. 

4. What this means:
A human typing takes seond which can be seen in the manually typed attempts, a difference of 7 sec. While a brute force tool attempt multiple attacks in under a second.

5. Next steps:
Pull in Metasploitable's log too and check for a successful login following a burst of failures.
