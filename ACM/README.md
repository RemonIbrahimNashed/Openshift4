# get current alretmanager.yaml
oc -n open-cluster-management-observability get secret alertmanager-config --template='{{ index .data "alertmanager.yaml" }}' |base64 -d > alertmanager.yaml
# edit alretmanger.yml
vim alertmanager.yaml
# apply new config
oc project open-cluster-management-observability
oc -n open-cluster-management-observability create secret generic alertmanager-config --from-file=alertmanager.yaml --dry-run -o=yaml |  oc -n open-cluster-management-observability replace secret --filename=-

# use amtool from alretmanger pod 
amtool alert --alertmanager.url=http://localhost:9093
amtool silence add alertname=KubeCPUOvercommit --comment=acked
amtool --alertmanager.url=http://localhost:9093 alert query | grep -i clock
