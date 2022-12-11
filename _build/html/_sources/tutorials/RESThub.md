# So what is REST API?
70% of all APIs are REST APIs (basically the infrastructure of any company is based on it). REST is an archetectural syle. REST is based on HTTP (network protocol that is secure & encrypted end-to-end). The people at CERN came up with http, which is stateless (you don't have to establish a server connection - everything is handeled by the request/response) - each operation=new connection. 

HTTP means 
* you send a request to a particular url with message, etc and you 
* get something back from the server based on the request as a response (in JSON format). 
* your browser translates this json data to display information,e.g. a picture in the web.

# What is RESTHub (https://github.com/valdasraps/resthub/)?
Previously, in order to connect to an Oracle DB, such as HGCALDB, you had to have an oracle client, a running server managing some connection states, where transactions can go across multiple calls.. This is very slow!

RESTHub allows you to have SQL pattern (query) power the https request, and the response will be the result of the SQL command in JSON format.

# How to use RESTHub for HGCAL DB?
## Making sure you can run the python client
* `wget https://raw.githubusercontent.com/valdasraps/resthub/master/clients/python/src/main/python/rhapi.py`
* `python3 rhapi.py --help`
* Using `python3 rhapi.py --url=http://dbloader-tracker:8113` locally doesn't work because theres no connection. But using it in `lxplus` shows `['trker_cmsr', 'trker_int2r']`
* FROM NOW ON, USE THIS ON LXPLUS.
* `python3 rhapi.py --url=http://dbloader-hgcal:8113 hgc_int2r`
* Now `hgc_int2r` takes the place of 
* Execute query: by adding the query in `" <SQL QUERY>` to the commands above.

# Suppose in the normal sqldeveloper we use the sql query below. How can we use translate this to RESTHub? 
```
/* see everythong in the hcg_cern_sensor_ivl */
select * from CMS_HGC_HGCAL_COND.hgc_cern_sensor_iv;
```
The easiest way is using conditions tables (not secure: http):
(for secure use dbloader through DCA as in https://github.com/valdasraps/cmsdbldr/blob/1f2e1ed14886c9b16e8ea8fbf9ff52d7cc9a00f6/src/main/python/cmsdbldr_client.txt)

    
1.1 Find the available conditions
`python3 rhapi.py --url=http://dbloader-hgcal:8113 "select * from hgc_int2r.conditions c"`

1.2 Find the header row columns: `python3 rhapi.py --url=http://dbloader-hgcal:8113 "select * from hgc_int2r.conditions c" | head -1`

1.3. Find the conditions tables that have `IV`: `python3 rhapi.py --url=http://dbloader-hgcal:8113 "select * from hgc_int2r.conditions c" | grep IV`

<p align="center">
<img src="images/RESTHUB_conditions_IV.png" width="1000" height="100" border="10"/>
</p>

2. Clearly we want `HGC CERN SENSOR IV`. `RESTHub` allows us to use that table either by its ID (0th column), or `DATABASE_TABLE` (2nd column) (potentially by `CONDITION_TABLE` by I don't know how to yet). Let's use `DATABAE_TABLE`, where for us `DATABASE_TABLE` is `HGC_CERN_SENSOR_IV`.

3. Modify our query to use this table
````
python3 rhapi.py --url=http://dbloader-hgcal:8113 "select * from hgc_int2r.conditions c where c.DATABASE_TABLE =  'HGC_CERN_SENSOR_IV'" 
```


* What if I want to use it locally? Just change the port name to the one you're using, after to do the ssh command to connect earlier.  
