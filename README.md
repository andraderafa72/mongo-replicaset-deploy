# MongoDB Replica Set com Docker Compose

Este projeto configura um Replica Set do MongoDB 8.0 usando Docker Compose, com três nós (mongo1, mongo2, mongo3). Apenas o nó `mongo1` é exposto externamente, garantindo segurança e simplicidade na conexão.

---

## Estrutura dos serviços

- **mongo1**: Nó primário (ou candidato a primário), exposto na porta 27017 do host. Responsável por receber conexões externas da aplicação.
- **mongo2** e **mongo3**: Nós secundários, não expostos externamente, apenas acessíveis internamente na rede Docker.
- **mongo-init-replica**: Serviço que inicializa o Replica Set, rodando o comando `rs.initiate` após os containers Mongo estarem ativos.

---

## Variáveis de ambiente necessárias

| Variável                  | Descrição                                               | Exemplo               |
|---------------------------|---------------------------------------------------------|-----------------------|
| `MONGO_INITDB_ROOT_USERNAME` | Usuário root do MongoDB (admin)                         | `admin`               |
| `MONGO_INITDB_ROOT_PASSWORD` | Senha do usuário root                                    | `senhaForte123`       |
| `MONGO_KEYFILE`            | Conteúdo da keyfile para autenticação interna do Replica Set | String base64 ou texto da keyfile |

**Importante:** A keyfile é usada para autenticação entre os nós do Replica Set e deve ser igual em todos os containers. Ela garante segurança na comunicação interna entre os nós.

---

## Como funciona o Replica Set

- Cada container MongoDB roda o `mongod` com o parâmetro `--replSet rs0` e usa o keyfile para autenticação interna.
- O serviço `mongo-init-replica` executa o comando de inicialização do Replica Set, definindo os três membros com seus hostnames internos (`mongo1:27017`, etc).
- A comunicação entre os nós ocorre na rede Docker, sem expor portas dos secundários para o host.
- Sua aplicação conecta via o nó `mongo1`, na porta 27017, usando a string de conexão:

```text
mongodb://<root>:<senha>@<host>:27017/?replicaSet=rs0


## Arquivos

- `captain-definition`: Arquivo de definições do CapRover
- `docker-compose.yml`: Definição de containers