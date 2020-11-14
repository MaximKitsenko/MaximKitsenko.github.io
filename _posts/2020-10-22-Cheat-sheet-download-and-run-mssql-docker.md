# MS SQL Docker Cheat Sheet

## Download image and run

download and run:

`docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest`

if you use cmd replace `'` to `"` :

`docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=yourStrong(!)Password" -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest`

run already downloaded image:

`docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=yourStrong(!)Password" -p 1433:1433 -d 5ced205176bc` where `5ced205176bc` is image hash

## Connect to the server via management studio

Importnat thing,- `localhost:1433` - is oncorrect, use `localhost,1433` in MSSMS to connect to `localhost` server, port `1433`.

![_config.yml]({{ site.baseurl }}/images/2020-10-22-Cheat-sheet-download-and-run-mssql-docker/1.png)

## Stop

`docker stop hopeful_hermann` where `hopeful_hermann` is container name

## Start

`docker start hopeful_hermann` where `hopeful_hermann` is container name