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





