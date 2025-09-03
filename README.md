# DimDim - Docker Compose Project (Spring Boot + MySQL)

Projeto pronto para abrir no VS Code e rodar com Docker Compose.  

---

## Arquitetura

### Arquitetura atual (antes da containerização)
- Aplicação monolítica rodando em ambiente local.  
- Banco de dados MySQL instalado manualmente.  
- Deploy manual e sem padronização.  

### Arquitetura futura (após containerização)
- **2 containers principais**:  
  - `app` → aplicação Spring Boot.  
  - `mysql_db` → banco de dados MySQL.  
- Rede interna do Docker para comunicação entre serviços.  
- Volume persistente para armazenar dados do banco.  
- Healthcheck para garantir que o MySQL esteja pronto antes da aplicação iniciar.  
- App rodando com usuário **sem privilégios administrativos**.  

---

## Estrutura
- `docker-compose.yml`  
- `backend/` (Spring Boot app - Maven)  
  - `Dockerfile` (multi-stage build)  
  - `pom.xml`  
  - `src/...`  

---

## Como usar (no terminal)

1. Conecte-se na VM e clone o repositório:  
   ```bash
   git clone https://github.com/EduardoSilva7/Checkpointdev
   cd Checkpointdev
   cd Checkpoint
   ```
2. Construa e suba os serviços:  
   ```bash
   docker-compose up --build -d
   ```
3. Verifique os logs:  
   ```bash
   docker-compose logs -f app
   docker logs -f mysql_db
   ```

---

## Conectar no banco de dados MySQL

Para abrir o cliente MySQL interativo dentro do container:  
```bash
docker exec -it mysql_db mysql -u dimdim -pdimdimpass -D dimdimdb
```

Depois disso, você pode executar comandos SQL manualmente, como:  
```sql
SHOW TABLES;
SELECT * FROM users;
```

---

## CRUD no banco usando `docker exec`

Todas as operações podem ser executadas diretamente pelo `docker exec` sem abrir o cliente.  
- Usuário: **dimdim**  
- Senha: **dimdimpass**  
- Banco: **dimdimdb**  

### Criar tabela e inserir registro (CREATE + READ)
```bash
docker exec -i mysql_db mysql -u dimdim -pdimdimpass -D dimdimdb -e "
CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100),
  email VARCHAR(100)
);
INSERT INTO users (nome, email) VALUES ('Eduardo', 'edu@teste.com');
SELECT * FROM users;
"
```

### Consultar registros (READ)
```bash
docker exec -i mysql_db mysql -u dimdim -pdimdimpass -D dimdimdb -e "SELECT * FROM users;"
```

### Atualizar registro (UPDATE + READ)
```bash
docker exec -i mysql_db mysql -u dimdim -pdimdimpass -D dimdimdb -e "
UPDATE users SET email='eduardo@novo.com' WHERE nome='Eduardo';
SELECT * FROM users;
"
```

### Deletar registro (DELETE + READ)
```bash
docker exec -i mysql_db mysql -u dimdim -pdimdimpass -D dimdimdb -e "
DELETE FROM users WHERE nome='Eduardo';
SELECT * FROM users;
"
```
---

## Logs da aplicação

Acompanhar em tempo real:  
```bash
docker-compose logs -f app
```

Ver apenas os últimos 50 registros:  
```bash
docker-compose logs --tail=50 app
```

---

## Parar e limpar
```bash
docker-compose down -v
```

---

## Troubleshooting rápido
- **Erro de conexão com MySQL** → aguarde o container do MySQL ficar saudável (`docker ps`, `docker logs mysql_db`).  
- **Porta 8080 ocupada** → altere o mapeamento em `docker-compose.yml`.  
- **Problema de permissão no Docker** → adicione o usuário ao grupo `docker`:  
  ```bash
  sudo usermod -aG docker $USER
  ```
