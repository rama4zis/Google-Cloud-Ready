# 1 : Create a Compute Engine instance, add necessary firewall rules

Go to Compute Engine > VM instances > Create instance

```
   Name : <Given in the lab>
   Zone : <Given in the lab>
   Series : N1
   Boot Disk : Default

   Tick : Allow HTTP Traffic
          Allow HTTPS Traffic
```

Click Create

# 2 : Configure Apache2 Web Server in your instance

-   SSH to 'apache' instance and run
    
    ```
    sudo apt-get update
    sudo apt-get install apache2 
    sudo service apache2 restart
    service --status-all    
    ```

# 3 : Test your server

Click external IP address of your instance to see the default page of Apache2 Web Server