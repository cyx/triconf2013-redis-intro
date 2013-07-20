# 0. We need a monotonic ID server

    INCR User:id

# 1. Create a user, add it, etc

    HMSET User:1 status active email foo@example.com

# 2. Add to indices

    SADD User:all 1
    SADD User:status:active 1
    HSET User:email foo@example.com 1

# 3. More users

    HMSET User:2 status active email bar@example.com
    HMSET User:3 status pending email baz@example.com
    HMSET User:4 status canceled email qux@example.com

# 4. Index those users

    SADD User:status:active 2
    SADD User:status:pending 3
    SADD User:status:canceled 4

    HSET User:email bar@example.com 2
    HSET User:email baz@example.com 3
    HSET User:email qux@example.com 4

# 5. Find all active users

    SMEMBERS User:status:active

# 6. Find all canceled and pending users

    SUNION User:status:pending User:status:canceled

# 7. Get a user by their email

    HGET User:email bar@example.com
    HGETALL User:2

# 8. Queues

    # in your app
    LPUSH welcome_email 1

    # in the worker
    BRPOP welcome_email 10

# 9. Blocking queues (what we call channels)

    # in your app
    LPUSH provision 1
    BRPOP provision:1 600

    # in the worker
    BRPOP provision 10

    # do something with the ID you get, then
    LPUSH provision:1

# 10. Transactions

    Hallway track
