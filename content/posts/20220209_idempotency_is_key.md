---
title: "Idempotency is Key"
date: 2022-02-09T00:58:48-08:00
draft: true
---

**Idempotence**[^1] is a property of software that regardless of the number of times
a function or process is run, the final results (output + side effects) are 
equivalent to running it only once.

If you're performing an action at a given time, you could just schedule a cron
job and perform it at the desired time. However, the idempotent way of doing 
this would also include checks against _if you've already done an action 
before._ 

In some software, this might be overkill. However, in many cases, this property
of software is **critical** to your success, whether it be software that 
performs financial transactions[^2] or sends notifications to users.

## Why do I need to worry about this?

There are many reasons software might require this property:

- your server went down at the moment you may or may not have performed an 
  action
- you may need to update the code and be able to execute it again, but without
  side effects due to re-running the code
- your data pipeline received data late, and needs to be re-run for the late 
  time-range

In any case, it only takes a little thought to figure out that you've run into
any case like these.

With cloud computing as common practice, failure is almost an expectation of 
your hardware[^3]. How you handle error scenarios is all based on _risk_. You'll 
want to apply idempotency to software where you _can't_ take the risk that your 
code will generate additional side effects if re-run.

## Example 1: Notifications

Email marketing is a common solution to reaching out to the masses about your
product, blog, or feature update. A simple solution to do this is might be as 
follows:

1. Gather a list of recipients that need to be emailed
3. Iterate over the list and email them

However, there could be issues with running this in production. This process is 
not idempotent, and could be a larger issue if it fails in the middle or is 
processed multiple times.

If we want to enable idempotency, you could do the following (changes in bold):

1. Gather a list of receipients **that haven't received this email**
2. Iterate over the list and email them, **marking them off as you go**

Now, if you run the process again, it will only notify those that haven't 
already received the email.

## Example 2: Data Processing

Data is another common example that is ever-present in software nowadays.
Something as simple as a user login count could be simply implemented like this:

```sql
UPDATE users SET login_count = login_count + 1 WHERE id = <id>;
```

As we can see, there's two main issues with this statement:

1. **It's not idempotent!** Running it multiple times (software bug, login spam) 
   will result in a invalid count that shows signs of the number of times it was 
   run.
2. **You can't re-trace your steps!** You only have the final count in the DB; 
   unless you load a DB backup and re-run your API logs against it, you can't 
   reconcile the login count of a user if there was an issue.

Let's try this again, but include a path for idempotency. If we keep a log of 
logins that are performed by users, we could utilize a table like this:

```sql
INSERT INTO user_logins
  (id, user_id, timestamp)
VALUES
  (<id>, <user_id>, now())
ON CONFLICT(id) DO NOTHING;
```

| ID | User ID   | Timestamp           |
|:---|:----------|:--------------------|
| 1  | 1         | 2022-02-09 00:02:23 |
| 2  | 1         | 2022-02-09 00:02:45 |
| 3  | 2         | 2022-02-09 00:02:46 |
| 4  | 2         | 2022-02-09 00:03:01 |
| 5  | 2         | 2022-02-09 00:04:26 |

Since we assign an ID to each login request, we can dedup the results by the ID 
column. You can then perform a daily aggregation, like this:
 
```sql
INSERT INTO daily_user_counts
SELECT timestamp::date, user_id, COUNT(1) as count
FROM user_logins
WHERE timestamp::date = <date>
ON CONFLICT (user_id, date) UPDATE
  SET count = excluded.count;
```

If this is ran one time or twenty times for the date `'2022-02-09'`, you'll
always get the following result:
 
| User ID | Count | Date       |
|:--------|:------|:-----------|
| 1       | 2     | 2022-02-09 |
| 2       | 3     | 2022-02-09 |

This way, the _entire data process_[^4] is idempotent -- the final results don't 
show any sign of the number of times it was run, and can safely be performed
multiple times and deliver the same, accurate results.

## Idempotency is Key

Overall, Idempotency should be an all-time "Great Software Principle You Should 
Know"<sup>TM</sup>. It helps you build reliable software processes that can 
handle more of the unknowns that production systems can throw at you. When you 
have a critical process, idempotency can be one of the best tools at your 
disposal to create a more resilient software solution.

[^1]: [Wikipedia, Idempotence](https://en.wikipedia.org/wiki/Idempotence)

[^2]: A great example is Stripe's 
      [Idempotency Key](https://stripe.com/blog/idempotency)

[^3]: _When you think in terms of the blast radius, the question of failure is 
      no longer a question of if but a matter of when._ 
      [AWS Fundamentals](https://aws.amazon.com/getting-started/fundamentals-core-concepts/)

[^4]: The process is idempotent, but the transform is _reproducable_. 
      [Let’s Get ‘Idempotence’ Right](https://medium.com/ssense-tech/lets-get-idempotence-right-59f227178bb8)