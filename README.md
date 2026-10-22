# Cloud-computing-lab-pgStep-by-Step Procedure
Step 1: Install the database server
sudo apt-get update
sudo apt-get install -y mariadb-server
Step 2: Start the database instance
sudo service mariadb start
sudo service mariadb status
The status output should show the server as running — the equivalent of the RDS status changing to Available.
Step 3: Create the database and user
Open the administrative client (the Codespace’s sudo access acts as the master account):
sudo mysql
Then run these SQL statements:
CREATE DATABASE lab;
CREATE USER 'main'@'localhost' IDENTIFIED BY 'lab-password';
CREATE USER 'main'@'127.0.0.1' IDENTIFIED BY 'lab-password';
GRANT ALL PRIVILEGES ON lab.* TO 'main'@'localhost';
GRANT ALL PRIVILEGES ON lab.* TO 'main'@'127.0.0.1';
FLUSH PRIVILEGES;
EXIT;
Step 4: Connect using the endpoint
mysql -h 127.0.0.1 -P 3306 -u main -p lab
Enter lab-password when prompted. The lab> prompt is the equivalent of the web application connecting to the RDS endpoint.
Step 5: Use the database (Address Book)
CREATE TABLE contacts (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50),
  phone VARCHAR(20),
  email VARCHAR(50)
);
 
INSERT INTO contacts (name, phone, email) VALUES ('Asha', '9876543210', 'asha@example.com');
INSERT INTO contacts (name, phone, email) VALUES ('Ravi', '9123456780', 'ravi@example.com');
SELECT * FROM contacts;
 
UPDATE contacts SET phone = '9000000001' WHERE name = 'Ravi';
DELETE FROM contacts WHERE name = 'Asha';
SELECT * FROM contacts;
EXIT;
Step 6: Test access control
Try connecting with a wrong password:
mysql -h 127.0.0.1 -u main -pwrong-password lab
The server refuses the connection with an "Access denied" error, showing that only holders of valid credentials can reach the data.
Step 7: Back up and restore (RDS snapshot equivalent)
mysqldump -h 127.0.0.1 -u main -p lab > lab-backup.sql
 
mysql -h 127.0.0.1 -u main -p lab -e "DROP TABLE contacts;"
mysql -h 127.0.0.1 -u main -p lab < lab-backup.sql
mysql -h 127.0.0.1 -u main -p lab -e "SELECT * FROM contacts;"
Note: lab-password comes from the lab manual and is for demonstration only. Keep port 3306 private in the Ports panel; do not make it public.
Full Command Reference
sudo apt-get update && sudo apt-get install -y mariadb-server
sudo service mariadb start
sudo mysql   (CREATE DATABASE lab; CREATE USER main...; GRANT ...)
mysql -h 127.0.0.1 -P 3306 -u main -p lab
  (CREATE TABLE, INSERT, SELECT, UPDATE, DELETE, EXIT)
mysqldump -h 127.0.0.1 -u main -p lab > lab-backup.sql
mysql -h 127.0.0.1 -u main -p lab < lab-backup.sql
Result
A MySQL-compatible database instance was created in a GitHub Codespace with a database named lab and a user named main. A connection was made through the host and port endpoint, and data was stored, retrieved, updated and deleted in an address-book table. A backup was taken and restored, reproducing the outcome of the RDS experiment without an AWS account.
Output after the first SELECT in Step 5 (ids and values as inserted):
+----+-------+------------+-------------------+
| id | name  | phone      | email             |
+----+-------+------------+-------------------+
|  1 | Asha  | 9876543210 | asha@example.com  |
|  2 | Ravi  | 9123456780 | ravi@example.com  |
+----+-------+------------+-------------------+
