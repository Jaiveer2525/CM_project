# Start the Apache and MariaDB, I havent enabled them to save on memory
```
sudo systemctl start httpd
sudo systemctl start mariadb
```


(ofc i used chat gpt)

Step 3: Upload Video Files

Create a Directory for Video Files:

bash
```
sudo mkdir /var/www/html/videos
sudo chown -R www-data:www-data /var/www/html/videos
```
Upload Video Files: Place your video files in /var/www/html/videos.

Insert Video Metadata into the Database:

sql
```
    INSERT INTO videos (title, filepath) VALUES ('Sample Video', 'videos/sample.mp4');
```
Step 4: Configure Apache to Serve Video Files

Enable Necessary Modules (if not already enabled):

bash
```
sudo a2enmod mime
sudo a2enmod headers
```
Set Up Proper MIME Types: Ensure that your apache2.conf or .htaccess file has the correct MIME types for video streaming:

apache
```
AddType video/mp4 .mp4
AddType video/ogg .ogv
AddType video/webm .webm
```
Configure Directory Settings: In your Apache configuration file (e.g., /etc/apache2/sites-available/000-default.conf), you can add:

apache
```
    <Directory /var/www/html/videos>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
```
Step 5: Create a Simple PHP Interface

Create a PHP Script to Fetch Video Data: Create a file, e.g., index.php, in /var/www/html:

php
```
    <?php
    $conn = new mysqli('localhost', 'stream_user', 'your_password', 'video_streaming');

    if ($conn->connect_error) {
        die("Connection failed: " . $conn->connect_error);
    }

    $result = $conn->query("SELECT * FROM videos");

    while ($row = $result->fetch_assoc()) {
        echo "<h3>" . $row['title'] . "</h3>";
        echo "<video width='640' height='480' controls>
                <source src='" . $row['filepath'] . "' type='video/mp4'>
                Your browser does not support the video tag.
              </video><br>";
    }

    $conn->close();
    ?>
```
Step 6: Test Your Setup

Restart Apache:

bash
```
    sudo systemctl restart apache2
```
Open your web browser and go to http://your_server_ip/index.php to see your video list.

