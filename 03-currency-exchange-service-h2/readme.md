Access or clone the git hub 
-------------------------------
git clone https://github.com/pponnusw1/aws-microservices.git


Install docker desktop for windows 64 bit
--------------------------------------------
Request from OneIt.

Install Git bash for windows
--------------------------------
REquest from OneIt.


aws microservices handson
-------------------------------

docker network create network-exchange
docker run --publish 8000:8000 --network network-exchange --name=currency-exchange-service pponnusw/currency-exchange-service:1.0
docker run --publish 8100:8100 --network network-exchange --env CURRENCY_EXCHANGE_URI=http://currency-exchange-service:8000 pponnusw/currency-conversion-service:1.0



URL access from docker container
------------------------------------
•http://localhost:8100/api/currency-conversion-microservice/currency-converter/from/EUR/to/INR/quantity/10
•http://localhost:8000/api/currency-exchange-microservice/currency-exchange/from/USD/to/INR


docker push pponnusw/currency-exchange-service:1.0
docker push pponnusw/currency-conversion-service:1.0

