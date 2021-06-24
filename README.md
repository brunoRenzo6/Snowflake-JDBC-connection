# Snowflake-JDBC-connection
Source Code:
https://www.snowflake.com/blog/ability-to-connect-to-snowflake-with-jdbc/

My implementation:

```java
package com.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class SnowflakeDriverExample {

    public static void main(String[] args) throws Exception{
        // write your code here
        System.out.println("Hello World!");

        // get connection
        System.out.println("Create JDBC connection");
        Connection connection = getConnection();
        System.out.println("Done creating JDBC connectionn");

        // create statement
        System.out.println("Create JDBC statement");
        Statement statement = connection.createStatement();
        System.out.println("Done creating JDBC statementn");

        // create a table
        System.out.println("Create demo table");
        statement.executeUpdate("create or replace table demo(C1 STRING)");
        //statement.close();  You cant close it yet, bacause we going to use statment further on still
        System.out.println("Done creating demo tablen");

        // insert a row
        System.out.println("Insert 'hello world'");
        statement.executeUpdate("insert into demo values ('hello world')");
        statement.executeUpdate("insert into demo values ('hello world2')");
        //statement.close();  You cant close it yet, bacause we going to use statment further on still
        System.out.println("Done inserting 'hello world'n");

        // query the data
        System.out.println("Query demo");
        ResultSet resultSet = statement.executeQuery("SELECT * FROM demo");
        System.out.println("Metadata:");
        System.out.println("================================");

        // fetch metadata
        ResultSetMetaData resultSetMetaData = resultSet.getMetaData();
        System.out.println("Number of columns=" + resultSetMetaData.getColumnCount());
        for (int colIdx = 0; colIdx < resultSetMetaData.getColumnCount(); colIdx++)
        {
            System.out.println("Column " + colIdx + ": type=" +
                    resultSetMetaData.getColumnTypeName(colIdx+1));
        }

        // fetch data
        System.out.println("nData:");
        System.out.println("================================");
        int rowIdx = 0;
        while(resultSet.next())
        {
            System.out.println("row " + rowIdx + ", column 0: " + resultSet.getString(1));
            rowIdx++;
        }
        statement.close();
    }
    private static Connection getConnection() throws SQLException
    {
        try
        {
            Class.forName("com.snowflake.client.jdbc.SnowflakeDriver");
        }
        catch (ClassNotFoundException ex)
        {
            System.err.println("Driver not found");
        }
        // build connection properties
        Properties properties = new Properties();
        properties.put("user", "brunoRenzoSnowF");     // replace "" with your username
        properties.put("password", "xxxxxxxxxx"); // replace "" with your password
        properties.put("account", "lx59561.us-east-2.aws");  // replace "" with your account name
        properties.put("db", "modak_db");       // replace "" with target database name
        properties.put("schema", "PUBLIC");   // replace "" with target schema name
        //properties.put("tracing", "on");

        // create a new connection
        String connectStr = System.getenv("SF_JDBC_CONNECT_STRING");
        // use the default connection string if it is not set in environment
        if(connectStr == null)
        {
            connectStr = "jdbc:snowflake://lx59561.us-east-2.aws.snowflakecomputing.com"; // replace accountName with your account name
        }
        return DriverManager.getConnection(connectStr, properties);
    }
}
```
