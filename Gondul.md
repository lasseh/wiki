# Install guide for Gondul

```


apt-get install apt-transport-https ca-certificates gnupg2

add docker repo
/etc/apt/sources.list.d/docker.list
deb https://apt.dockerproject.org/repo debian-jessie main
get key from docker repo
apt-get update
apt-get install docker-engine


install ansible 2.1.x

/etc/apt/sources.list.d/ansible.list
deb http://httpredir.debian.org/debian jessie-backports main contrib non-free
deb-src http://httpredir.debian.org/debian jessie-backports main contrib non-free

apt-get -t jessie-backports install ansible


wget https://bootstrap.pypa.io/get-pip.py
python get-pip.py
pip install 'docker-py==1.9.0'



Gondul:
get mibs:
./extras/tools/get_mibs.sh


``` 











