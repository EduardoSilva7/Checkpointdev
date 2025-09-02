# DimDim - Docker Compose Project (Spring Boot + MySQL)

Projeto pronto para abrir no VS Code e rodar com Docker Compose.

## Estrutura
- docker-compose.yml
- backend/ (Spring Boot app - Maven)
  - Dockerfile
  - pom.xml
  - src/...

## Requisitos do PDF (verificado)
- Mínimo 2 containers: ✅ (app + mysql)
- Usar imagens oficiais: ✅ (mysql, maven, openjdk)
- Persistência do banco via volume: ✅
- Healthcheck do MySQL: ✅
- Rodar app com usuário sem privilégios: ✅
- Documentação com comandos e troubleshooting: ✅

## Como usar (no terminal)
1. Abra no VS Code.
2. Construa e suba os serviços:
   ```bash
   docker-compose up --build -d
   ```
3. Verifique logs:
   ```bash
   docker-compose logs -f app
   docker-compose logs -f mysql
   ```
4. API endpoints:
   - GET    http://localhost:8080/users
   - POST   http://localhost:8080/users
   - GET    http://localhost:8080/users/{id}
   - PUT    http://localhost:8080/users/{id}
   - DELETE http://localhost:8080/users/{id}

## Observações
- A build do backend é feita dentro do Docker (multi-stage), portanto não precisa rodar `mvn package` localmente.
- Se preferir rodar sem Docker: execute `mvn spring-boot:run` em backend/ e um MySQL local com as mesmas credenciais.
- Para parar e remover volumes:
  ```bash
  docker-compose down -v
  ```

## Troubleshooting rápido
- Erro de conexão com MySQL: aguarde o container do mysql ficar saudável (veja `docker ps` e `docker logs mysql_db`).
- Porta 8080 ocupada: altere mapeamento em docker-compose.yml.
