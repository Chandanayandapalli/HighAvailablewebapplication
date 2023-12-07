 #Script 1: RDS connection from ec2
★sudo su
★sudo apt update
★ sudo apt-get install mysql-server -y
★mysql --version
★mysqlmydatabase.cedvynohrzwi.us-east-1.rds.amazonaws.com -P 3306 -u admin -p
★Enter password: admin123


Script 2: RDS dummy data insertion
Upon using the commands given in the script 1 andentering the “mysql” database through the RDS endpointon an EC2 instance.

Use the following

SQL commands to insert dummy data:
#!/bin/bash

# MySQL connection details host
my-database.c7k13rii9ip.us-east-1.rds.amazonaws.com
username=admin
password=admin123
database=mydatabase

# SQL query to create table
create_table_query="CREATE TABLE details (id INT PRIMARY KEY, name VARCHAR (50);"

# SQL query to insert dummy data
insert_data_query="INSERT INTO details (id, name)VALUES (1, Rahul), (2, 'Budi'), (3, 'Chandana');"

# Connect to MySQL and execute queries
mysql -h $host -u $username -p$password -D $database-e "$create_table_query" &&/
mysql -h $host -u $username -p$password -D $database-e "$insert_data_query" echo "Table created with dummydata."

Script 3: Apache server fetches data from RDS

#!/bin/bash

# Database configuration
host="<RDS_endpoint"
username="admin"
password="admin123"
database="mydatabase"
table="your_table_name"

# Install required packages
sudo apt update
sudo apt install -y apache2 php libapache2-mod-php php-mysql

# Create a PHP script to fetch data from the database

sudo tee /var/www/html/index.php >/dev/null <<EOF
<?php
\$conn = new mysqli("$host", "$username", "$password",
"$database");
if (\$conn->connect_error) {
die("Connection failed: " . \$conn->connect_error);
}
\$sql = "SELECT * FROM $table";
\$result = \$conn->query(\$sql);
if (\$result->num_rows > 0) {
echo "<table><tr><th>ID</th><th>Name</th></tr>";
while(\$row = \$result->fetch_assoc()) {
echo "<tr><td>" . \$row["id"] . "</td><td>" .
\$row["name"] . "</td></tr>";
}
echo "</table>";
} else {
echo "0 results";
}
\$conn->close();
?>
EOF


# Configure Apache to serve PHP files
sudo sed -i "s/index.html/index.php/g"/etc/apache2/mods-enabled/dir.conf

# Restart Apache
sudo systemctl restart apache2

# Display public IP
echo "Web application is now accessible at: http://$(curl-s http://checkip.amazonaws.com)/"
