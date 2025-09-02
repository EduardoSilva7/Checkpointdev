# DimDim - Docker Compose Project (Spring Boot + MySQL)

Projeto pronto para abrir no VS Code e rodar com Docker Compose.  
Este repositório atende aos requisitos do **1º Checkpoint – 2º Semestre: Docker Compose (FIAP)**.

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

1. Abra no VS Code ou copie o projeto para a VM.  
2. Construa e suba os serviços:  
   ```bash
   docker-compose up --build -d
   ```
3. Verifique os logs:  
   ```bash
   docker-compose logs -f app
   docker-compose logs -f mysql_db
   ```

---

## CRUD no banco usando `docker exec`

Todas as operações devem ser feitas diretamente dentro do container do MySQL.  
- Usuário: **root**  
- Senha: **root**  

### Criar banco, tabela e inserir registro (CREATE + READ)
```bash
docker exec -i mysql_db mysql -u root -proot -e "
CREATE DATABASE IF NOT EXISTS dimdim;
USE dimdim;
CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100),
  email VARCHAR(100)
);
INSERT INTO users (nome, email) VALUES ('Eduardo', 'edu@teste.com');
SELECT * FROM users;
"
```

### Atualizar registro (UPDATE)
```bash
docker exec -i mysql_db mysql -u root -proot -e "
USE dimdim;
UPDATE users SET email='eduardo@novo.com' WHERE nome='Eduardo';
SELECT * FROM users;
"
```

### Deletar registro (DELETE)
```bash
docker exec -i mysql_db mysql -u root -proot -e "
USE dimdim;
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
