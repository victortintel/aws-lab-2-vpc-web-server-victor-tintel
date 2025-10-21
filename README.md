# Criação de uma VPC personalizada e implantação de um servidor web na AWS

> Laboratório prático da AWS: construção de uma VPC do zero com sub-redes públicas e privadas, tabelas de rotas, gateways e implantação de um servidor web EC2 com Apache e PHP.

---

##  Objetivo

Após a conclusão deste laboratório, você será capaz de:

- Criar uma **VPC (Virtual Private Cloud)** personalizada;
- Criar **sub-redes públicas e privadas**;
- Configurar **tabelas de rotas**, **gateway da internet** e **NAT Gateway**;
- Criar e associar **grupos de segurança**;
- Iniciar uma **instância EC2** dentro da VPC e configurar um **servidor web Apache com PHP**.

>  Duração estimada: ~45 minutos

---

##  Cenário

Neste laboratório, foi simulada a construção de uma infraestrutura solicitada por um **cliente Fortune 100**, que precisava de uma **VPC isolada e segura** para hospedar um servidor web.  
A arquitetura foi projetada com **duas zonas de disponibilidade (AZs)**, garantindo **alta disponibilidade e redundância**.  
A rede inclui sub-redes públicas e privadas, gateways e regras de segurança para permitir conectividade controlada com a internet.

---

##  Arquitetura da Solução

A VPC foi construída com a seguinte estrutura:
VPC CIDR: 10.0.0.0/16
├── Sub-rede pública 1: 10.0.0.0/24
├── Sub-rede privada 1: 10.0.1.0/24
├── Sub-rede pública 2: 10.0.2.0/24
└── Sub-rede privada 2: 10.0.3.0/24


**Componentes principais:**
- **Internet Gateway** para acesso externo;
- **NAT Gateway** para saída controlada das sub-redes privadas;
- **Tabelas de rotas** separadas (pública e privada);
- **Grupos de segurança** com regras HTTP abertas (porta 80);
- **Servidor EC2 (Web Server 1)** com Apache + PHP;
- **Alta disponibilidade** entre duas zonas de disponibilidade (AZ A e AZ B).

---

##  Diagrama de Rede

**Tabela de rotas pública**
| Destino | Alvo |
|----------|------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | Gateway de internet |

**Tabela de rotas privada**
| Destino | Alvo |
|----------|------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | Gateway NAT |

---

## ⚙️ Etapas Executadas

###  Tarefa 1 — Criar a VPC
- Configuração via **VPC Wizard**;
- Criação automática de **Internet Gateway**, **NAT Gateway** e **tabelas de rotas**;
- CIDR principal: `10.0.0.0/16`.

###  Tarefa 2 — Criar sub-redes adicionais
- Criação de sub-redes públicas e privadas em **duas zonas de disponibilidade** (AZA e AZB);
- Configuração manual de blocos CIDR adicionais:
  - Pública 2: `10.0.2.0/24`
  - Privada 2: `10.0.3.0/24`

###  Tarefa 3 — Associar sub-redes e ajustar rotas
- Associação das sub-redes às **tabelas de rotas correspondentes**;
- A tabela pública foi vinculada à sub-rede pública 2;
- A tabela privada foi vinculada à sub-rede privada 2.

###  Tarefa 4 — Criar grupo de segurança
- Grupo: `Web Security Group`
- Descrição: `Enable HTTP access`
- Regras:
  - Tipo: HTTP (porta 80)
  - Origem: Anywhere (0.0.0.0/0)
  - Descrição: `Permit web requests`

###  Tarefa 5 — Iniciar instância EC2 (Web Server)
- AMI: **Amazon Linux 2 (HVM)**
- Tipo: `t3.micro`
- Par de chaves: `vockey`
- Rede: **Lab VPC**
- Sub-rede: **pública**
- IP público automático: **habilitado**
- Grupo de segurança: **Web Security Group**
- User Data (dados do usuário):
  ```bash
  #!/bin/bash
  #Instalar Apache Web Server e PHP
  yum install -y httpd mysql php
  #Baixar arquivos de laboratório
  wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-RESTRT-1/267-lab-NF-build-vpc-web-server/s3/lab-app.zip
  unzip lab-app.zip -d /var/www/html/
  #Ativar servidor web
  chkconfig httpd on
  service httpd start

---

## Resultado Final:

- VPC funcional com comunicação segura entre sub-redes públicas e privadas;
- Servidor web operacional dentro da VPC configurada;
- Alta disponibilidade garantida com múltiplas AZs;
- Isolamento de rede e segurança com grupos de segurança dedicados;
- Arquitetura replicável para projetos de clientes corporativos.
  
---
## Aprendizados Técnicos:

- Entendimento prático de Amazon VPC e sub-redes;
- Criação de infraestrutura isolada e escalável;
- Uso de NAT Gateway e Internet Gateway;
- Aplicação de boas práticas de segurança em rede;
- Deploy automatizado de aplicações via User Data;
- Validação de conectividade pública e privada na AWS.
  
---

## Descrição Detalhada:
Este laboratório teve como objetivo compreender e aplicar, de forma prática, os principais fundamentos de redes na nuvem (Cloud Networking) utilizando o serviço Amazon Virtual Private Cloud (Amazon VPC) da AWS. A atividade foi projetada para reproduzir um cenário real de um cliente corporativo de grande porte (Fortune 100) que necessitava de uma infraestrutura de rede personalizada, segura e escalável para hospedar um servidor web na nuvem.

A Amazon VPC é o serviço que permite criar ambientes de rede virtual completamente isolados dentro da nuvem AWS. Por meio dela, é possível definir endereços IP, sub-redes, gateways e rotas, controlando de forma granular o tráfego entre recursos internos e externos. Esse controle é essencial para garantir segurança, desempenho e alta disponibilidade em aplicações corporativas.

Durante a execução do laboratório, o primeiro passo foi a criação de uma VPC personalizada com o bloco CIDR 10.0.0.0/16, representando o espaço de endereçamento da rede. A partir dessa VPC, foram criadas sub-redes públicas e privadas, cada uma com seus respectivos blocos CIDR (10.0.0.0/24, 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24) distribuídas entre duas zonas de disponibilidade (AZs). Essa configuração é um exemplo clássico de arquitetura de alta disponibilidade (HA), permitindo que recursos críticos possam continuar operando mesmo que uma das zonas apresente falha.

Na sequência, foi configurado um Internet Gateway, responsável por permitir que os recursos dentro das sub-redes públicas se comuniquem com a Internet. Paralelamente, foi criado um NAT Gateway — componente essencial para que instâncias em sub-redes privadas possam acessar a Internet de forma controlada (por exemplo, para baixar atualizações ou pacotes), sem expor diretamente seus endereços IP públicos.

As tabelas de rotas (Route Tables) foram configuradas para definir o comportamento de roteamento da rede:
- A tabela pública direciona o tráfego da rede (0.0.0.0/0) para o Internet Gateway.
- A tabela privada redireciona o tráfego externo (0.0.0.0/0) para o NAT Gateway.
- Essas tabelas são fundamentais para o isolamento e controle do tráfego dentro da VPC, garantindo que apenas os recursos desejados sejam acessíveis publicamente.

Após a criação da infraestrutura de rede, o próximo passo foi configurar um grupo de segurança (Security Group), que funciona como um firewall virtual. Esse grupo foi configurado para permitir tráfego de entrada apenas na porta 80 (HTTP), liberando o acesso a partir de qualquer endereço IPv4 (0.0.0.0/0). Essa política de segurança é suficiente para o propósito do laboratório, mas, em ambientes de produção, recomenda-se aplicar políticas mais restritivas com base em endereços IP ou faixas específicas de clientes e serviços.

Com a infraestrutura de rede pronta, foi iniciada a instância EC2 que serviria como servidor web. A AMI escolhida foi a Amazon Linux 2, uma imagem estável e otimizada para ambientes AWS, executada no tipo de instância t3.micro, ideal para testes e laboratórios de baixo custo.

Após o provisionamento, a instância foi testada e validada. O endereço público (Public IPv4 DNS) foi acessado por meio de um navegador, resultando na exibição da mensagem de sucesso “Web Server Successfully Launched!”, confirmando que o servidor estava operacional e respondendo corretamente às requisições HTTP.

O resultado final foi uma VPC funcional e completa, com:
- Sub-redes públicas e privadas em múltiplas zonas de disponibilidade;
- Roteamento configurado para acesso seguro e eficiente à Internet;
- Servidor web EC2 implantado e operacional;
- Camada de segurança implementada via Security Group.

O laboratório ilustrou, na prática, como as camadas de rede, segurança e computação se integram para formar uma infraestrutura escalável e confiável. Além disso, consolidou conceitos importantes como:
- Isolamento lógico entre ambientes (público/privado);
- Importância do roteamento e da segmentação de rede;
- Automação de configuração via scripts;
- Boas práticas de segurança em ambientes cloud.

Com essa experiência, é possível compreender o papel fundamental da Amazon VPC como base para praticamente todos os serviços da AWS. Ela permite construir desde pequenos ambientes de desenvolvimento até infraestruturas complexas com balanceamento de carga, escalabilidade automática e políticas avançadas de segurança.

Esse conhecimento é essencial não apenas para arquitetos e engenheiros de nuvem, mas também para cientistas de dados e analistas de infraestrutura, que precisam compreender como os recursos computacionais são organizados, conectados e protegidos dentro da nuvem.

A conclusão deste laboratório reforça a importância de dominar os blocos de construção da AWS — VPC, EC2, Security Groups, NAT, Internet Gateway e Route Tables — como pré-requisito para avançar para temas mais avançados como Elastic Load Balancer (ELB), Auto Scaling, CloudFormation, Terraform e arquiteturas distribuídas de microserviços.

