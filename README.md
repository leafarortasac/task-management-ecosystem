🏗️ Task Management Ecosystem - Hub Principal

Este repositório é o ponto central de uma arquitetura robusta de Gerenciamento de Tarefas. Ele orquestra um ecossistema de microsserviços distribuídos, infraestrutura de mensageria assíncrona, cache de alta performance e notificações em tempo real, garantindo que todo o ambiente seja provisionado de forma automática via Docker.

🌌 Arquitetura do Sistema

A solução foi desenhada seguindo os princípios de Sistemas Distribuídos e Event-Driven Architecture:

Segurança (IAM Service): Provedor de identidade centralizado que emite tokens JWT para proteção de todas as rotas.

Gerenciamento (Task Service): Core business para CRUD de tarefas, utilizando Redis para cache de consultas e MySQL para persistência relacional.

Mensageria Híbrida:

RabbitMQ: Utilizado para comunicação persistente e assíncrona entre o serviço de tarefas e o de notificações.

MQTT (Mosquitto): Protocolo leve utilizado para o push de notificações em tempo real diretamente para os clientes Mobile e Frontend.

Notificações (Notification Service): Consome eventos de novas tarefas e persiste o histórico em MongoDB antes de disparar o alerta via MQTT.

📂 Estrutura de Pastas (Organizacional)

Para o correto funcionamento do docker-compose.yml, os repositórios devem ser clonados seguindo a hierarquia abaixo. O orquestrador espera que os códigos-fonte estejam em pastas irmãs ou subpastas conforme definido no contexto de build:

      Plaintext
      /projeto-task-manager (Raiz)
      │
      ├── task-management-ecosystem/  <-- (Repositório Principal / Você está aqui)
      │   ├── mosquitto/              <-- Configurações do Broker MQTT
      │   ├── frontend-web/           <-- Código React (Vite)
      │   ├── task-app-mobile/        <-- Código React Native
      │   ├── docker-compose.yml      <-- Orquestrador Geral 
      │   └── .gitignore
      │
      ├── iam-service/                <-- Microsserviço de Identidade
      ├── task-service/               <-- Microsserviço de Tarefas
      ├── notification-service/       <-- Microsserviço de Notificações
      └── shared-contracts/           <-- Biblioteca de Contratos Java

🔗 Repositórios do Ecossistema

Para garantir o desacoplamento e a especialização de cada componente, o projeto foi dividido nos seguintes repositórios. O Hub Principal orquestra todos eles via Docker.

Hub Principal (Orquestrador): Ponto central de execução e configuração da infraestrutura (Docker Compose).

IAM Service (Identity): Microsserviço responsável pela segurança, autenticação e geração de tokens JWT.

Task Service (Core): Microsserviço de negócio para gerenciamento de tarefas e integração com Redis.

Notification Service: Microsserviço de consumo de eventos e disparo de notificações real-time.

Shared Contracts (Lib): Biblioteca Java compartilhada que define os DTOs e contratos de integração do ecossistema.

Frontend Web: Interface administrativa e de usuário desenvolvida em React.

Task App Mobile: Aplicativo móvel desenvolvido em React Native (Expo).

💡 Por que essa estrutura?

Como um projeto de Arquitetura de Microsserviços, a separação em repositórios independentes permite:

Escalabilidade Individual: Podemos escalar apenas o Notification-Service em momentos de pico de mensagens sem onerar o IAM.

Contratos Fortes: O uso do shared-contracts evita a duplicidade de código e garante que uma alteração no payload de uma tarefa seja refletida imediatamente em todos os serviços.

CI/CD Independente: Cada peça do ecossistema possui seu próprio ciclo de vida, permitindo deploys parciais e redução do "raio de instabilidade".

🚀 Links do Ecossistema

Hub Principal (Orquestrador Docker): https://github.com/leafarortasac/task-management-ecosystem

Segurança (IAM Service): https://github.com/leafarortasac/iam-service

Gerenciamento (Task Service): https://github.com/leafarortasac/task-service

Notificações (Notification Service): https://github.com/leafarortasac/notification-service

Contratos (Shared Contracts): https://github.com/leafarortasac/shared-contracts


🛠️ Infraestrutura Automatizada (Docker)

O projeto utiliza Docker Compose para subir 11 containers que compõem a infraestrutura de suporte:

MySQL 8.0: Persistência de tarefas e usuários.

MongoDB: Persistência NoSQL de notificações e logs.

Redis: Camada de cache para otimização de performance.

RabbitMQ: Broker para filas de mensagens assíncronas.

Mosquitto: Broker MQTT para notificações real-time.

Interfaces Gráficas (GUIs):

Mongo Express: Gestão do MongoDB (Porta 8085).

phpMyAdmin: Gestão do MySQL (Porta 8086).

Node-RED/Dashboard: Monitoramento de tráfego MQTT (Porta 1880).

🚀 Como Executar o Ecossistema Completo

   1. Clonagem
      Clone todos os módulos dentro da pasta raiz mencionada na estrutura acima. Certifique-se de que os nomes das pastas correspondam exatamente aos citados.

   2. Execução via Docker Compose
      Dentro da pasta task-management-ecosystem, utilize o comando abaixo para realizar o build multi-stage das imagens e subir a infraestrutura:

Bash
docker-compose up -d --build

Nota: O parâmetro --build é obrigatório na primeira execução para compilar os projetos Java e a biblioteca de contratos via Maven dentro dos containers.

📡 Documentação e Testes (Swagger)

Interaja com as APIs através das interfaces Swagger integradas:

IAM (Segurança): http://localhost:8080/swagger-ui.html

Tasks (Tarefas): http://localhost:8081/swagger-ui.html

Notifications (Alertas): http://localhost:8082/swagger-ui.html

RabbitMQ Console: http://localhost:15672 (guest/guest)

Mongo Express: http://localhost:8085 (admin/pass)

🏗️ Diferenciais de Nível Sênior

🛡️ Resiliência e Tolerância a Falhas

O sistema utiliza o padrão de Mensageria Assíncrona. Se o serviço de notificações estiver offline, o RabbitMQ armazena os eventos. Assim que o serviço retorna, ele processa a fila acumulada, garantindo entrega zero-loss.

⚡ Performance com Cache

Consultas à lista de tarefas são servidas pelo Redis. O cache é invalidado automaticamente em operações de escrita (Update/Delete), garantindo consistência e baixa latência.

📱 Real-Time Push

A integração com o MQTT permite que o App Mobile (React Native) receba alertas instantâneos sem realizar polling constante, otimizando bateria e consumo de dados.

📱 Guia de Teste Mobile (Expo Go)

Para validar a experiência mobile e o recebimento de notificações push em tempo real via MQTT, siga os passos abaixo:

      1. Instalação do Client
         O projeto utiliza o ecossistema Expo para facilitar o teste em dispositivos físicos sem a necessidade de compilação nativa imediata.
      
      Android: Baixe o Expo Go na Play Store.
      
      iOS: Baixe o Expo Go na App Store.
      
      2. Configuração de Rede (Importante)
         Para que o celular se comunique com o servidor Metro Bundler rodando no Docker:

      Certifique-se de que o seu celular e o seu computador estão conectados na mesma rede Wi-Fi.
      
      Verifique o IP local da sua máquina (ex: 192.168.0.83). Se o IP for diferente do configurado no docker-compose.yml, atualize a variável REACT_NATIVE_PACKAGER_HOSTNAME antes de subir o container.
      
      3. Inicialização e Pareamento
         Com o ecossistema rodando (docker-compose up), o Metro Bundler estará disponível na porta 9000.

      Abra o aplicativo Expo Go no seu celular.
      
      Selecione a opção "Enter URL manually".
      
      Digite o endereço utilizando o seu IP local:
      
      Plaintext
      exp://192.168.0.83:9000
      O app iniciará o download do bundle diretamente do container Docker e abrirá a interface de login.
      
      4. Resolução de Problemas (Troubleshooting)
         Tela Azul / Network Error: Se o app não carregar, verifique se o seu Firewall não está bloqueando a porta 9000.

Logs do Bundler: Para acompanhar o status da conexão do celular com o servidor de build, utilize o comando:

Bash
docker logs -f task-mobile-bundler
Cache: O container está configurado com a flag --clear, garantindo que toda subida de ambiente limpe caches antigos que possam causar conflitos de IP.
