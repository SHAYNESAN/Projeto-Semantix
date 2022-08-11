## Projeto-Semantix

Projeto final do Curso Engenharia de Dados - Big Data - Semantix

Projeto com analise dos dados do Covid19 no Brasil no periodo:03-2019 a 06/07/21.

Dados utilizados:     
https://mobileapps.saude.gov.br/esus-vepi/files/unAFkcaNDeXajurGB7LChj8SgQYS2ptm/04bd3419b22b9cc5c6efac2c6528100d_HIST_PAINEL_COVIDBR_06jul2021.rar

Referência das Visualizações:

Site: https://covid.saude.gov.br/


Utilizei o docker para subir os contaniers do servicos Hive, kafa, Spark, Elasticsearch. 

Primeiros comandos:

docker-compose up -d

docker exec -it hive-server bash.

beeline -u jdbc:hive2://localhost:10000

#### Criar database e tabela com os parametros

![Acessando_configurando_beeline](https://user-images.githubusercontent.com/39307787/183940349-a435724d-f9ac-4735-80bb-313fe73d9a2d.png)

#### Criação da tabela 

![Criando_tabela_hive_Particionada](https://user-images.githubusercontent.com/39307787/183940685-a120b69b-d380-4f8a-8962-e3ff91dce40c.png)

#### 2 parte executada no Jupyter Notebook

    
 ![Captura de tela de 2022-08-09 21-38-14](https://user-images.githubusercontent.com/39307787/183941122-df400206-2878-47e0-8352-af4334ae8596.png)

Salvando o Df como tabela hive particionada por munincipo

![Captura de tela de 2022-08-10 12-17-07](https://user-images.githubusercontent.com/39307787/183942020-dd75f27b-2a18-4f55-a8f2-6e64881437f3.png)

#### 3. Criar as 3 vizualizações pelo Spark com os dados enviados para o HDFS:

![Captura de tela de 2022-08-10 12-21-03](https://user-images.githubusercontent.com/39307787/183942898-451d46fe-eab7-419d-8f59-61b385640bae.png)

![Captura de tela de 2022-08-10 12-22-50](https://user-images.githubusercontent.com/39307787/183943263-3e63e340-0055-4d61-99fb-438285c7cd45.png)

![Captura de tela de 2022-08-10 12-25-10](https://user-images.githubusercontent.com/39307787/183943871-fabcf47b-b32a-42a2-a030-2e2fd77a252c.png)

 #### Enviar a tabela para o Kafka
 
![Captura de tela de 2022-08-10 12-26-35](https://user-images.githubusercontent.com/39307787/183944155-abc1e91e-6067-4988-b1ef-151a2dc11286.png)

#### Panorama geral do Covid19

![Captura de tela de 2022-08-10 12-30-35](https://user-images.githubusercontent.com/39307787/183945722-8ba48c0e-9d58-43cb-a1bc-6e17edb821af.png)

![Captura de tela de 2022-08-10 12-32-20](https://user-images.githubusercontent.com/39307787/183945511-bf786db2-59c0-4adb-8b1b-4b09763a13d7.png)

#### Visualizações dos dados no Elasticsearch - kibana

![Discover_kibana](https://user-images.githubusercontent.com/39307787/183946320-d9358ebb-1f81-4332-b88a-c5e419fb35b3.png)

![Dashboard_kibana](https://user-images.githubusercontent.com/39307787/183946435-396b863f-3a13-45a8-96e6-fe2ca72ab128.png)























