# Servidor WebDAV Baseado em Nginx com Docker

Imagem Docker de um servidor webDAV baseado em Nginx para uso pessoal, conforme a especificação [Class 2 WebDAV](https://stackoverflow.com/questions/58900793/what-is-a-level-2-webdav-server). É recomendável utilizá-lo com cautela devido à falta de compatibilidade retroativa, o que pode levar a problemas imprevistos.

## Uso

Crie um arquivo `.env` conforme o exemplo, `.env.example`, e defina as variáveis de ambiente `WEBDAV_USERNAME` e `WEBDAV_PASSWORD`. Você também pode criar um arquivo `.htpasswd` por conta própria e coloque-o no diretório `/config/nginx/.htpasswd`. Mesmo que a imagem seja projetada para uso atrás de proxy reverso, o servidor web deve ser acessado via HTTPS para evitar problemas com certos métodos WebDAV (como MOVE).

Utilize o comando `docker compose up -d` para iniciar o container do servidor WebDAV.

## Parametros de Configuração

Configure o container especificando variáveis de ambiente:

Nome | Descrição
--- | ---
`WEBDAV_USERNAME` | Nome do usuário WebDAV
`WEBDAV_PASSWORD` | Senha do usuário WebDAV
Verifique também os parâmetros da imagem baseada em [linuxserver/nginx](https://github.com/linuxserver/docker-nginx#parameters).

## Endpoints

Endereço | Requisito de Autenticação | Descrição
--- | --- | ---
`/dav` | sim | Servidor WebDAV
`/health` | não | Checagem de Saúde, sempre retorna `200`

## Exemplo de Configuração

```yml
networks:
  nginx-webdav-net:
    name: nginx-webdav-net
    driver: bridge

services: 
  nginx-webdav:
    container_name: nginx-webdav
    build: .
    networks:
      - nginx-webdav-net
    volumes:
      - ~/teste-webdav:/var/www/dav
    ports:
      - 443:443
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - WEBDAV_USERNAME=username
      - WEBDAV_PASSWORD=changeme
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-fk", "https://localhost/health"]
      start_period: 1m
      interval: 5s
      retries: 10
```

## Créditos

- [linuxserver/nginx](https://github.com/linuxserver/docker-nginx)
- [jan-di/docker-webdav](https://github.com/jan-di/docker-webdav)
