Documentação Projeto

Contextualização Do Projeto
O Projeto Simula Uma Plataforma Completa De Gestão:
 - Clientes
 - Profissionais
 - Planos
 - Reservas
 - Contratos
 - Pagamentos
 - Notificação E E-Mail
 - Estruturado 100% Em Arquitetura De Microsserviços
 - Cada Serviço É Independente E Escalável
 - Comunicação Via Eureka + Gateway + Docker

O Que É O Projeto
- Um ecossistema distribuído, composto por 11 serviços, que juntos formam uma plataforma capaz de:
- Criar usuários e autenticar com JWT
- Gerenciar clientes, profissionais e serviços
- Criar e gerenciar planos e contratos
- Processar pedidos de reserva
- Enviar e-mails e notificações
- Controlar pagamentos

O Que Motivou O Desenvolvimento
- Treinar arquitetura de microsserviços real
- Modernizar o aprendizado além de Monolito
- Entender comunicação distribuída (Eureka, Gateway, Load Balancing)
- Simular sistemas grandes usados no mercado
- Praticar:
- Docker
- API REST
- Integração assíncrona

Serviços Desenvolvidos

Domínio
- Auth Service
- Cliente Service
- Profissional Service
- Servico Service
- Plano Service
- Contrato Service
- Reserva Service
- Pagamento Service
- Email Service
- Notificacao Service

Infraestrutura
- Service Discovery (Eureka Server)
- Gateway Service
- Nginx

Estrutura do Projeto
  
1 .service-discovery/

- Responsável por registrar e descobrir automaticamente os serviços da aplicação.

Função:
- Implementa o Eureka Server.
- Todos os microserviços se registram nele e consultam outros serviços por nome.

Conteúdo típico:
- src/main/java/.../EurekaServer.java
- Configurações de porta e registrador Eureka.

2. auth-service/

Microserviço de autenticação e autorização.

Responsabilidades:
- Registrar usuários
- Autenticação (Login)
- Geração de tokens JWT
- Validação de token no Gateway
- Gestão de papéis/permissões

Componentes típicos:
- Controllers (login, register)
- Services (auth, user management)
- Repositórios
- Filtro JWT
- Configurações de segurança

3. cliente-service/

Gerencia informações sobre clientes.

Funções principais:
- Cadastro e atualização de clientes
- Consulta de clientes
- Exclusão (lógica ou total)
- Integração com contratos, reservas, faturamento etc.

Estrutura típica:
- Controllers
- Services
- Entities
- Repositories

4.contrato-service/

Gerencia os contratos entre clientes e a empresa.

Funções:
- Criação de contratos
- Vinculação de clientes
- Definição de valores, datas
- Atualização / cancelamento
- Relacionamento com faturamento e manutenção

5. faturamento-service/

Serviço que cuida de cobranças e faturas.

Responsabilidades:
- Criar faturas a partir dos contratos
- Registrar pagamentos
- Gerar relatórios financeiros
- Comunicar-se com o serviço de clientes, contratos e reservas

6. gateway-service/

O API Gateway central do sistema.

Função:
- Recebe todas as requisições externas
- Redireciona para o microserviço correto
- Aplica filtros de segurança (JWT)
- Faz rate limit / CORS / logs

Conteúdo típico:
- application.yml com rotas estáticas para cada serviço
- Filtro global de autenticação
- Integração com o Eureka (service-discovery)

7. manutencao-service/

Serviço de manutenção, normalmente relacionado a instalações, equipamentos ou contratos.

Funções:
- Registrar pedidos de manutenção
- Atribuir técnicos
- Registrar status (pendente, em andamento, resolvido)
- Integração com faturamento e contratos

8. Relatorio-service/

Microserviço focado em geração de relatórios consolidados.

Gera relatórios sobre:
- Clientes
- Contratos
- Faturamento
- Reservas
- Manutenções

Pode exportar:
- PDF
- Excel
- JSON consolidados

9. reserva-service/

Gerencia reservas feitas pelos clientes.

Funções:
- Criar, editar e cancelar reservas
- Checar disponibilidade
- Relacionar contratos e clientes
- Enviar dados ao faturamento, se necessário

10. docker-compose.yml

Arquivo responsável por subir todos os serviços simultaneamente.

Inclui:
- Todos os microserviços
- Eureka Server
- Gateway
- NGINX (se necessário)
- Cada serviço geralmente contém:
- porta exposta
- nome do container
- variáveis de ambiente
- link com o Eureka

11. nginx.conf

Arquivo de configuração do NGINX.

- Usado para:
- Load balancing
- Proxy reverso
- Redirecionamento para o gateway
- Configurar HTTPS (se ativado)
- Configurar caching

-----------------------------------------------------------------------------------------

Arquitetura Resumida

Cliente → NGINX → Gateway → Eureka (descobre serviços)
                ↓
        Auth Service → RabbitMQ → demo1 / notification / cupom
                ↓
    Outros serviços (cliente, contrato, reserva, etc.)


Fluxo da Arquitetura – Explicado em Tópicos (Super Claro)

Cliente
→ Qualquer pessoa/app que usa o sistema (navegador, celular, Postman, etc.)
→ Só conhece um único endereço: http://localhost

NGINX
→ Primeiro servidor que recebe a requisição do mundo externo
→ Faz terminação de HTTPS (certificado SSL)
→ Pode fazer load-balance se tiver mais de um Gateway
→ Redireciona tudo para o Gateway Service (porta 8080 internamente)

Gateway Service (Spring Cloud Gateway)
→ Ponto de entrada único da aplicação
→ Valida o token JWT (se a rota precisar de autenticação)
→ Consulta o Eureka para saber: “qual é o IP/porta atual do serviço X?”
→ Encaminha a requisição para o microsserviço correto (ex: /auth/** → auth-service, /cliente/** → cliente-service)

Eureka Server (service-discovery)
→ Banco de dados vivo de “quem está online”
→ Todos os microsserviços se registram automaticamente ao subir
→ Faz heartbeat a cada 30s → se um serviço cair, é removido da lista
→ Permite usar nomes lógicos (auth-service, cliente-service) em vez de IPs fixos

Auth Service
→ Responsável por cadastro e login de usuários
→ Quando cria um usuário → faz duas coisas:
  1. Salva no banco (síncrono)
  2. Publica um evento assíncrono no RabbitMQ (UserCreatedEvent)
  
RabbitMQ
→ Mensageria assíncrona (não bloqueia ninguém)
→ Recebe o evento do auth-service no exchange auth
→ Entrega cópias do evento para todas as filas vinculadas:
   → notification
   → cupom
   → (futuramente) email, sms, analytics, etc.
   
demo1 / notification / cupom (exemplo atual)
→ São consumidores da fila notification
→ Recebem o evento e reagem (ex: logam, geram cupom, enviam e-mail)
→ Podem estar em serviços diferentes, em máquinas diferentes, em linguagens diferentes

Outros serviços (cliente-service, contrato-service, reserva-service, etc.)
→ São chamados de forma síncrona pelo Gateway quando o cliente precisa (ex: listar contratos)
→ Também estão registrados no Eureka
→ Podem publicar seus próprios eventos no RabbitMQ quando quiserem (ex: contrato assinado → gera fatura)

------------------------------------------------------------------------------------

Ajustes realizados pelo Professor

Ajuste para Conexão
Configuração do Eureka Server (service-discovery)

A partir do commit `2fe773f`, o serviço `service-discovery` foi corretamente configurado como um **Eureka Server puro**, sem comportamento de cliente.

Alterações realizadas em `application.properties`

properties Desativa o comportamento de cliente Eureka (obrigatório no Eureka Server)
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

# Permite que o hostname seja resolvido pelo nome da aplicação (útil em Docker/K8s)
eureka.instance.hostname=${spring.application.name}

# Define a URL do Eureka Server usando o nome da aplicação e a porta do serviço
eureka.client.service-url.defaultZone=http://$$ {spring.application.name}: $${server.port}/eureka/ 

Por que essas mudanças foram feitas?

- O Eureka Server não deve se registrar nele mesmo nem buscar o registry (evita loops e comportamento inesperado).
- Usar ${spring.application.name} como hostname e na URL permite que outros microsserviços descubram o servidor através do nome do serviço em vez de localhost ou IP fixo.
- Essa configuração é essencial em ambientes containerizados (Docker, Docker Compose, Kubernetes), onde os serviços são resolvidos por nome via DNS interno.

Como os outros serviços devem apontar para este Eureka Server

Nos demais microsserviços, configure:

propertiesspring.application.name=nome-do-seu-servico
server.port=qualquer-porta

eureka.client.service-url.defaultZone=http://service-discovery:8080/eureka/
eureka.instance.hostname=${spring.application.name}

Com isso, todos os serviços se registrarão automaticamente no Eureka Server usando apenas o nome service-discovery, funcionando perfeitamente em desenvolvimento local e em produção/container.
Alteração aplicada em: 2fe773f – "Ajuste para conexão".

----------------------------------------------------------------------------------------

### Configuração Inicial do RabbitMQ (auth-service)

A partir do commit `f22fa5d` ("Configuração inicial rabbitmq"), o `auth-service` agora publica eventos de usuários via RabbitMQ para integração assíncrona com outros microsserviços.

#### Recursos Criados Automaticamente
- **Exchange**: `auth` (Topic – permite roteamento por padrões).
- **Filas Duráveis**:
  - `notification`: Para envios de e-mail/SMS de boas-vindas.
  - `cupom`: Para geração de cupons personalizados.
- **Routing Keys**:
  - `auth.user.created`: Dispara em criações de usuário.
  - `auth.user.updated`: Para futuras atualizações (binding pendente).

#### Configurações Chave
- **Conexão**: `host=rabbitmq`, user/pass `admin` (via Docker).
- **Publisher Confirms**: Ativados para entrega garantida (não perde eventos em falhas).

#### No Docker Compose

rabbitmq:
  image: rabbitmq:4.1.4-management
  container_name: rabbitmq
  environment:
    - RABBITMQ_DEFAULT_USER=admin
    - RABBITMQ_DEFAULT_PASS=admin
  ports:
    - "5672:5672"   # Protocolo AMQP
    - "15672:15672" # UI de gerenciamento
  networks:
    - app-network

-------------------------------------------------------------------------------------

   ### Implementação de Publicador e Consumidor RabbitMQ

A partir do commit `7ba2f19` ("RabbitMQ: Consumidor e Publicador"), o sistema agora suporta comunicação assíncrona completa entre `auth-service` (publicador) e `demo1` (consumidor) via eventos de usuário criado.

#### Fluxo de Eventos
1. **Publicação (auth-service)**: Ao registrar um usuário, publica `UserCreatedEvent` no exchange `auth` (routing key: `auth.user.created`).
2. **Consumo (demo1)**: Escuta a fila `notification` e reage (ex: loga o evento; expanda para envios de email/cupons).

#### Recursos Adicionados
- **Evento Compartilhado**: `UserCreatedEvent` (record com `userId`, `name`, `email`, `role`).
- **Publicador**: `UserCreatedPublisher` usa `RabbitTemplate` + JSON converter.
- **Consumidor**: `UserCreatedListener` com `@RabbitListener` na fila `notification`.
- **Configurações**: Exchanges, filas e bindings auto-inicializados; conexão via `rabbitmq:5672`.

#### Dependências Adicionadas (demo1/pom.xml)

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>



  






