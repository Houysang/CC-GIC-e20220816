# Cloud Lab 5 Submission — AWS Database Lab

- **Student name:** GUOV Houysang
- **Student ID:** e20220816
- **Learner Lab user name:** voclabs/user5619307=GUOV_HOUYSANG
- **Date completed:** November 11, 2026

---

## 1. Security Group Rules

`aws-database-options-security-group` with its inbound rules (SSH port 22, Custom TCP ports 8000 and 5173) and its outbound rule.

![Security group rules](images/01-security-group.png)

## 2. EC2 Instance Running

`aws-database-options-lab-instance` in the **Running** state with `aws-database-options-security-group` attached.

![EC2 instance running](images/02-ec2-running.png)

## 3. S3 Bucket

Your `clab-imagestore-…` bucket with its public access settings and bucket policy visible.

![S3 bucket permissions](images/03-s3-bucket.png)

## 4. RDS DB Instance

`aws-dbo-sql` with the status **Available** and its endpoint visible.

![RDS DB instance](images/04-rds-sql.png)

## 5. Aurora DB Status

The `aws-dbo-aurora` cluster with the status **Available** and its writer and reader endpoints visible.

![Aurora DB status](images/05-aurora-cluster.png)

## 6. DynamoDB Table

The `posts` table with the status **Active** and its partition key `post_id` visible.

![DynamoDB table](images/06-dynamodb-table.png)

## 7. Application Configuration and Testing

The EC2 instance terminal after `npm start`, showing that the application started.

![Application started in the terminal](images/07-app-start.png)

The application running in your browser at `http://<Public_IP_of_EC2>:5173`, with the address bar visible.

![Application in the browser](images/08-app-browser.png)

## 8. Final Infrastructure

One collage of the resources you created: EC2 instance, security group, RDS and Aurora databases, DynamoDB table and S3 bucket.

![Final infrastructure](images/09-final-infrastructure.png)

## Signup and Sample Post (optional)

The signup or login page and a sample post. Delete this section if you did not take this screenshot.

![Signup and sample post](images/10-signup-post.png)

## Errors and troubleshooting (optional)

Screenshots of any error messages you met, and what you did about them.

---

## Questions

1. **Which instance answers the Aurora reader endpoint, and why does the application still work?**

   With no Aurora Replica, the reader endpoint points to an instance that can accept reader connections. In an Aurora cluster with only one instance, that instance is the primary/writer instance. The application works because its SQL connection to the reader endpoint is routed to the available primary instance, and the primary can execute reads while also handling writes.

2. **What does each post table store, and how does `/get_posts` join them?**

   The RDS `user_post` table stores the post ID and username—the relational metadata that links a post to its author. The DynamoDB `posts` table stores the post content and related post data keyed by `post_id`. `/get_posts` reads the post metadata from RDS and then uses each `id`/`post_id` as the lookup key in DynamoDB to retrieve the corresponding content, combining the username and content in the returned result.

3. **What happens if the same user publishes two posts in the same second, or on two different days at the same time?**

   The post ID is `username + time in %H:%M:%S` format, so two posts by the same user in the same second produce the same ID and overwrite or collide instead of representing two distinct posts. Two posts on different days at the same time also have the same time-based ID because the date is not included, so they collide for the same reason.

4. **Why does post content go into DynamoDB instead of a SQL table?**

   DynamoDB is used for the post content because this part of the data is large and well suited to key-based access: the `post_id` directly identifies a post and retrieves its content. RDS remains responsible for the small, relational metadata in `user_post`. This separation is intended to scale post content storage independently while keeping author/post relationships in SQL.

5. **Name one risk of storing plaintext passwords and allowing public S3 reads.**

   Storing passwords exactly as typed is unsafe because anyone who obtains the database will immediately know every user's password. The plaintext value is also vulnerable to reuse if users use the same password elsewhere. Allowing anyone to read S3 objects can expose uploaded data to the public and allow unauthorized copying or distribution of user images/content.

## Clean up

- [ ] EC2 instance terminated and `aws-database-options-security-group` deleted.
- [ ] RDS instance `aws-dbo-sql` and Aurora cluster `aws-dbo-aurora` deleted.
- [ ] S3 bucket emptied and deleted.
- [ ] DynamoDB table `posts` deleted.