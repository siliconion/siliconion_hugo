---
title: "A tale of code not maintained"
date: 2023-01-15T11:00:00-05:00
draft: false
---

This is a cautionary tale about how code deteriorates. 

The following is a code snippets that is running in production, re-written to obscure proprietary info. 

```node
function get_logs(dbName, uuid) {
    switch (dbName) {
        case 'A':
            // TODO MIGRATE TO API
            db = get_database_conn_A()
        case 'B':
            return call_service_B_API(uuid)
            break
        default:
            return null
    }
    
    sql = `SELECT * FROM logs WHERE uuid = ${uuid}` 
    db.query(sql, function(err, result) {
        if (err) throw err
        return result
    })
}
```

The code is very confusing and very hard to read. What it really does is either query database A, or call service B for data. It's a simple if-else.


## Initial commit

How can the code be this bad? The code was not started this way. The initial commit was:

```node
function get_logs(dbName, uuid) {
    switch (dbName) {
        case 'A':
            db = get_database_conn_A()
            break
        case 'B':
            db =  get_database_conn_B()
            break
        case 'C':
            db = get_database_conn_C()
            break
        default:
            return null
            break
    }
    
    sql = `SELECT * FROM logs WHERE uuid = ${uuid}` 
    db.query(sql, function(err, result) {
        if (err) throw err
        return result
    })
}
```
This code actually do make sense. 

## Change 1: Using API for service B

Ideally, service should not directly connect to other services' databases. At a fast-paced startup, sometimes you have to take on tech debt. This is justifiable.

The good things is, the developer actually came back and made an API. But, for service B only. This is again justifiable. Any improvement is better than nothing. 

However, the code now becomes:
```node
function get_logs(dbName, uuid) {
    switch (dbName) {
        case 'A':
            // TODO MIGRATE TO API
            db = get_database_conn_A()
            break
        case 'B':
            return call_service_B_API(uuid)
        case 'C':
            // TODO MIGRATE TO API
            db = get_database_conn_C()
            break
        default:
            return null
            break
    }
    
    sql = `SELECT * FROM logs WHERE uuid = ${uuid}` 
    db.query(sql, function(err, result) {
        if (err) throw err
        return result
    })
}
```
The functionality works fine. However, the code became confusing. The main problems are:

* The parameter dbName is not correct anymore, as we are not connecting to database B. The parameter should be renamed to coreServices.
* The code to query database after the switch statement is confusing. The early return for service B is not highlighted. 

## Change 2: Depreciation of service C
The next thing happened was that, service C got depreciated. This makes the code extra ridiculous, as the switch statement can simply be an if-else.
```node
function get_logs(dbName, uuid) {
    switch (dbName) {
        case 'A':
            // TODO MIGRATE TO API
            db = get_database_conn_A()
            break
        case 'B':
            return call_service_B_API(uuid)
        default:
            return null
            break
    }
    
    sql = `SELECT * FROM logs WHERE uuid = ${uuid}` 
    db.query(sql, function(err, result) {
        if (err) throw err
        return result
    })
}
```

## Refactors that should've happened
When API for service B is introduced, to solve the two problems mentioned above, the code should've been refactored to:
```node
function get_logs(sercieName, uuid) {
    if (sercieName == 'B') {
        return call_service_B_API(uuid)
    } else {
        db = get_database_conn(service)
        return query_database_for_logs(db, uuid)
    }
}

function get_database_conn(sercieName){
    if (sercieName == 'A') {
        return get_database_conn_A()
    } else if (sercieName == 'C') {
        return get_database_conn_C()
    } else {
      throw new Error()
    }
}

function query_database_for_logs(db_coon, uuid){
    sql = `SELECT * FROM logs WHERE uuid = ${uuid}` 
    db.query(sql, function(err, result) {
        if (err) throw err
        return result
    })
}
```

And when service C was removed, the code can further be:

```node
function get_logs(serviceName, uuid) {
    if (serviceName == 'A')
        return get_logs_from_service_A(uuid)
    else if (serviceName == 'B') {
        return get_logs_from_service_B(uuid)
    }
    return null
}

function get_logs_from_service_A(uuid) {
    // query service A database
}

function get_logs_from_service_B(uuid) {
    // call service B API
}
```

## More modular code
Each time a change is introduced, the code should've been refactored. Why didn't it happen? Because people often have tunnel vision. And, the code review tool also justifies the tunnel vision. Here are the merge request for the two changes:
[merge request file diff for using API for service B](https://github.com/siliconion/wireboundcloud/pull/1/files)
[merge request file diff for removing service C](https://github.com/siliconion/wireboundcloud/pull/2/files)
Any reviewer that only read the title of the MR and the diff, which honestly is all of us, would pass the code review. 

On top of "read the whole function/file after making a change", "make sure variable name makes sense", more modular mode can alleviate this problem. The initial commit could've been broken down more, as:
```node
function get_logs(sercieName, uuid) {
    db = get_database_conn(service)
    return query_database_for_logs(db, uuid)
}

function get_database_conn(sercieName){
    switch (dbName) {
        case 'A':
            return get_database_conn_A()
        case 'B':
            return get_database_conn_B()
        case 'C':
            return get_database_conn_C()
        default:
          throw new Error()
    }
}

function query_database_for_logs(db_coon, uuid){
    sql = `SELECT * FROM logs WHERE uuid = ${uuid}` 
    db.query(sql, function(err, result) {
        if (err) throw err
        return result
    })
}
```

This will force the refactoring happen when we introduce API for service B. 