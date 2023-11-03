# Enphase
```
import requests
import oracledb
import my_secrets

print(oracledb.__version__)

connection = oracledb.connect(user=my_secrets.user, password=my_secrets.password, dsn=my_secrets.dsn)

sql = ('insert into enphase(data) '
        'values(:json_data)')

url1 = "https://envoy/api/v1/production/inverters"

headers = {
"accept" : "application/json",
"Authorization": "Bearer "+my_secrets.token
}

try:
    r = requests.get(url1, headers=headers, verify=False)
    r.raise_for_status()
    json_data = r.text 

    print(json_data);
    with connection.cursor() as cursor:
        # execute the insert statement
        cursor.execute(sql, [json_data])
        # commit work
        connection.commit()
except requests.exceptions.HTTPError as err:
    raise SystemExit(err)
```
