from --platform=amd64 ubuntu/apache2:2.4-26.04_edge as apache2
onbuild run apt udpate && apt upgrade
run apt install less nano 
run mkdir /var/www/cms
volume /opt/cms-app /var/www/cms 
expose 3000:3000
