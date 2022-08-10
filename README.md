# Projeto-Semantix
Projeto final do Curso Engenharia de Dados - Big Data.
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

