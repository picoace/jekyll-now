---
layout: post
title: JNDI lookup set for local binding
---

# JNDI lookup set for local binding

## 1. ejb-jar.xml과 web.xml 에 resource-ref 사용시 lookup 안되는 문제.

실제 JEUS에 등록하는 resource와의 매핑을 하기 위해서는 ejb-jar.xml 과 web.xml 에 resource-ref를 설정하고

jeus-ejb-dd.xml 과 jeus-web-dd.xml 에 res-ref 설정을 통해 매핑을 시켜줘야 합니다.

아래 구체적인 설정 예를 바탕으로 설명을 진행하도록 합니다.

### 1) JEUSMain.xml 에 설정한 DataSource 설정.

```
<database>
	<vendor>oracle</vendor>
	<export-name>SpringDS</export-name>
	<data-source-class-name>oracle.jdbc.xa.client.OracleXADataSource</data-source-class-name>
	<data-source-type>XADataSource</data-source-type>
	<database-name>ORA92</database-name>
	<data-source-name>oracle.jdbc.xa.client.OracleXADataSource</data-source-name>
	............
</database>
```

### 2)  web.xml 또는 ejb-jar.xml

```
<resource-ref>
	<res-ref-name>jdbc/DataSource</res-ref-name>
	<res-type>javax.sql.DataSource</res-type>
	<res-auth>Container</res-auth>
	<res-sharing-scope>Shareable</res-sharing-scope>
</resource-ref>
```

### 3) jeus-web-dd.xml 또는 jeus-ejb-jar.xml

```
<res-ref>
	<jndi-info>
		 <ref-name>jdbc/DataSource</ref-name>
		 <export-name>SpringDS</export-name>
	</jndi-info>
</res-ref>
```
