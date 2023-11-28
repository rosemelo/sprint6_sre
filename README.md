# Desafio 3
## Fazer o deploy do KIBANA no seu cluster EKS

- A princípio esse seria o desafio, mas você também pode de executar Elasticsearch e Kibana como contêineres Docker.
Para poder executar esse desafio, você precisará ter instalado o **Docker**

- Então agora vamos baixar e instalar o **Docker Compose**:
```sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose && sudo chmod +x /usr/bin/docker-compose
```


- Para confirmar que a instalação foi feita:
```docker-compose –version
```

- Vamos reagrupar as nossas variáveis de ambiente com o arquivo .env.
- Crie o arquivo **.env**:
```vi .env
```

- E coloque o seguinte conteúdo:
```# Version of Elastic products
STACK_VERSION=8.4.0
# Port to expose Elasticsearch HTTP API to the host
ES_PORT=9200
# Port to expose Kibana to the host
KIBANA_PORT=5601
```

- Como é um ambiente de teste não teremos senhas nem nada relacionado a segurança.
Para sair do **vi** aperte a tecla **Insert**, depois **Esc** para ter certeza de que não está no modo de inserção e digite **:wq** para salvar e e sair
 
- Agora vamos para o arquivo **docker-compose.yml**.
- Crie o arquivo:
```vi docker-compose.yml
```

- Insira o conteúdo:
```version: '3.8'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:${STACK_VERSION}
    container_name: elasticsearch
    volumes:
      - elasticsearch-data:/usr/share/elasticsearch/data
    ports:
      - ${ES_PORT}:9200
    restart: always
    environment:
      - xpack.security.enabled=false
      - discovery.type=single-node
    ulimits:
      memlock:
        soft: -1
        hard: -1
  kibana:
    depends_on:
      - elasticsearch
    image: docker.elastic.co/kibana/kibana:${STACK_VERSION}
    container_name: kibana
    volumes:
      - kibana-data:/usr/share/kibana/data
    ports:
     - ${KIBANA_PORT}:5601
    restart: always
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
volumes:
  elasticsearch-data:
    driver: local
  kibana-data:
    driver: local
```


**Obs.:** Aqui, estamos executando um cluster Elasticsearch versão 8.4.0 de nó único com a segurança desabilitada . O nome do nosso container é elasticsearch e caso haja algum travamento ou algum problema o container sempre irá reiniciar . Também precisamos especificar um volume para persistir os dados (você pode modificar as portas no arquivo .env ).

O mesmo para o Kibana, especificamos um nome para o contêiner ( kibana ), preenchemos ELASTICSEARCH_HOSTS como uma variável de ambiente para conectar o Kibana ao Elasticsearch. Também é importante especificar a propriedade depende_on para iniciar o contêiner somente se o contêiner Elasticsearch for iniciado.


- Agora, crie e inicie a instância Kibana e o cluster Elasticsearch de um nó executando o seguinte comando:
```docker-compose up -d
```


- Abra no navegador acessando:
```o_seu_ip:5601
```


- Para parar o cluster:
```docker-compose down
```


- Para excluir os containers e também os volumes:
```docker-compose down -v
```
