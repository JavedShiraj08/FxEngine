# FxEngine
Refinitiv Matching Engine Exercise

# Overview:
The project FxEngine contains the spark scala solution for a Trading Engine assessment problem by TCS.

## Build:
The project uses maven for dependency management. The main dependencies are:
- scala 2.13.0
- spark-core_2.13
- spark-sql_2.13

Jars created:
- Jar: FxEngine-1.0.0-SNAPSHOT.jar
- UberJar: FxEngine-1.0.0-SNAPSHOT-jar-with-dependencies.jar

Command To Run Application Locally with uber jar (assuming your are inside repo):
```
spark-submit --name "TradeEngine" --master local --class com.refinitivengine.tcs.TradeEngine target/FxEngine-1.0.0-SNAPSHOT-jar-with-dependencies.jar input/inputOrders.csv
```
where `input/inputOrders.csv` is path to the input orders file.


## Code walkthrough:
1. The main class is TradeEngine.scala
2. There are two case classes defined i.e. `Order.scala` and `Match.scala` which represents an order and a match respectively. Since the schema is well defined we will be using Dataset[Order] and Dataset[Match] instead of a DataFrame in our program
3. Import the required libraries and packages in the main class
4. Create a Spark session on local and configure it
5. Define the schema for reading the input file by Encoding the `Order.scala` case class
6. Create the dataframe which will read the csv file and load the data into it. Convert the dataframe to `Dataset[Order]` and store in variable `inputOrdersDF`
7. Create datasets for empty orderbook `Dataset[order]` and empty matchedOrders `Dataset[Match]`
8. Applying the loop on `inputOrdersDF` dataset and do follwing for each input order:
    - Evaluate `type` of matching order and `quanity`
    - Filter matching orders from orderbook using the type and quantity evaluated above and store in `matchingOrders`.
    - Find best match out of the matching orders as following:
        - if orginal order is SELL then look for **Highest** BUY, so sort matching orders by price **descensing**
        - if orginal order is BUY then look for **Lowest** SELL, so sort matching orders by price **ascending**
    - If `matchingOrders` is empty then no match found. Simply place the input order in orderBook.
    - If `matchingOrders` is not empty then take first and create a `Match`, place it in matched orders DataSet[Match] and remove the  
      matched order from orderBook.
9. Write out matches orders in `output/matchedOrders.csv` and open/unmatched orders in `output/OpenOrders.csv`
