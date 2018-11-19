---
title: MySQL - Manual - SQL Syntax - 13.1.12 - CREATE EVENT 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE EVENT
---

MySQL `CREATE EVENT` 语法

<!--more-->

```
CREATE
    [DEFINER = { user | CURRENT_USER }]
    EVENT
    [IF NOT EXISTS]
    event_name
    ON SCHEDULE schedule
    [ON COMPLETION [NOT] PRESERVE]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'string']
    DO event_body;

schedule:
    AT timestamp [+ INTERVAL interval] ...
    | EVERY interval
    [STARTS timestamp [+ INTERVAL interval] ...]
    [ENDS timestamp [+ INTERVAL interval] ...]

interval:
    quantity {YEAR | QUARTER | MONTH | DAY | HOUR | MINUTE |
    WEEK | SECOND | YEAR_MONTH | DAY_HOUR | DAY_MINUTE |
    DAY_SECOND | HOUR_MINUTE | HOUR_SECOND | MINUTE_SECOND}
```

This statement creates and schedules a new event. The event will not run unless the Event Scheduler
is enabled. For information about checking Event Scheduler status and enabling it if necessary, see
Section 23.4.2, “Event Scheduler Configuration”.

CREATE EVENT requires the EVENT privilege for the schema in which the event is to be created. It might
also require the SET_USER_ID or SUPER privilege, depending on the DEFINER value, as described later in
this section.

The minimum requirements for a valid CREATE EVENT statement are as follows:
* The keywords CREATE EVENT plus an event name, which uniquely identifies the event in a database
schema.
* An ON SCHEDULE clause, which determines when and how often the event executes.
* A DO clause, which contains the SQL statement to be executed by an event.

This is an example of a minimal CREATE EVENT statement:
```
CREATE EVENT myevent
    ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR
    DO
        UPDATE myschema.mytable SET mycol = mycol + 1;
```

The previous statement creates an event named myevent. This event executes once—one hour following
its creation—by running an SQL statement that increments the value of the myschema.mytable table's
mycol column by 1.

The event_name must be a valid MySQL identifier with a maximum length of 64 characters. Event
names are not case-sensitive, so you cannot have two events named myevent and MyEvent in the same
schema. In general, the rules governing event names are the same as those for names of stored routines.
See Section 9.2, “Schema Object Names”.

An event is associated with a schema. If no schema is indicated as part of event_name, the default
(current) schema is assumed. To create an event in a specific schema, qualify the event name with a
schema using schema_name.event_name syntax.

The DEFINER clause specifies the MySQL account to be used when checking access privileges
at event execution time. If a user value is given, it should be a MySQL account specified as
'user_name'@'host_name', CURRENT_USER, or CURRENT_USER(). The default DEFINER value
is the user who executes the CREATE EVENT statement. This is the same as specifying DEFINER =
CURRENT_USER explicitly.

If you specify the DEFINER clause, these rules determine the valid DEFINER user values:
* If you do not have the SET_USER_ID or SUPER privilege, the only permitted user value is your own
account, either specified literally or by using CURRENT_USER. You cannot set the definer to some other
account.
* If you have the SET_USER_ID or SUPER privilege, you can specify any syntactically valid account name.
If the account does not exist, a warning is generated.
* Although it is possible to create an event with a nonexistent DEFINER account, an error occurs at event
execution time if the account does not exist.

For more information about event security, see Section 23.6, “Access Control for Stored Programs and
Views”.

Within an event, the CURRENT_USER() function returns the account used to check privileges at event
execution time, which is the DEFINER user. For information about user auditing within events, see
Section 6.3.13, “SQL-Based MySQL Account Activity Auditing”.

IF NOT EXISTS has the same meaning for CREATE EVENT as for CREATE TABLE: If an event named
event_name already exists in the same schema, no action is taken, and no error results. (However, a
warning is generated in such cases.)

The ON SCHEDULE clause determines when, how often, and for how long the event_body defined for the
event repeats. This clause takes one of two forms:
* AT timestamp is used for a one-time event. It specifies that the event executes one time only
  at the date and time given by timestamp, which must include both the date and time, or must be
  an expression that resolves to a datetime value. You may use a value of either the DATETIME or
  TIMESTAMP type for this purpose. If the date is in the past, a warning occurs, as shown here:
  ```
  mysql> SELECT NOW();
  +---------------------+
  | NOW() |
  +---------------------+
  | 2006-02-10 23:59:01 |
  +---------------------+
  1 row in set (0.04 sec)
  
  mysql> CREATE EVENT e_totals
  -> ON SCHEDULE AT '2006-02-10 23:59:00'
  -> DO INSERT INTO test.totals VALUES (NOW());
  Query OK, 0 rows affected, 1 warning (0.00 sec)
  
  mysql> SHOW WARNINGS\G
  *************************** 1. row ***************************
  Level: Note
  Code: 1588
  Message: Event execution time is in the past and ON COMPLETION NOT
  PRESERVE is set. The event was dropped immediately after
  creation.
  ```
  
  CREATE EVENT statements which are themselves invalid—for whatever reason—fail with an error.
  
  You may use CURRENT_TIMESTAMP to specify the current date and time. In such a case, the event acts
  as soon as it is created.
  
  To create an event which occurs at some point in the future relative to the current date and time—such
  as that expressed by the phrase “three weeks from now”—you can use the optional clause + INTERVAL
  interval. The interval portion consists of two parts, a quantity and a unit of time, and follows the
  same syntax rules that govern intervals used in the DATE_ADD() function (see Section 12.7, “Date
  and Time Functions”. The units keywords are also the same, except that you cannot use any units
  involving microseconds when defining an event. With some interval types, complex time units may
  be used. For example, “two minutes and ten seconds” can be expressed as + INTERVAL '2:10'
  MINUTE_SECOND.
  
  You can also combine intervals. For example, AT CURRENT_TIMESTAMP + INTERVAL 3 WEEK +
  INTERVAL 2 DAY is equivalent to “three weeks and two days from now”. Each portion of such a clause
  must begin with + INTERVAL.

* To repeat actions at a regular interval, use an EVERY clause. The EVERY keyword is followed by an
  interval as described in the previous discussion of the AT keyword. (+ INTERVAL is not used with
  EVERY.) For example, EVERY 6 WEEK means “every six weeks”.
  
  Although + INTERVAL clauses are not permitted in an EVERY clause, you can use the same complex
  time units permitted in a + INTERVAL.
  
  An EVERY clause may contain an optional STARTS clause. STARTS is followed by a timestamp value
  that indicates when the action should begin repeating, and may also use + INTERVAL interval to
  specify an amount of time “from now”. For example, EVERY 3 MONTH STARTS CURRENT_TIMESTAMP
  + INTERVAL 1 WEEK means “every three months, beginning one week from now”. Similarly, you
  can express “every two weeks, beginning six hours and fifteen minutes from now” as EVERY 2 WEEK
  STARTS CURRENT_TIMESTAMP + INTERVAL '6:15' HOUR_MINUTE. Not specifying STARTS is
  the same as using STARTS CURRENT_TIMESTAMP—that is, the action specified for the event begins
  repeating immediately upon creation of the event.
  
  An EVERY clause may contain an optional ENDS clause. The ENDS keyword is followed by a timestamp
  value that tells MySQL when the event should stop repeating. You may also use + INTERVAL
  interval with ENDS; for instance, EVERY 12 HOUR STARTS CURRENT_TIMESTAMP + INTERVAL
  30 MINUTE ENDS CURRENT_TIMESTAMP + INTERVAL 4 WEEK is equivalent to “every twelve hours,
  beginning thirty minutes from now, and ending four weeks from now”. Not using ENDS means that the
  event continues executing indefinitely.

  ENDS supports the same syntax for complex time units as STARTS does.

  You may use STARTS, ENDS, both, or neither in an EVERY clause.

  If a repeating event does not terminate within its scheduling interval, the result may be multiple instances
  of the event executing simultaneously. If this is undesirable, you should institute a mechanism to prevent
  simultaneous instances. For example, you could use the GET_LOCK() function, or row or table locking.

The ON SCHEDULE clause may use expressions involving built-in MySQL functions and user variables to
obtain any of the timestamp or interval values which it contains. You may not use stored functions or
user-defined functions in such expressions, nor may you use any table references; however, you may use
SELECT FROM DUAL. This is true for both CREATE EVENT and ALTER EVENT statements. References
to stored functions, user-defined functions, and tables in such cases are specifically not permitted, and fail
with an error (see Bug #22830).

Times in the ON SCHEDULE clause are interpreted using the current session time_zone value. This
becomes the event time zone; that is, the time zone that is used for event scheduling and is in effect
within the event as it executes. These times are converted to UTC and stored along with the event time
zone in the mysql.event table. This enables event execution to proceed as defined regardless of any
subsequent changes to the server time zone or daylight saving time effects. For additional information
about representation of event times, see Section 23.4.4, “Event Metadata”. See also Section 13.7.6.18,
“SHOW EVENTS Syntax”, and Section 24.9, “The INFORMATION_SCHEMA EVENTS Table”.

Normally, once an event has expired, it is immediately dropped. You can override this behavior by
specifying ON COMPLETION PRESERVE. Using ON COMPLETION NOT PRESERVE merely makes the
default nonpersistent behavior explicit.

You can create an event but prevent it from being active using the DISABLE keyword. Alternatively, you
can use ENABLE to make explicit the default status, which is active. This is most useful in conjunction with
ALTER EVENT (see Section 13.1.3, “ALTER EVENT Syntax”).

A third value may also appear in place of ENABLE or DISABLE; DISABLE ON SLAVE is set for the status
of an event on a replication slave to indicate that the event was created on the master and replicated to the
slave, but is not executed on the slave. See Section 17.4.1.16, “Replication of Invoked Features”.

You may supply a comment for an event using a COMMENT clause. comment may be any string of up to 64
characters that you wish to use for describing the event. The comment text, being a string literal, must be
surrounded by quotation marks.

The DO clause specifies an action carried by the event, and consists of an SQL statement. Nearly any
valid MySQL statement that can be used in a stored routine can also be used as the action statement
for a scheduled event. (See Section C.1, “Restrictions on Stored Programs”.) For example, the following
event e_hourly deletes all rows from the sessions table once per hour, where this table is part of the
site_activity schema:
```
CREATE EVENT e_hourly
    ON SCHEDULE
        EVERY 1 HOUR
    COMMENT 'Clears out sessions table each hour.'
    DO
        DELETE FROM site_activity.sessions;
```

MySQL stores the sql_mode system variable setting in effect when an event is created or altered, and
always executes the event with this setting in force, regardless of the current server SQL mode when the
event begins executing.

A CREATE EVENT statement that contains an ALTER EVENT statement in its DO clause appears to
succeed; however, when the server attempts to execute the resulting scheduled event, the execution fails
with an error.

    Note
    Statements such as SELECT or SHOW that merely return a result set have no effect
    when used in an event; the output from these is not sent to the MySQL Monitor,
    nor is it stored anywhere. However, you can use statements such as SELECT ...
    INTO and INSERT INTO ... SELECT that store a result. (See the next example
    in this section for an instance of the latter.)

The schema to which an event belongs is the default schema for table references in the DO clause. Any
references to tables in other schemas must be qualified with the proper schema name.

As with stored routines, you can use compound-statement syntax in the DO clause by using the BEGIN and
END keywords, as shown here:
```
delimiter |
CREATE EVENT e_daily
ON SCHEDULE
EVERY 1 DAY
COMMENT 'Saves total number of sessions then clears the table each day'
DO
BEGIN
INSERT INTO site_activity.totals (time, total)
SELECT CURRENT_TIMESTAMP, COUNT(*)
FROM site_activity.sessions;
DELETE FROM site_activity.sessions;
END |
delimiter ;
```

This example uses the delimiter command to change the statement delimiter. See Section 23.1,
“Defining Stored Programs”.

More complex compound statements, such as those used in stored routines, are possible in an event. This
example uses local variables, an error handler, and a flow control construct:
```
delimiter |

CREATE EVENT e
    ON SCHEDULE
        EVERY 5 SECOND
    DO
        BEGIN
            DECLARE v INTEGER;
            DECLARE CONTINUE HANDLER FOR SQLEXCEPTION BEGIN END;

            SET v = 0;

            WHILE v < 5 DO
                INSERT INTO t1 VALUES (0);
                UPDATE t2 SET s1 = s1 + 1;
                SET v = v + 1;
            END WHILE;
    END |

delimiter ;
```

There is no way to pass parameters directly to or from events; however, it is possible to invoke a stored
routine with parameters within an event:
```
CREATE EVENT e_call_myproc
    ON SCHEDULE
      AT CURRENT_TIMESTAMP + INTERVAL 1 DAY
    DO CALL myproc(5, 27);
```
If an event's definer has the SYSTEM_VARIABLES_ADMIN or SUPER privilege, the event can read and
write global variables. As granting this privilege entails a potential for abuse, extreme care must be taken in
doing so.

Generally, any statements that are valid in stored routines may be used for action statements executed
by events. For more information about statements permissible within stored routines, see Section 23.2.1,
“Stored Routine Syntax”. You can create an event as part of a stored routine, but an event cannot be
created by another event.

