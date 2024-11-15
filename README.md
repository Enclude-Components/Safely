# Safely Class
A wrapper class for the system Database class which is secure by default. Also allows for database operations to be mocked in unit tests.

## Deploy

<a href="https://githubsfdeploy.herokuapp.com?owner=Enclude-Components&repo=Safely&ref=main">
  <img alt="Deploy to Salesforce"
       src="https://raw.githubusercontent.com/afawcett/githubsfdeploy/master/deploy.png">
</a>

## Usage

### Construction
```java
// Constructed with default parameters
Safely db = new Safely();
```

By default, constructing the class with no additional parameters will run SOQL and DML:
- In USER_MODE
- Silently stripping away inaccessible fields
- AllOrNone (bulk transactions will fail as a whole, if any of the transactions fail)

```java
// The various builder methods, invoke one or more to set the parameters
Safely db = new Safely()
    .allOrNone(false) // Allows bulk operation failures
    .throwIfRemovedFields(true) // Throws `Safely.RemovedFieldsException` if any fields are stripped
    .throwIfRemovedFields( // Throws `Safely.RemovedFieldsException` only if specified fields are stripped
        'Account',
        new Set<String>{ 'AccountNumber', 'AccountSource' }
    )
    .withAccessLevel(System.AccessLevel.SYSTEM_MODE) // Runs queries and DML in SYSTEM_MODE
```

### Queries
```java
Safely db = new Safely();
Account[] accounts = (Account[])db.doQuery('SELECT Id from Account');
Account[] accountsWithBinds = (Account[])db.doQueryWithBinds(
    'SELECT Id from Account WHERE Id = :myAccountId',
    new Map<String, Object> {
        'myAccountId' => '001AP00000U22m3YAB'
    }
);
```

### DML
```java
// Insert Methods
Safely db = new Safely();
Database.SaveResult singleResult = db.doInsert(new Account(Name = 'My Account'));
Database.SaveResult[] bulkResult = db.doInsert(
    new List<Account> {
        new Account(Name = 'My Bulk Account 1'),
        new Account(Name = 'My Bulk Account 2')
    }
);
```
```java
// Update Methods
Safely db = new Safely();
Database.SaveResult singleResult = db.doUpdate(myUpdatedRecord);
Database.SaveResult[] bulkResult = db.doUpdate(myUpdatedRecordList);
```
```java
// Delete Methods
Safely db = new Safely();
Database.DeleteResult singleResult = db.doDelete(myDeletedRecord);
Database.DeleteResult[] bulkResult = db.doDelete(myDeletedRecordList);
```
```java
// Upsert Methods
Safely db = new Safely();
Database.UpsertResult singleResult = db.doUpsert(myUpsertedRecord);
Database.UpsertResult[] bulkResult = db.doUpsert(myUpsertedRecordList);
Database.UpsertResult singleResult = db.doUpsert(
    myUpsertedRecordWithExternalId,
    My_Custom_Object__c.My_External_ID_Field__c
);
Database.UpsertResult[] bulkResult = db.doUpsert(
    myUpsertedRecordWithExternalIdList,
    My_Custom_Object__c.My_External_ID_Field__c
);
```