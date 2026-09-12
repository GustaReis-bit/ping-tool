# Docker Git Lab

Este repositório funciona como um laboratório prático para explorar conceitos de containers com Docker, composição de serviços com Compose e entrega de uma página estática via Nginx.

O projeto reúne duas abordagens de trabalho com containers:

- uma imagem base Ubuntu com o utilitário `ping`, criada a partir do `Dockerfile` raiz;
- uma aplicação web estática servida por Nginx, criada com um `Dockerfile.multistage` e orquestrada pelo `docker-compose.yml`.

## Objetivo

O objetivo principal é demonstrar, de forma simples e didática, como:

- empacotar uma imagem Docker com ferramentas de rede;
- criar uma página web estática com HTML, CSS e JavaScript;
- servir essa página com Nginx em um container;
- orquestrar a aplicação com Docker Compose usando bind mount para expor o conteúdo local.

## Estrutura do Repositório

```text
.
├── Dockerfile                 # Imagem Ubuntu com iputils-ping
├── docker-compose.yml         # Orquestração do serviço web
└── web/
    ├── Dockerfile.multistage   # Build multi-stage para Nginx
    └── site/
        ├── index.html
        ├── script.js
        └── style.css
```

## Componentes

### Imagem de rede com ping

O `Dockerfile` raiz usa a imagem `ubuntu:22.04` e instala o pacote `iputils-ping`, permitindo que o container rode de forma interativa com um shell Bash.

### Aplicação web estática

A pasta `web/site` contém uma página HTML minimalista com CSS e JavaScript que exibem uma tela voltada para demonstrações de Docker e Nginx.

A imagem web utiliza um build multi-stage:

1. `alpine:3.20` como etapa de preparação;
2. `nginx:1.27-alpine` como etapa final, copiando os arquivos estáticos para `/usr/share/nginx/html/`.

### Docker Compose

O arquivo `docker-compose.yml` define o serviço `web`, usando a imagem oficial do Nginx e mapeando a página local para o diretório padrão de conteúdo HTML do servidor:

```yaml
services:
  web:
    image: nginx:1.27-alpine
    container_name: site-compose
    ports:
      - "8082:80"
    volumes:
      - ./web/site:/usr/share/nginx/html
```

## Como executar

### 1. Construir a imagem de teste com ping

```bash
docker build -t ping-tool:1.0 .
```

### 2. Rodar o container com shell interativo

```bash
docker run -it --rm ping-tool:1.0
```

### 3. Subir o serviço web com Docker Compose

```bash
docker compose up --build
```

A aplicação ficará disponível em:

http://localhost:8082

### 4. Encerrar o ambiente

```bash
docker compose down
```

## Fluxo de uso

- Ajuste a página estática em `web/site`.
- Altere estilos ou o comportamento JavaScript em `script.js` e `style.css`.
- Recarregue o serviço com `docker compose up` para testar o conteúdo servido pelo Nginx.

## Requisitos

- Docker
- Docker Compose
- Navegador web para acessar a página local

## Observações

O projeto foi pensado para uma experiência de laboratório simples, com foco em didática e visualização imediata do comportamento dos containers. Ele serve como base para aprender build de imagens, uso do Dockerfile, locais de bind mount e publicação de conteúdo web em contêineres.