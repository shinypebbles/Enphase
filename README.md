# Enphase
```
CREATE TABLE  "ENPHASE" 
   (	"SAMPLE_DATE" DATE DEFAULT sysdate, 
	"DATA" CLOB, 
	 CONSTRAINT "ENPHASE_JSON_CHK" CHECK (data IS JSON) ENABLE
   )
/

CREATE TABLE  "ENPHASEPRODUCTION" 
   (	"SAMPLE_DATE" DATE DEFAULT sysdate, 
	"DATA" CLOB, 
	 CONSTRAINT "ENPHASEPROD_JSON_CHK" CHECK (data IS JSON) ENABLE
   )
/
```
```
import requests
import oracledb
import my_secrets

print(oracledb.__version__)

connection = oracledb.connect(user=my_secrets.user, password=my_secrets.password, dsn=my_secrets.dsn)

sql1 = ('insert into enphase(data) '
        'values(:json_data)')
sql2 = ('insert into enphaseproduction(data) '
        'values(:json_data)')

url1 = "https://envoy/api/v1/production/inverters"
url2 = "https://envoy/production.json?details=0"

headers = {
"accept" : "application/json",
"Authorization": "Bearer "+my_secrets.token
}

try:
    r = requests.get(url1, headers=headers, verify=False, timeout=5)
    r.raise_for_status()
    json_data = r.text 

    print(json_data);
    with connection.cursor() as cursor:
        # execute the insert statement
        cursor.execute(sql1, [json_data])
        # commit work
        connection.commit()
except requests.exceptions.HTTPError as err:
    raise SystemExit(err)

try:
    r = requests.get(url2, headers=headers, verify=False, timeout=5)
    r.raise_for_status()
    json_data = r.text 

    print(json_data);
    with connection.cursor() as cursor:
        # execute the insert statement
        cursor.execute(sql2, [json_data])
        # commit work
        connection.commit()
except requests.exceptions.HTTPError as err:
    raise SystemExit(err)
```
