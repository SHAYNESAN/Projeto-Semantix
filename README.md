# Projeto-Semantix
Projeto final do Curso Engenharia de Dados - Big Data

Subir o docker-compose up -d.

docker exec -it hive-server bash.

beeline -u jdbc:hive2://localhost:10000

criar database, criar tabela com os parametros

create table covid1(regiao string, estado string, coduf integer, codmun integer,codregiaosaude integer,nomeregiaosaude string, data timestamp,semanaEpi integer,
populacaoTCU2019 integer, casosacumulado decimal(10,0),casosnovos integer, obitosacumulados integer, obitosNnvos integer, recuperadosnovos integer,
emacompanhamentonovos integer,interior_metropolitana integer) PARTITIONED BY (municipio string)row format delimited fields terminated by ';' lines terminated by '\n'
stored as textfile tblproperties("skip.header.line.count"="1") ;

2 parte executada no Jupyter Notebook

from pyspark.sql.functions import *

from pyspark.sql.types import *

from pyspark.sql import functions as f

from pyspark.sql import SQLContext

from pyspark.sql import SparkSession

df = spark.read.option("header",True)\
    .option("inferSchema",True)\
    .option("mode","DROPMALFORMED")\
    .option("delimiter", ";")\
    .csv("/user/sandro/projeto/HIST_PAINEL_COVIDBR_2021_Parte2_06jul2021.csv")
    
#Salvando o Df como tabela hive particionada por munincipo

df.write.mode("overwrite").partitionBy("municipio").saveAsTable("covid19")
casos_recuperados = df.select("regiao","Recuperadosnovos","emAcompanhamentoNovos").filter("regiao is Not Null")\
.filter("Recuperadosnovos is Not Null").filter("emAcompanhamentoNovos is Not Null").filter("data == '2021-07-06 00:00:00'")\
.groupBy("regiao").agg(max("Recuperadosnovos").alias("Casos_recuperados"),\
                       max("emAcompanhamentoNovos").alias("Em_acompanhamento"))
           egiao|Casos_recuperados|Em_acompanhamento|
           


casos_confirmados.write.mode("overwrite").saveAsTable("Casos_confirmados")


casos_obitos = df.select("regiao","obitosAcumulado","obitosNovos","populacaoTCU2019","casosAcumulado")\
.filter("regiao == 'Brasil'").filter("data == '2021-07-06 00:00:00'")\
.filter("obitosAcumulado is Not Null").filter("obitosNovos is Not Null").filter("populacaoTCU2019 is Not Null")\
.groupBy("regiao").agg(max("obitosAcumulado").alias("Obitos confirmados"),\
                       max("obitosNovos").alias("Casos novos"),\
                       format_number(max((df["obitosAcumulado"]/df["casosAcumulado"])*100),1).cast('float')\
                       .alias("letalidade"),\
                       format_number(max((df["obitosAcumulado"]/df["populacaoTCU2019"])*100000),1).cast('float')\
                       .alias("mortalidade")).orderBy(col("Obitos confirmados").desc())
casos_obitos.show(10)
casos_obitos.printSchema()




 
 #Enviar a tabela para o Kafka
 
 casos_obitos.selectExpr("to_json(struct(*)) AS value")\
.write.format("kafka")\
.option("kafka.bootstrap.servers", "kafka:9092")\
.option("topic", "casos_obitos")\
.save()


#Panorama geral do Covid19

panorama_geral = df.select("regiao","estado","casosAcumulado","obitosAcumulado","populacaoTCU2019","data")\
.filter("data == '2021-07-06 00:00:00'")\
.withColumn("Regiao",col("regiao"))\
.withColumn("Estado",col("estado"))\
.withColumn("Casos",col("casosAcumulado"))\
.withColumn("Obitos",col("obitosAcumulado"))\
.withColumn("Incidencia_100mil",f.round((df["casosAcumulado"]/df["populacaoTCU2019"])*100000,1).cast('float'))\
.withColumn("Mortalidade_100mil",format_number((df["obitosAcumulado"]/df["populacaoTCU2019"])*100000,1).cast('float'))\
.withColumn("Atualizacao",col("data"))\
.drop(df.casosAcumulado).drop(df.data)\
.drop(df.obitosAcumulado)\
.drop(df.populacaoTCU2019)\
.orderBy(col("Casos").desc())

















