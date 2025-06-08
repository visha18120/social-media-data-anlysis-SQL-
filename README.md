# social-media-data-anlysis-SQL-
To analyze data from social media platforms (e.g., Twitter, Instagram, Facebook, YouTube) to extract insights on user engagement, post performance, trends, sentiment, and content strategy.
social media analysis

-- Designed and implemented a MySQL-based social media analysis project, 
-- leveraging relational database capabilities to efficiently 
-- store, retrieve, and analyze extensive social media data. 
-- Developed features including user profiles, post storage, s
-- entiment analysis, and trend tracking to provide 
-- aluable insights into user behavior and trending topics. 
-- Demonstrated proficiency in database management for 
-- effective data organization and retrieval, showcasing 
-- a keen understanding of scalable and well-structured MySQL systems


-- task 1 data collection 
USE social_media;
SHOW tables;

-- total 13 tables are present in database

-- bookmarks
-- comment_likes
-- comments
-- follows
-- hashtag_follow
-- hashtags
-- login
-- photos
-- post
-- post_likes
-- post_tags
-- users
-- videos

-- relationship(data modeling)

-- task 2 -- CHECK AND EXPLORER DIAGRAM 
-- EXPLORE RELATIONSHIP
-- relationship (data modeling)

-- task 3 DATA cleaning 
-- no date cleaning is required

-- task 4 tatal users

SELECT count(*) FROM users;

-- total 50 users data available

-- task 5 total post
SELECT count(post_id) from post;

-- total 100 post done by users in our database 

-- task 6 user location 
SELECT location from post;

-- location is not mentioned in a proper way

-- task 7 average post per users
SELECT count(distinct user_id) from post;
SELECT COUNT(post_id)/(SELECT count(distinct user_id) from post) avg_post FROM post;

-- on an average 2 post are done by each user

-- task 8 find user who have not posted yet
 SELECT*FROM users WHERE user_id NOT IN(SELECT distinct user_id from post);


-- five users out of 50 have posted not yet

-- task 9 display users who have posted 5 or more than 5 post
SELECT u1.username,u1.user_id ,count(p1.post_id) total FROM users u1 
INNER JOIN post p1 
on u1.user_id=p1.user_id 
group by u1.username
HAVING total>=5;

-- 4 users have posted 5 or more than 5 post

-- task 10 display users with 0 comment
-- users 
-- comments

-- subquery
SELECT*FROM users WHERE user_id NOT IN(SELECT distinct user_id from comments);

SELECT u1.username,u1.user_id ,count(p1.post_id) total FROM users u1 
LEFT JOIN post p1 
on u1.user_id=p1.user_id 
group by u1.username
HAVING total>=5;

SELECT*FROM users U1 left join comments c1  
on u1.user_id=c1.user_id
WHERE c1.comment_id IS NULL; 

-- just 1 user raj gupta is not comment yet

-- task 11 people who have been using the # platform for the longest time. limit 5

SELECT * FROM users 
order by created_at ASC 
LIMIT 5;

-- 5 users using the platform for the longest time.


-- task 12 Find the longest caption in post
SELECT user_id,post_id,caption,length(caption) total from post 
order by total DESC
LIMIT 1;

-- task 13 FIND post which has beauty or beautifull
SELECT post_id,caption FROM post WHERE caption regexp'beauty|beautiful';
-- | - means OR
-- there are 3 post which contain beauty or beautifull in their caption 

-- task 14 #display the comment len>=12 chr

SELECT count(*) FROM comments where length(comment_text)>=12;

-- task 15 FIND users who has followers >=40

SELECT followee_id,count(follower_id) total FROM follows
group by followee_id
HAVING total>=40;

#14 users have followers>=40

-- task 14 display most liked post
 SELECT p1.post_id,p1.caption,count(*)Total FROM post p1
 INNER JOIN post_likes p2
 ON p1.post_id=p2.post_id
 GROUP BY p1.post_id
 ORDER BY TOTAL DESC;
 
 
SELECT 
    p1.post_id,
    p2.caption,
    p3.username,
    COUNT(*) AS total
FROM 
    post_likes p1
INNER JOIN 
    post p2 ON p1.post_id = p2.post_id
INNER JOIN
    users p3 ON p2.user_id = p3.user_id
GROUP BY 
    p1.post_id, p2.caption, p3.username
ORDER BY total DESC;

-- post id 42 is most liked post

-- task 17 FIND most commonly used hashtags

SELECT h1.hashtag_name,count(hashtag_name)Total FROM hashtags h1
 INNER JOIN Post_tags p1
 ON p1.post_id=h1.hashtag_id
 GROUP BY h1.hashtag_name
 ORDER BY TOTAL DESC;
 
 #beautiful is most commonly used hashtags
 
 -- task 18 find most followed hashtags
 SELECT*FROM hashtag_follow;

SELECT 
    p1.post_id,
    p2.caption,
    p3.username,
    COUNT(*) AS total
FROM 
    post_likes p1
INNER JOIN 
    post p2 ON p1.post_id = p2.post_id
INNER JOIN
    users p3 ON p2.user_id = p3.user_id
GROUP BY 
    p1.post_id, p2.caption, p3.username
ORDER BY total DESC;

-- festival is most trending/followed hashtag in data

 # task-19 display users who dont follow anybody
 # task-20   most inactive user ..(post concept) 
 # task-21 most inactive user (post likes)
 # task-22 CHECK FOR BOT,
-- user who commented on every post
# task-23  CHECK FOR BOT,
# users who like every post

-- task 19
 USE social_media;
SELECT u1.user_id, u1.username
FROM users u1
LEFT JOIN follows f1 ON u1.user_id = f1.follower_id
WHERE f1.follower_id IS NULL;

SELECT*FROM users where user_id NOT IN(SELECT follower_id FROM Follows);


-- 0 users who not follow anybody 

-- task 20
SELECT u1.user_id, u1.username, COUNT(p1.post_id) AS post_count
FROM users u1
LEFT JOIN post p1 ON u1.user_id = p1.user_id
GROUP BY u1.user_id
ORDER BY post_count ASC
LIMIT 1;

SELECT*FROM users WHERE user_id NOT IN(SELECT distinct user_id from post);

# joshua is the most inactive user 

-- task 21

SELECT*FROM users WHERE user_id NOT IN(SELECT distinct user_id from post_likes);

-- 0 most inactive user on post likes 

-- task 22
SELECT u1.user_id, u1.username
FROM users u1
INNER JOIN comments c1 ON u1.user_id = c1.user_id
GROUP BY u1.user_id
HAVING COUNT(c1.comment_id) = (SELECT COUNT(post_id) FROM post);

SELECT*FROM users WHERE user_id NOT IN(SELECT distinct user_id from comments);

#raj gupta is user who commented on every post

-- task 23
-- check for bot,
-- user who commented on every post

SELECT u1.user_id, u1.username, COUNT(c1.comment_id)total
FROM users u1
INNER JOIN comments c1 ON u1.user_id = c1.user_id
GROUP BY u1.user_id
HAVING total = (SELECT COUNT(post_id) FROM post)
ORDER BY total DESC;

-- there is no bot who commented on every post

-- task 24

SELECT u1.username,count(*) total
FROM 
users u1 INNER JOIN post_likes p1 
ON u1.user_id=p1.user_id
GROUP BY u1.username
HAVING total=(SELECT count(*) FROM post)
ORDER BY total DESC;

# 0 bot who like every post

-- user defined function
-- stored procedure
-- trigger
-- cursor
-- zomato data analysis





















