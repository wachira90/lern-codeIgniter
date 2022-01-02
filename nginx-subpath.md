# sub path nginx

````
location /ci311 {
    root   html;
    index  index.php index.html index.htm;
    try_files /ci311/$uri /ci311/index.php;
}
````
