# Fatechat

## Introdução

Uma aplicação de chat em Node.js alimentada por Kafka, Redis e WebSocket (Socket.io). Isso fornece robustez no processamento de mensagens em tempo real e escalabilidade — requisitos essenciais para praticamente qualquer aplicação de chat que precise lidar com diversos tipos de interações.

![Imagem](IMAGE)

## Funcionalidades

- Interface amigável para interação
- Registro de usuário
- Login/Autenticação
- Serviços de chat por sala
- Mensagens privadas
- Rastreamento de usuários online
- Recuperação de mensagens com baixa latência usando Redis
- Processamento escalável de mensagens com Kafka

## Índice

- [Introdução](#introdução)
- [Funcionalidades](#funcionalidades)
- [Instalação](#instalação)
- [Uso](#uso)
- [Eventos de Socket](#eventos-de-socket)
- [Interface do Usuário](#interface-do-usuário)
- [Design de Componentes](#design-de-componentes)
- [Conclusão](#conclusão)

## Instalação

Clone este repositório ou faça o download para o seu computador e execute `docker-compose up` para subir o ambiente.

Talvez você queira verificar os arquivos `docker-compose.yml` e `DockerfileServer` para alterar portas ou configurar seu próprio cluster.

## Uso

### APIs

Para acessar o chat recente, login, criação de usuário e usuários ativos, o backend oferece 5 APIs:

- `http://localhost:3000/api/login`
- `http://localhost:3000/api/register`
- `http://localhost:3000/api/chat/` — POST, GET
- `http://localhost:3000/api/chat/:id/messages`

A documentação detalhada das APIs está disponível [aqui](Postman API doc).

### Eventos de Socket

Para garantir a entrega rápida e eficiente das mensagens, os eventos de socket são gerenciados para facilitar a comunicação.

#### Eventos de Listener:
- `receiveMessage` — para ouvir mensagens recebidas
- `onlineUsers` — atualização quando usuários entram ou saem da sala
- `recentMessages` — quando o usuário dispara o evento `getRecentMessages`, recebe as mensagens recentes neste evento

#### Eventos Emitidos:
- `sendMessage` — enviado pelo frontend para transmitir uma nova mensagem
- `joinRoom` — disparado quando um novo usuário entra em uma sala
- `getRecentMessages` — requisita mensagens recentes
- `getOnlineUsers` — envia atualização de usuários online

Quando um usuário abre uma nova conexão socket ele é tratado como online, e ao fechar a aba ou perder a conexão o usuário é tratado como offline.

A documentação detalhada dos eventos de Socket está disponível [aqui](Chat App Socket Collection).

## Interface do Usuário

Após concluir a instalação, você pode interagir com a aplicação pela interface gráfica. Todas as funcionalidades, eventos e endpoints estão integrados ao front-end para oferecer uma experiência completa baseada em WebSocket.

Acesse `localhost:3000` para explorar o sistema. Para ver a aplicação funcionando, siga os passos abaixo:

1. Registre um novo usuário
2. Faça login
3. Crie uma nova sala
4. Veja as salas disponíveis
5. Entre em uma sala clicando no nome
6. Interaja pelo chat

![Imagem](IMAGE)

## Configuração

O arquivo de configuração da aplicação está no diretório raiz do projeto. As configurações para banco de dados, Redis e Kafka estão definidas para o ambiente Docker. Fique à vontade para personalizar essas configurações editando o arquivo `config.js`.

## Design de Componentes

Nesta aplicação de chat, a interação do usuário começa através de uma interface intuitiva, iniciando requisições que passam pelo servidor backend. Esse servidor executa tanto um servidor HTTP/REST quanto um servidor WebSocket, ambos acessíveis pela porta 3000. O servidor REST fornece acesso às APIs, enquanto o WebSocket gerencia as funcionalidades de mensagens em tempo real.

Para tornar o sistema mais responsivo, utilizamos Redis e MySQL em conjunto. Quando um usuário envia uma mensagem, ela segue um caminho dinâmico:

1. A mensagem é publicada no Kafka.
2. Consumidores dedicados nos servidores capturam a mensagem.
3. Ela é enviada imediatamente ao usuário alvo via WebSocket.

![Imagem](IMAGE)

O Kafka oferece suporte a mensagens distribuídas, garantindo escalabilidade e confiabilidade. Ele permite conectar e operar vários servidores WebSocket usando diferentes consumer groups. Assim, cada servidor recebe todas as mensagens, analisa quais devem ser enviadas aos seus usuários conectados e mantém o sistema escalável e eficiente.

Para reduzir ainda mais a latência, o fluxo de mensagens também é sincronizado com o Redis. A estrutura de dados no Redis mantém a relação entre usuários ativos e seus respectivos IDs de socket, garantindo rastreamento em tempo real por sala.

![Imagem](IMAGE)

Quando eventos como `joinRoom` ou `disconnect` acontecem, o Redis mantém o registro atualizado de usuários ativos, garantindo monitoramento dinâmico das salas.

Essa integração de componentes fornece uma experiência de comunicação eficiente e sem interrupções para os usuários.

## Conclusão

Em conclusão, esta aplicação oferece uma solução poderosa e escalável para mensagens em tempo real. Com funcionalidades como rastreamento de usuários, recuperação de mensagens com baixa latência e integração robusta com Kafka, ela representa uma arquitetura moderna de aplicações de chat.
