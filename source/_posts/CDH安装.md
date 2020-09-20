---
title: CDS安装
tags: CDH，spark
renderNumberedHeading: true
grammar_cjkRuby: true
---

配置parcel文件：
```
	mkdir spark_parcel && cd spark_parcel
	wget http://archive.cloudera.com/spark2/parcels/2.4.0.cloudera2/SPARK2-2.4.0.cloudera2-1.cdh5.13.3.p0.1041012-el7.parcel
	wget http://archive.cloudera.com/spark2/parcels/2.4.0.cloudera2/SPARK2-2.4.0.cloudera2-1.cdh5.13.3.p0.1041012-el7.parcel.sha1
	mv SPARK2-2.4.0.cloudera2-1.cdh5.13.3.p0.1041012-el7.parcel.sha1 SPARK2-2.4.0.cloudera2-1.cdh5.13.3.p0.1041012-el7.parcel.sha
	wget http://archive.cloudera.com/spark2/parcels/2.4.0.cloudera2/manifest.json
	cd ../
	mv spark_parcel /var/www/html/
```

CDH WebUI
```
	
```