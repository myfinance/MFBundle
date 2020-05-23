# mfbundle

mkdir /opt/mf/prod_db/  (or mount to other directory)
helm repo update
helm upgrade -i --cleanup-on-fail mfbundle local/mfbundle --set repository=192.168.100.73:31003/repository/mydockerrepo/holgerfischer/myfinance- --set stage=prod --devel