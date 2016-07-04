# Platform ELK Dashboards

This project contains pre-configured ELK dashboards for use with PNDA.

## Import & Export

Until Kibana is upgraded to allow export/import of dashboards, we can use [elasticsearch-dump](https://github.com/taskrabbit/elasticsearch-dump).

### Example

Export

	./bin/elasticdump --input=http://10.60.18.50:9200/.kibana --output=kibana.json

Import

	./bin/elasticdump --input=kibana.json --output=http://10.10.10.113:9200/.kibana 
