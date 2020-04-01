  
  将容器提交一个新的的镜像

  docker commit -m "add support libevet for php5.6 and swoft for php7.2 " -a "henry init" 2bcea8cf9204 goodawei/henry-image-centos-php

  查看镜像
  docker images

  打标签
  docker tag goodawei/henry-image-centos-php goodawei/henry-image-centos-php:v2
  
  推送
  docker login

  sudo docker push goodawei/henry-image-centos-php:v2