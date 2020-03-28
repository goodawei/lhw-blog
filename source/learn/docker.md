  
  docker commit -m "add support libevet for php5.6 and swoft for php7.2 " -a "henry init" 2bcea8cf9204 goodawei/henry-image-centos-php

  docker images

  docker tag goodawei/henry-image-centos-php goodawei/henry-image-centos-php:v2

  docker login

  sudo docker push goodawei/henry-image-centos-php:v2