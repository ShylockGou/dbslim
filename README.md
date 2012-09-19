# dbslim based on Mark S. version here https://sourceforge.net/projects/dbslim/

## Use slim testrunner

!define TEST_SYSTEM {slim} 


## Fitnesse Test Setup

Oracle database connection example given below.

!*> setup
|import|
|fitnesse.slim.test|
|fitnesse.fixtures|
|slim|

*!

| script | Db Slim Setup |!-oracle.jdbc.driver.OracleDriver-!| jdbc:oracle:thin:@host_name:1521:database_name | username | password |


## Fitnesse Test Update Query Examples

!define currentCustomerId {101}
!define currentCustomerUsername {user101}
!define currentCustomerPassword {password101}


| script | Db Slim Update Query | Update customer_balances Set balance = 10000 Where customer_id = ${currentCustomerId} |
| check | rowsUpdated; | 1 |



|Query:Db Slim Update Query | Update customer_balances Set balance = 10000 Where customer_id = ${currentCustomerId} |
| rowsUpdated | 
| 1 |

| script | Db Slim Update Query | Update customer_balances Set balance = 10000 Where customer_id = ${currentCustomerId} |
| check | rowsUpdated; | 1 |


## Fitnesse Test Select Query Examples

|Query:Db Slim Select Query | select * from customer_balances where customer_id = ${currentCustomerId}|
| customer_id | balance |
|${currentCustomerId}| 10000 |

!define dbQuerySelectCustomerbalance (
select * from customer_balances 
where customer_id = ${currentCustomerId}
)

| script | Db Slim Select Query | ${dbQuerySelectCustomerbalance} |
| check | data; | 0 | 0 | ${currentCustomerId} |
| check | dataByColumnIndexAndRowIndex; | 0 | 0 | ${currentCustomerId} |
| check | data By Column Index | 0 | and Row Index | 0 | ${currentCustomerId} |
| check | data; | customer_id | 0 | ${currentCustomerId} |
| check | dataByColumnNameAndRowIndex; | customer_id | 0 | ${currentCustomerId} |
| check | dataByColumnNameAndRowIndex; | balance | 0 | >0 |
| $currentCustomerbalance= | dataByColumnNameAndRowIndex; | balance | 0 |
| check | dataByColumnNameAndRowIndex; | balance | 0 | $currentCustomerbalance |


| script | Db Slim Select Query | select * from customer_balances where customer_id = ${currentCustomerId} |
| check | dataByColumnNameAndRowIndex; | balance | 0 | $currentCustomerbalance |


|Query:Db Slim Select Query | select * from customer_balances where customer_id = ${currentCustomerId} |
| customer_id | balance |
|${currentCustomerId}| $currentCustomerbalance |


## Fitnesse Test Wait Query Examples

error case (query result has no rowcount)
!|script                                                                                                                                |
|start|db slim setup               |!-oracle.jdbc.driver.OracleDriver-!| jdbc:oracle:thin:@host_name:1521:db_name | username | password |
|start|db slim wait query          |select 0 as somethingelse from dual|20000                                                           |
|start|db slim select query        |select 2 as result from dual                                                                        |
|show |dataByColumnNameAndRowIndex;|result                             |0                                                               |


timeout case
!|script                                                                                                                                |
|start|db slim setup               |!-oracle.jdbc.driver.OracleDriver-!| jdbc:oracle:thin:@host_name:1521:db_name | username | password |
|start|db slim wait query          |select 0 as rowcount from dual |20000                                                               |
|start|db slim select query        |select 2 as result from dual                                                                        |
|show |dataByColumnNameAndRowIndex;|result                         |0                                                                   |


success case
!|script                                                                                                                                |
|start|db slim setup               |!-oracle.jdbc.driver.OracleDriver-!| jdbc:oracle:thin:@host_name:1521:db_name | username | password |
|start|db slim wait query          |select 1 as rowcount from dual |20000                                                               |
|start|db slim select query        |select 2 as result from dual                                                                        |
|show |dataByColumnNameAndRowIndex;|result                         |0                                                                   |
