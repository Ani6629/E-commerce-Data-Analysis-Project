# Instagram Data Model & Analytics Project

## Project Overview
This project involves designing a data model for an Instagram-like application using PostgreSQL. It includes creating various tables to represent users, posts, comments, likes, and followers, along with SQL statements for creating, inserting, and updating data. The project also demonstrates various analytical queries to extract meaningful insights from the data.

![Data Model](https://github.com/username/repository/blob/main/images/data_model.png)


## Table of Contents
- [Project Overview](#project-overview)
- [Data Model Design](#data-model-design)
- [Database Setup](#database-setup)
- [Data Insertion](#data-insertion)
- [Analytical Queries](#analytical-queries)
- [Technologies Used](#technologies-used)
- [How to Run](#how-to-run)
- [Conclusion](#conclusion)
- [Contributing](#contributing)
- [License](#license)

## Data Model Design
The data model consists of the following tables:
1. **Users**
   - `user_id`: Primary Key
   - `name`: User's name
   - `email`: User's email (unique)
   - `phone_number`: User's phone number (unique)
   
2. **Posts**
   - `post_id`: Primary Key
   - `user_id`: Foreign Key referencing `Users(user_id)`
   - `caption`: Post caption
   - `image_url`: URL of the post image
   - `created_at`: Timestamp of post creation

3. **Comments**
   - `comment_id`: Primary Key
   - `post_id`: Foreign Key referencing `Posts(post_id)`
   - `user_id`: Foreign Key referencing `Users(user_id)`
   - `comment_text`: Comment text
   - `created_at`: Timestamp of comment creation

4. **Likes**
   - `like_id`: Primary Key
   - `post_id`: Foreign Key referencing `Posts(post_id)`
   - `user_id`: Foreign Key referencing `Users(user_id)`
   - `created_at`: Timestamp of like creation

5. **Followers**
   - `follower_id`: Primary Key
   - `user_id`: Foreign Key referencing `Users(user_id)`
   - `follower_user_id`: Foreign Key referencing `Users(user_id)`
   - `created_at`: Timestamp of follow action

## Database Setup
To set up the database, use the following SQL commands to create the tables:

```sql
CREATE TABLE Users (
    user_id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone_number VARCHAR(20) UNIQUE
);

CREATE TABLE Posts (
    post_id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL,
    caption TEXT,
    image_url VARCHAR(200),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

CREATE TABLE Comments (
    comment_id SERIAL PRIMARY KEY,
    post_id INTEGER NOT NULL,
    user_id INTEGER NOT NULL,
    comment_text TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES Posts(post_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

CREATE TABLE Likes (
    like_id SERIAL PRIMARY KEY,
    post_id INTEGER NOT NULL,
    user_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES Posts(post_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

CREATE TABLE Followers (
    follower_id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL,
    follower_user_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (follower_user_id) REFERENCES Users(user_id)
);
```

## Data Insertion
Example SQL statements to insert data into the tables:

```sql
-- Inserting into Users table
INSERT INTO Users (name, email, phone_number)
VALUES
    ('John Smith', 'johnsmith@gmail.com', '1234567890'),
    ('Jane Doe', 'janedoe@yahoo.com', '0987654321'),
    ('Bob Johnson', 'bjohnson@gmail.com', '1112223333'),
    ('Alice Brown', 'abrown@yahoo.com', NULL),
    ('Mike Davis', 'mdavis@gmail.com', '5556667777');

-- Inserting into Posts table
INSERT INTO Posts (user_id, caption, image_url)
VALUES
    (1, 'Beautiful sunset', 'https://www.example.com/sunset.jpg'),
    (2, 'My new puppy', 'https://www.example.com/puppy.jpg'),
    (3, 'Delicious pizza', 'https://www.example.com/pizza.jpg'),
    (4, 'Throwback to my vacation', 'https://www.example.com/vacation.jpg'),
    (5, 'Amazing concert', 'https://www.example.com/concert.jpg');

-- Inserting into Comments table
INSERT INTO Comments (post_id, user_id, comment_text)
VALUES
    (1, 2, 'Wow! Stunning.'),
    (1, 3, 'Beautiful colors.'),
    (2, 1, 'What a cutie!'),
    (2, 4, 'Aww, I want one.'),
    (3, 5, 'Yum!'),
    (4, 1, 'Looks like an awesome trip.'),
    (5, 3, 'Wish I was there!');

-- Inserting into Likes table
INSERT INTO Likes (post_id, user_id)
VALUES
    (1, 2),
    (1, 4),
    (2, 1),
    (2, 3),
    (3, 5),
    (4, 1),
    (4, 2),
    (4, 3),
    (5, 4),
    (5, 5);

-- Inserting into Followers table
INSERT INTO Followers (user_id, follower_user_id)
VALUES
    (1, 2),
    (2, 1),
    (1, 3),
    (3, 1),
    (1, 4),
    (4, 1),
    (1, 5),
    (5, 1);
```

## Analytical Queries
Some examples of analytical queries to extract insights:

- **Selecting all posts by a specific user**:
  ```sql
  SELECT * FROM Posts WHERE user_id = 1;
  ```
- **Ordering posts by creation date**:
  ```sql
  SELECT * FROM Posts ORDER BY created_at DESC;
  ```
- **Counting likes for each post and filtering by posts with more than 2 likes**:
  ```sql
  SELECT Posts.post_id, COUNT(Likes.like_id) AS num_likes
  FROM Posts
  LEFT JOIN Likes ON Posts.post_id = Likes.post_id
  GROUP BY Posts.post_id
  HAVING COUNT(Likes.like_id) > 2;
  ```
- **Finding users who commented on a specific post**:
  ```sql
  SELECT name FROM Users WHERE user_id IN (
      SELECT user_id FROM Comments WHERE post_id = 1
  );
  ```
- **Ranking posts based on the number of likes**:
  ```sql
  SELECT post_id, num_likes, RANK() OVER (ORDER BY num_likes DESC) AS rank
  FROM (
      SELECT Posts.post_id, COUNT(Likes.like_id) AS num_likes
      FROM Posts
      LEFT JOIN Likes ON Posts.post_id = Likes.post_id
      GROUP BY Posts.post_id
  ) AS likes_by_post;
  ```

## Technologies Used
- **Programming Languages**: Python
- **Libraries**: pandas, psycopg2, SQLAlchemy
- **Database**: PostgreSQL
- **Tools**: Jupyter Notebook

## How to Run
1. Clone the repository to your local machine.
2. Set up a PostgreSQL database and create the necessary tables using the provided SQL commands.
3. Load the sample data into the tables.
4. Run the analytical queries to extract insights.

## Conclusion
This project demonstrates the process of designing a data model, setting up a database, and performing data analysis using SQL. It highlights the ability to handle data from multiple sources, transform and load data into a relational database, and perform complex queries to extract insights.

## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.

## License
This project is licensed under the MIT License.
