
# hibernate-lazy-initialization-exception
The main goal of this project is to show typical problem with lazy 
fetching - `LazyInitializationException`.

_Reference_: http://docs.jboss.org/hibernate/orm/5.3/userguide/html_single/Hibernate_User_Guide.html  
_Reference_: http://download.oracle.com/otn-pub/jcp/persistence-2_2-mrel-spec/JavaPersistence.pdf?AuthParam=1538677370_fbd3bf5088a447d9721552ad131ae9c7

# preface
**Fetching**, essentially, is the process of grabbing data from the 
database and making it available to the application.

We have two approaches to loading:
* `public enum FetchType { LAZY, EAGER };`
    * `EAGER` - requirement on the persistence provider
      runtime that the value must be eagerly fetched
    * `LAZY` - hint to the persistence provider runtime.


# annotations
|Annotation   |Default   |
|---|---|
|`@OneToOne`   |`EAGER`   |
|`@OneToMany`   |`LAZY`   |
|`@ManyToOne`   |`EAGER`   |
|`@ManyToMany`   |`LAZY`   |
|`@Basic`   |`EAGER`   |

# LazyInitializationException
**LazyInitializationException** indicates an attempt to access not-yet-fetched data outside 
 of a session context.  
For example, when an uninitialized proxy or collection is accessed after the session was closed.

# tests
* `noSession_onlyGetter` - there is no exception because we don't fire
loading: 
    ```
    repository.findById(1).map(Employee::getIssues);
    ```
doesn't consume the collection so there is no need to load it.

* `noSession_consumingCollection`
    ```
    repository.findById(1).map(Employee::getIssues).map(Collection::size);
    ```
    consumes the collection so query is fired and produces the exception
    - there is no open session.
    
* `transactional()`
    ```
    repository.findById(1).map(Employee::getIssues).map(Collection::size);
    ```
    we consume the collection but we also provide open session by
    `@Transactional` annotation