# Descomplicando Gateway API no Kubernetes
Escrito por **Jeferson Fernando** para a **LINUXtips**

<div style="page-break-after: always;"></div>

## Índice

- [Descomplicando Gateway API no Kubernetes](#descomplicando-gateway-api-no-kubernetes)
  - [Índice](#índice)
  - [O que vamos aprender?](#o-que-vamos-aprender)
  - [Pré-requisitos](#pré-requisitos)
  - [O que é a Gateway API e por que ela é melhor que o Ingress no Kubernetes?](#o-que-é-a-gateway-api-e-por-que-ela-é-melhor-que-o-ingress-no-kubernetes)
    - [Evolução do Ingress](#evolução-do-ingress)
    - [Como Funciona a Gateway API?](#como-funciona-a-gateway-api)
  - [Benefícios da Gateway API em Relação ao Ingress](#benefícios-da-gateway-api-em-relação-ao-ingress)
    - [Flexibilidade e Extensibilidade](#flexibilidade-e-extensibilidade)
    - [Controle e Granularidade](#controle-e-granularidade)
    - [Separação de Preocupações](#separação-de-preocupações)
    - [Suporte a Funcionalidades Avançadas](#suporte-a-funcionalidades-avançadas)
  - [Quando Usar o Ingress e Quando Usar a Gateway API?](#quando-usar-o-ingress-e-quando-usar-a-gateway-api)
  - [Representação Avançada do **Ingress**](#representação-avançada-do-ingress)
  - [Representação Avançada da **Gateway API**](#representação-avançada-da-gateway-api)
  - [Comparação Detalhada entre Ingress e Gateway API](#comparação-detalhada-entre-ingress-e-gateway-api)
  - [Passo 1: Criando o Cluster com o KIND](#passo-1-criando-o-cluster-com-o-kind)
    - [Criando o arquivo de configuração](#criando-o-arquivo-de-configuração)
    - [Criando o cluster](#criando-o-cluster)
  - [Passo 2: Instalando o NGINX Gateway Fabric](#passo-2-instalando-o-nginx-gateway-fabric)
    - [Aplicando as CRDs](#aplicando-as-crds)
    - [Implantando o NGINX Gateway](#implantando-o-nginx-gateway)
  - [Passo 3: Configurando o Gateway e o Serviço](#passo-3-configurando-o-gateway-e-o-serviço)
    - [Criando o Gateway](#criando-o-gateway)
    - [Configurando o serviço do Gateway](#configurando-o-serviço-do-gateway)
  - [Passo 4: Verificando a Instalação](#passo-4-verificando-a-instalação)
    - [Verificando os pods](#verificando-os-pods)
    - [Testando o acesso ao Gateway](#testando-o-acesso-ao-gateway)
  - [Passo 5: Implantando Duas Aplicações e Testando o Roteamento](#passo-5-implantando-duas-aplicações-e-testando-o-roteamento)
    - [Implantando as aplicações](#implantando-as-aplicações)
      - [Criando o Deployment da app1](#criando-o-deployment-da-app1)
      - [Criando o Deployment da app2](#criando-o-deployment-da-app2)
      - [Aplicando os Deployments](#aplicando-os-deployments)
      - [Verificando os Pods](#verificando-os-pods-1)
    - [Criando os serviços para as aplicações](#criando-os-serviços-para-as-aplicações)
      - [Serviço para a app1](#serviço-para-a-app1)
      - [Serviço para a app2](#serviço-para-a-app2)
      - [Aplicando os Serviços](#aplicando-os-serviços)
      - [Verificando os Serviços](#verificando-os-serviços)
    - [Configurando o HTTPRoute](#configurando-o-httproute)
      - [Aplicando o HTTPRoute](#aplicando-o-httproute)
      - [Verificando o HTTPRoute](#verificando-o-httproute)
    - [Testando o roteamento](#testando-o-roteamento)
      - [Testando a app1](#testando-a-app1)
      - [Testando a app2](#testando-a-app2)
      - [Analisando o Resultado](#analisando-o-resultado)
    - [Verificando os logs](#verificando-os-logs)
      - [Logs do NGINX Gateway](#logs-do-nginx-gateway)
      - [Logs das Aplicações](#logs-das-aplicações)
    - [Escalando as aplicações](#escalando-as-aplicações)
      - [Escalando a app1 para 4 réplicas](#escalando-a-app1-para-4-réplicas)
  - [Passo 6: Configurações Avançadas de HTTPRoute](#passo-6-configurações-avançadas-de-httproute)
    - [Redirecionamento de Requisições](#redirecionamento-de-requisições)
      - [Criando o Arquivo de Redirecionamento](#criando-o-arquivo-de-redirecionamento)
      - [Aplicando a Configuração](#aplicando-a-configuração)
      - [Testando o Redirecionamento](#testando-o-redirecionamento)
    - [Roteamento com Balanceamento de Carga Ponderado](#roteamento-com-balanceamento-de-carga-ponderado)
      - [Criando o Arquivo de Balanceamento Ponderado](#criando-o-arquivo-de-balanceamento-ponderado)
      - [Aplicando a Configuração](#aplicando-a-configuração-1)
      - [Testando o Balanceamento de Carga](#testando-o-balanceamento-de-carga)
    - [Roteamento Baseado em Parâmetros de Consulta](#roteamento-baseado-em-parâmetros-de-consulta)
      - [Criando o Arquivo de Roteamento por Parâmetros](#criando-o-arquivo-de-roteamento-por-parâmetros)
      - [Aplicando a Configuração](#aplicando-a-configuração-2)
      - [Testando o Roteamento por Parâmetros](#testando-o-roteamento-por-parâmetros)
    - [Implementando Rate Limiting](#implementando-rate-limiting)
      - [Criando o Arquivo de Rate Limiting](#criando-o-arquivo-de-rate-limiting)
      - [Aplicando a Configuração](#aplicando-a-configuração-3)
      - [Testando o Rate Limiting](#testando-o-rate-limiting)
      - [Verificando os Logs](#verificando-os-logs-1)
    - [Implementando Reescrita de URL (URL Rewrite)](#implementando-reescrita-de-url-url-rewrite)
      - [Criando o Arquivo de Reescrita de URL](#criando-o-arquivo-de-reescrita-de-url)
      - [Aplicando a Configuração](#aplicando-a-configuração-4)
      - [Testando a Reescrita de URL](#testando-a-reescrita-de-url)
    - [Redirecionamento de Requisições e Roteamento para Aplicações Internas](#redirecionamento-de-requisições-e-roteamento-para-aplicações-internas)
      - [Criando o Arquivo de Roteamento Combinado](#criando-o-arquivo-de-roteamento-combinado)
      - [Aplicando a Configuração](#aplicando-a-configuração-5)
      - [Testando o Redirecionamento e Roteamento](#testando-o-redirecionamento-e-roteamento)
    - [Tabela de Resumo das Configurações Avançadas](#tabela-de-resumo-das-configurações-avançadas)
    - [Verificando os Logs](#verificando-os-logs-2)
  - [Passo 7: Configurando Roteamento com TLS](#passo-7-configurando-roteamento-com-tls)
    - [Criando o Deployment da Secure Service](#criando-o-deployment-da-secure-service)
      - [Criando o Arquivo `secure-service.yaml`](#criando-o-arquivo-secure-serviceyaml)
      - [Aplicando o Deployment](#aplicando-o-deployment)
    - [Configurando o Gateway com Suporte a TLS](#configurando-o-gateway-com-suporte-a-tls)
      - [Criando o Arquivo `gateway-tls.yaml`](#criando-o-arquivo-gateway-tlsyaml)
      - [Aplicando a Configuração do Gateway](#aplicando-a-configuração-do-gateway)
    - [Criando o Serviço para a Secure Service](#criando-o-serviço-para-a-secure-service)
      - [Criando o Arquivo `secure-service-svc.yaml`](#criando-o-arquivo-secure-service-svcyaml)
      - [Aplicando o Serviço](#aplicando-o-serviço)
    - [Configurando o Secret com o Certificado TLS](#configurando-o-secret-com-o-certificado-tls)
      - [Gerando Certificados TLS Autoassinados](#gerando-certificados-tls-autoassinados)
      - [Criando o Secret TLS no Kubernetes](#criando-o-secret-tls-no-kubernetes)
    - [Criando o HTTPRoute para a Rota Segura](#criando-o-httproute-para-a-rota-segura)
      - [Criando o Arquivo `https_route.yaml`](#criando-o-arquivo-https_routeyaml)
      - [Aplicando a Configuração](#aplicando-a-configuração-6)
    - [Verificando a Instalação](#verificando-a-instalação)
      - [Verificando os Pods](#verificando-os-pods-2)
      - [Verificando os Serviços](#verificando-os-serviços-1)
    - [Testando o Roteamento com TLS](#testando-o-roteamento-com-tls)
      - [Testando a Conexão HTTPS](#testando-a-conexão-https)
    - [Entendendo em Detalhes](#entendendo-em-detalhes)
      - [Fluxo da Requisição com TLS](#fluxo-da-requisição-com-tls)
    - [Verificando os Logs](#verificando-os-logs-3)
      - [Logs do NGINX Gateway](#logs-do-nginx-gateway-1)
      - [Logs da Secure Service](#logs-da-secure-service)
  - [Resumo da Brincadeira toda](#resumo-da-brincadeira-toda)
  - [Recursos Adicionais](#recursos-adicionais)

<div style="page-break-after: always;"></div>

## O que vamos aprender?

Neste artigo completo da **LINUXtips**, escrito por **Jeferson Fernando**, você aprenderá a montar um cluster Kubernetes utilizando o **KIND** (Kubernetes IN Docker) e a configurar o **NGINX Gateway Fabric** para gerenciar eficientemente o tráfego das suas aplicações. Abordaremos desde a instalação dos pré-requisitos essenciais, como **Docker**, **kubectl** e **KIND**, até configurações avançadas de roteamento utilizando **HTTPRoute**, incluindo balanceamento de carga, redirecionamentos, rate limiting e segurança **TLS**. Além disso, você descobrirá como implantar aplicações, testar o roteamento, escalar serviços e monitorar logs para garantir um ambiente Kubernetes robusto, seguro e escalável. Este artigo da [LINUXtips.io](https://linuxtips.io) é ideal para desenvolvedores e administradores que desejam otimizar a gestão de tráfego em seus clusters Kubernetes de maneira eficiente e segura.

Prepare-se para transformar sua infraestrutura com soluções avançadas e seguras!

## Pré-requisitos

Antes de começarmos, certifique-se de que sua máquina possui os seguintes componentes **instalados** e **funcionando corretamente**:

- **Docker**: Fundamental para a criação de contêineres. [Instale o Docker](https://docs.docker.com/get-docker/).
- **kubectl**: Ferramenta de linha de comando para interagir com Kubernetes. [Instale o kubectl](https://kubernetes.io/docs/tasks/tools/).
- **KIND**: Utilizado para criar clusters Kubernetes dentro de contêineres Docker. [Instale o KIND](https://kind.sigs.k8s.io/docs/user/quick-start/).

> **Dica:** Caso ainda não tenha algum desses componentes, recomendo conferir os tutoriais oficiais ou os vídeos da **LINUXtips** para uma instalação detalhada.

<div style="page-break-after: always;"></div>

## O que é a Gateway API e por que ela é melhor que o Ingress no Kubernetes?

A **Gateway API** é uma iniciativa mais recente no Kubernetes, projetada para superar as limitações do **Ingress**, oferecendo um conjunto de APIs mais **flexíveis** e **extensíveis** para gerenciar o tráfego de rede. Ela não substitui o Ingress, mas evolui a partir dele, trazendo soluções para demandas mais complexas e necessidades não atendidas pelo modelo de Ingress.

### Evolução do Ingress

O Ingress é amplamente utilizado e, para muitas aplicações, cumpre bem seu papel. No entanto, conforme as infraestruturas crescem e as demandas por funcionalidades avançadas aumentam, o Ingress começa a mostrar suas limitações. A **Gateway API** surgiu justamente para atender a esses cenários mais sofisticados, oferecendo uma abordagem modular, separação clara de responsabilidades e suporte a diversos protocolos, como HTTP, HTTPS, TCP e TLS, de forma nativa.

### Como Funciona a Gateway API?

A **Gateway API** introduz uma estrutura hierárquica de recursos, como **GatewayClass**, **Gateway**, **HTTPRoute**, **TLSRoute** e **TCPRoute**, permitindo uma separação mais clara das responsabilidades e maior flexibilidade na configuração do tráfego de rede. Veja alguns dos principais benefícios:

- **Extensibilidade:** Permite que os provedores de infraestrutura adicionem funcionalidades específicas sem comprometer a compatibilidade com a API padrão.
- **Controle Granular:** Suporta cenários complexos, onde diferentes equipes podem gerenciar diferentes aspectos do tráfego de rede, como roteamento e segurança.

&nbsp;

## Benefícios da Gateway API em Relação ao Ingress

Abaixo, destacamos os principais diferenciais da Gateway API em relação ao Ingress:

### Flexibilidade e Extensibilidade

- **Ingress:** Limitado a roteamento de HTTP/HTTPS e sem suporte nativo a outros protocolos.
- **Gateway API:** Suporta múltiplos protocolos (HTTP, HTTPS, TCP, TLS) e permite a adição de novas funcionalidades sem modificar a API principal, proporcionando maior flexibilidade para diferentes cenários.

### Controle e Granularidade

- **Ingress:** Possui uma API mais simples e direta, ideal para cenários menos complexos, mas não oferece o mesmo nível de controle granular sobre o roteamento e balanceamento de carga.
- **Gateway API:** Oferece maior controle sobre o comportamento do tráfego, permitindo definir regras avançadas de roteamento, como baseado em cabeçalhos, políticas de segurança e balanceamento de carga por diferentes protocolos.

### Separação de Preocupações

- **Ingress:** Combina a definição de regras de roteamento com a implementação do controlador de ingressos, limitando a capacidade de gerenciamento por diferentes equipes.
- **Gateway API:** A separação entre os recursos **Gateway** (gerencia o ponto de entrada) e **HTTPRoute** ou **TCPRoute** (definem as regras de roteamento) permite uma gestão mais organizada e distribuída, facilitando a colaboração entre equipes de infraestrutura e desenvolvimento.

### Suporte a Funcionalidades Avançadas

- **Ingress:** Funcionalidades avançadas, como roteamento por cabeçalhos ou autenticação, dependem fortemente do controlador específico de Ingress, sem padronização.
- **Gateway API:** Oferece suporte nativo a recursos avançados de roteamento, permitindo uma padronização consistente entre diferentes implementações e provedores de serviços.

&nbsp;

## Quando Usar o Ingress e Quando Usar a Gateway API?

- **Ingress:** Ideal para cenários onde uma solução simples de roteamento HTTP/HTTPS é suficiente. Se você já utiliza o Ingress no seu cluster e ele atende às suas necessidades atuais, pode não haver necessidade imediata de migrar.

- **Gateway API:** Recomendado para infraestruturas mais complexas, onde há a necessidade de suporte a protocolos adicionais como **TCP** ou **TLS**, maior flexibilidade de **customização** e **controle de tráfego**, ou quando diferentes equipes precisam gerenciar partes distintas da configuração de rede.

> **Dica do Jefim:** Avalie bem as necessidades do seu projeto antes de decidir entre **Ingress** e **Gateway API**. Se a flexibilidade, suporte a múltiplos protocolos e o controle granular forem fundamentais, a **Gateway API** é o caminho ideal!

&nbsp;

## Representação Avançada do **Ingress**

```plaintext
                 +---------------------------------------+
                 | Requisições Externas (HTTP/HTTPS)     |
                 |       (Usuários / Aplicações)         |
                 +---------------------------------------+
                                |
                                v
                  +-----------------------------------+
                  |     Controlador de Ingress        |
                  | (Trata das regras de roteamento)  |
                  +-----------------------------------+
                                |
            +-------------------+-------------------+
            |                                       |
            v                                       v
      +---------------------+                 +---------------------+
      | Regra de Ingress 1  |                 | Regra de Ingress 2  |
      | (/app1 -> Serviço A)|                 | (/app2 -> Serviço B)|
      +---------------------+                 +---------------------+
            |                                       |
            v                                       v
      +----------------+                     +----------------+
      |   Serviço A    |                     |   Serviço B    |
      |   (ClusterIP)  |                     |   (ClusterIP)  |
      +----------------+                     +----------------+
            |                                       |
            v                                       v
       +------------+                        +------------+
       |  Pod A1    |                        |  Pod B1    |
       +------------+                        +------------+
       |  Pod A2    |                        |  Pod B2    |
       +------------+                        +------------+
            ^                                       ^
            |                                       |
      +-------------------------------+  +-------------------------------+
      | Balanceamento de Carga pelo    |  | Balanceamento de Carga pelo   |
      | Serviço Kubernetes (ClusterIP) |  | Serviço Kubernetes (ClusterIP)|
      +-------------------------------+  +-------------------------------+
```

**Detalhes do Ingress:**

- **Controlador de Ingress**: Recebe todas as requisições externas que chegam ao cluster. Interpreta as regras definidas em **Ingress** e direciona o tráfego para os serviços corretos com base em padrões de caminho, hosts ou protocolos.
- **Regra de Ingress**: Cada regra (por exemplo, `/app1` ou `/app2`) define como o tráfego é roteado para os serviços.
- **Serviços (ClusterIP)**: Intermediários que ligam o tráfego recebido ao conjunto de **Pods** responsáveis por processar a requisição.
- **Balanceamento de Carga Interno**: O Kubernetes utiliza o balanceamento de carga entre os pods de um serviço automaticamente para distribuir o tráfego.

> **Limitações**:
> - Suporte a **HTTP** e **HTTPS** apenas.
> - Não há separação clara de responsabilidades (roteamento e controle de tráfego estão agrupados no Controlador de Ingress).
> - Funcionalidades avançadas, como roteamento por cabeçalhos ou manipulação de tráfego, dependem fortemente do controlador de Ingress específico.

&nbsp;

## Representação Avançada da **Gateway API**

```plaintext
                 +-------------------------------------------+
                 | Requisições Externas (Multi-Protocolo)    |
                 |       (Usuários / Aplicações)             |
                 +-------------------------------------------+
                                |
                                v
                  +-----------------------------------+
                  |              Gateway              |
                  +-----------------------------------+
                                |
               +----------------+----------------+
               |                                 |
               v                                 v
        +---------------+                 +----------------+
        |   HTTPRoute   |                 |    TCPRoute    |
        |  (para HTTP)  |                 |  (para TCP)    |
        +---------------+                 +----------------+
               |                                 |
       +-----------------------+         +-----------------------+
       | Correspondência de    |         | Correspondência de    |
       | Caminho               |         | Porta                 |
       | (/api/* -> Serviço A) |         | (Porta 3306 -> SVC DB)|
       +-----------------------+         +-----------------------+
               |                                 |
               v                                 v
       +----------------+                 +----------------+
       |   Serviço A    |                 |   Serviço DB   |
       |   (ClusterIP)  |                 |   (ClusterIP)  |
       +----------------+                 +----------------+
               |                                 |
               v                                 v
         +------------+                     +------------+
         |  Pod A1    |                     |  Pod DB1   |
         +------------+                     +------------+
         |  Pod A2    |                     |  Pod DB2   |
         +------------+                     +------------+

       +---------------------------------------------+
       | Aplicação de Políticas (Autenticação,       |
       | Limitação de Taxa, etc.)                    |
       +---------------------------------------------+
```

**Detalhes da Gateway API:**

- **Gateway**: Define o ponto de entrada para o tráfego, incluindo os protocolos e portas. Um **Gateway** pode ter múltiplos **listeners** (por exemplo, para HTTP, TCP, TLS).
- **Rotas (HTTPRoute, TCPRoute, etc.)**: As rotas são definidas separadamente, permitindo uma divisão de responsabilidades. Cada rota (**HTTPRoute**, **TCPRoute**) define como o tráfego deve ser roteado e para qual serviço deve ser direcionado.
  - **HTTPRoute**: Regras específicas para tráfego HTTP, permitindo rotas baseadas em **caminhos**, **cabeçalhos** e **métodos HTTP**.
  - **TCPRoute**: Suporte a **TCP** ou outros protocolos, com roteamento baseado em portas.
- **Serviços**: Como no Ingress, os serviços são intermediários que conectam o tráfego aos pods dentro do cluster.
- **Políticas Avançadas**: A Gateway API permite aplicar políticas de tráfego diretamente nas rotas, como **autenticação**, **rate limiting** (limitação de taxa) e **TLS termination**, oferecendo um controle mais granular.

&nbsp;

## Comparação Detalhada entre Ingress e Gateway API

| Recurso                             | Ingress                                        | Gateway API                                     |
|-------------------------------------|------------------------------------------------|-------------------------------------------------|
| **Suporte a Múltiplos Protocolos**  | HTTP/HTTPS apenas                              | Suporte a HTTP, HTTPS, TCP, TLS e mais          |
| **Separação de Responsabilidades**  | Regras e controle de tráfego estão agrupados   | Regras de roteamento e controle de tráfego são separadas (ex: HTTPRoute, TCPRoute) |
| **Controle Granular de Tráfego**    | Funcionalidades básicas, controle limitado     | Controle avançado com roteamento baseado em cabeçalhos, métodos e políticas de segurança |
| **Extensibilidade**                 | Limitado aos controladores específicos         | Suporte a extensões e integração com provedores de serviços de forma nativa |
| **Balanceamento de Carga**          | Balanceamento simples entre pods via Serviço   | Balanceamento mais flexível, controle via políticas diretamente no Gateway |
| **Gestão Colaborativa**             | Limitada                                       | Permite delegar controle a diferentes equipes, com gerenciamento de rotas e políticas por namespaces diferentes |
| **Suporte a Políticas**             | Depende do controlador                         | Suporte a autenticação, rate limiting e outras políticas de rede diretamente nas rotas |

Portanto, **Ingress** e **Gateway API** são ferramentas poderosas para gerenciar o tráfego de rede em **Kubernetes**, cada uma com seus prós e contras. O **Ingress** é uma solução simples, amplamente suportada e ideal para cenários básicos de roteamento. Já a **Gateway API** oferece uma evolução significativa, permitindo uma configuração mais complexa e modular para infraestruturas avançadas. A escolha entre as duas soluções dependerá da **complexidade** do seu ambiente e das funcionalidades que você precisa implementar.

&nbsp;

## Passo 1: Criando o Cluster com o KIND

Vamos iniciar criando um cluster Kubernetes usando o **KIND**. Para isso, precisamos de um **arquivo de configuração** que define a estrutura do nosso cluster e as portas que serão expostas.

### Criando o arquivo de configuração

Crie um arquivo chamado `kind.yaml` com o seguinte conteúdo:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 31437
        hostPort: 8080
        protocol: TCP
      - containerPort: 31438
        hostPort: 8443
        protocol: TCP
```

**Explicação:**

- **Control-plane**: Define um único nó de controle.
- **Port Mappings**: Mapeia as portas `31437` e `31438` do contêiner para `8080` e `8443` do host, respectivamente, permitindo acesso aos serviços do cluster.

&nbsp;

### Criando o cluster

Execute o comando abaixo para criar o cluster:

```bash
kind create cluster --name strigus --config kind.yaml
```

- `--name strigus`: Nomeia o cluster como "strigus".
- `--config kind.yaml`: Utiliza o arquivo de configuração que criamos.

> **Observação:** Após a execução, o **KIND** irá configurar o cluster conforme especificado. Isso pode levar alguns minutos, então tenha paciência que já já terminamos essa parte! 😉

&nbsp;

## Passo 2: Instalando o NGINX Gateway Fabric

Com o cluster criado, o próximo passo é instalar o **NGINX Gateway Fabric**, que servirá como **controlador de tráfego** para suas aplicações.

### Aplicando as CRDs

As **Custom Resource Definitions (CRDs)** estendem a API do Kubernetes, permitindo que o **NGINX Gateway** funcione corretamente.

```bash
kubectl kustomize "https://github.com/nginxinc/nginx-gateway-fabric/config/crd/gateway-api/standard?ref=v1.4.0" | kubectl apply -f -
kubectl apply -f https://raw.githubusercontent.com/nginxinc/nginx-gateway-fabric/v1.4.0/deploy/crds.yaml
```

**O que os comandos fazem:**

- **Kustomize**: Monta os manifestos das **CRDs** diretamente do repositório do **NGINX** no GitHub.
- **kubectl apply**: Aplica as **CRDs** ao cluster.

> **Dica:** Esse passo é essencial para que o Kubernetes reconheça os recursos específicos do **NGINX Gateway Fabric**.

&nbsp;

### Implantando o NGINX Gateway

Agora, vamos implantar o **NGINX Gateway** utilizando o modo **NodePort**:

```bash
kubectl apply -f https://raw.githubusercontent.com/nginxinc/nginx-gateway-fabric/v1.4.0/deploy/nodeport/deploy.yaml
```

Este manifesto configura o **NGINX Gateway** para expor serviços através de **NodePort**, permitindo o acesso externo às portas configuradas.

> **Importante:** Usar o modo **NodePort** facilita o acesso aos serviços do cluster a partir do host local.

&nbsp;

## Passo 3: Configurando o Gateway e o Serviço

Com o **NGINX Gateway Fabric** instalado, vamos configurá-lo para gerenciar o tráfego das nossas aplicações.

### Criando o Gateway

Crie um arquivo chamado `gateway.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: my-gateway
spec:
  gatewayClassName: nginx
  listeners:
    - name: http
      protocol: HTTP
      port: 80
```

**Explicação:**

- **gatewayClassName**: Define que estamos utilizando a classe de gateway do **NGINX**.
- **Listeners**: Configura um listener na porta `80` para tráfego **HTTP**.

Aplique a configuração:

```bash
kubectl apply -f gateway.yaml
```

> **Nota:** Este passo cria o gateway que irá gerenciar as rotas do tráfego HTTP para suas aplicações.

&nbsp;

### Configurando o serviço do Gateway

Para expor o **NGINX Gateway**, crie o arquivo `gateway-service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-gateway
  namespace: nginx-gateway
  labels:
    app.kubernetes.io/name: nginx-gateway
    app.kubernetes.io/instance: nginx-gateway
    app.kubernetes.io/version: "1.4.0"
spec:
  type: NodePort
  selector:
    app.kubernetes.io/name: nginx-gateway
    app.kubernetes.io/instance: nginx-gateway
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 80
      nodePort: 31437
    - name: https
      port: 443
      protocol: TCP
      targetPort: 443
      nodePort: 31438
```

**Explicação:**

- **Namespace**: O serviço está no namespace `nginx-gateway`.
- **Selector**: Garante que o serviço selecione os pods corretos.
- **Portas**: Expondo as portas `80` e `443` através de **NodePort** mapeando para `31437` e `31438`.

Aplique o serviço:

```bash
kubectl apply -f gateway-service.yaml
```

> **Dica:** Utilizar **NodePort** permite que você acesse o gateway através das portas mapeadas no host.

&nbsp;

## Passo 4: Verificando a Instalação

É crucial verificar se tudo está funcionando conforme o esperado.

### Verificando os pods

Execute:

```bash
kubectl get pods -n nginx-gateway
```

Você deve ver algo como:

```
NAME                              READY   STATUS    RESTARTS   AGE
nginx-gateway-xxxxxxxxxx-xxxxx    1/1     Running   0          xxm
```

> **Observação:** O status `Running` indica que o pod está ativo e funcionando corretamente.

&nbsp;

### Testando o acesso ao Gateway

Realize uma requisição `curl` para verificar a resposta do **NGINX Gateway**:

```bash
curl http://localhost:8080
```

Se tudo estiver correto, você receberá uma resposta do **NGINX**.

> **Dica:** Caso não receba resposta, verifique se as portas estão corretamente mapeadas e se os pods estão em execução.

&nbsp;

## Passo 5: Implantando Duas Aplicações e Testando o Roteamento

Agora, vamos implantar duas aplicações simples e configurar o **NGINX Gateway** para rotear o tráfego para elas.

### Implantando as aplicações

#### Criando o Deployment da app1

Crie o arquivo `app1.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
        - name: app1
          image: hashicorp/http-echo
          args:
            - "-text=Giropops Strigus Girus na APP1"
          ports:
            - containerPort: 5678
```

**Explicação:**

- **Replicas**: Define 2 réplicas para alta disponibilidade.
- **Container**: Utiliza a imagem `hashicorp/http-echo` que retorna uma mensagem HTTP simples.

#### Criando o Deployment da app2

Crie o arquivo `app2.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
        - name: app2
          image: hashicorp/http-echo
          args:
            - "-text=Giropops Strigus Girus na APP2"
          ports:
            - containerPort: 5678
```

**Explicação:**

- Semelhante ao deployment da **app1**, mas para a **app2**.

#### Aplicando os Deployments

Execute:

```bash
kubectl apply -f app1.yaml
kubectl apply -f app2.yaml
```

> **Dica:** Após aplicar os deployments, o Kubernetes irá criar os pods correspondentes.

#### Verificando os Pods

```bash
kubectl get pods
```

Você deve ver algo como:

```
NAME                     READY   STATUS    RESTARTS   AGE
app1-xxxxxxxxxx-xxxxx    1/1     Running   0          xxm
app1-xxxxxxxxxx-xxxxx    1/1     Running   0          xxm
app2-xxxxxxxxxx-xxxxx    1/1     Running   0          xxm
app2-xxxxxxxxxx-xxxxx    1/1     Running   0          xxm
```

> **Observação:** Cada aplicação deve ter duas réplicas em execução.

&nbsp;

### Criando os serviços para as aplicações

#### Serviço para a app1

Crie o arquivo `svc1.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app1-service
spec:
  selector:
    app: app1
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5678
```

#### Serviço para a app2

Crie o arquivo `svc2.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app2-service
spec:
  selector:
    app: app2
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5678
```

**Explicação:**

- **Selector**: Conecta o serviço aos pods com as labels correspondentes.
- **Portas**: Expondo a porta `80` para comunicação interna.

#### Aplicando os Serviços

Execute:

```bash
kubectl apply -f svc1.yaml
kubectl apply -f svc2.yaml
```

#### Verificando os Serviços

```bash
kubectl get services
```

Você deve ver:

```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
app1-service    ClusterIP   10.xxx.xxx.xxx  <none>        80/TCP    xxm
app2-service    ClusterIP   10.xxx.xxx.xxx  <none>        80/TCP    xxm
```

> **Nota:** Os serviços devem estar do tipo `ClusterIP`, permitindo comunicação interna dentro do cluster.

&nbsp;

### Configurando o HTTPRoute

Vamos configurar o **HTTPRoute** para que o **NGINX Gateway** saiba para onde direcionar o tráfego com base nas URLs.

Crie o arquivo `http_route.yaml`:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: app1-app2-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/app1"
      backendRefs:
        - name: app1-service
          port: 80
    - matches:
        - path:
            type: PathPrefix
            value: "/app2"
      backendRefs:
        - name: app2-service
          port: 80
```

**Explicação:**

- **HTTPRoute**: Define regras de roteamento para o tráfego.
- **Regras**:
  - Requisições que começam com `/app1` são direcionadas para `app1-service`.
  - Requisições que começam com `/app2` são direcionadas para `app2-service`.

#### Aplicando o HTTPRoute

Execute:

```bash
kubectl apply -f http_route.yaml
```

#### Verificando o HTTPRoute

```bash
kubectl get httproute
```

Você deve ver:

```
NAME               HOSTNAMES   AGE
app1-app2-route    <none>      xxm
```

> **Dica:** O `<none>` em `HOSTNAMES` indica que o roteamento está baseado no caminho da URL.

&nbsp;

### Testando o roteamento

#### Testando a app1

Execute:

```bash
curl http://localhost:8080/app1
```

**Resposta Esperada:**

```
Giropops Strigus Girus na APP1
```

#### Testando a app2

Execute:

```bash
curl http://localhost:8080/app2
```

**Resposta Esperada:**

```
Giropops Strigus Girus na APP2
```

#### Analisando o Resultado

- **Roteamento**: O **NGINX Gateway** está recebendo as requisições na porta `8080` e direcionando para os serviços corretos.
- **Respostas**: Cada aplicação responde com a mensagem configurada, confirmando o roteamento adequado.

&nbsp;

### Verificando os logs

#### Logs do NGINX Gateway

Execute:

```bash
kubectl logs -n nginx-gateway deployment/nginx-gateway
```

Você verá logs das requisições sendo processadas pelo gateway.

#### Logs das Aplicações

Para a **app1**:

```bash
kubectl logs deployment/app1
```

Para a **app2**:

```bash
kubectl logs deployment/app2
```

> **Dica:** Monitorar os logs ajuda a identificar e resolver possíveis problemas rapidamente.

&nbsp;

### Escalando as aplicações

Para testar a escalabilidade, você pode **aumentar o número de réplicas** das aplicações.

#### Escalando a app1 para 4 réplicas

Execute:

```bash
kubectl scale deployment app1 --replicas=4
```

Verifique os pods:

```bash
kubectl get pods -l app=app1
```

Agora, você verá **4 pods** da **app1** em execução, proporcionando maior capacidade de atendimento.

> **Nota:** O Kubernetes gerencia automaticamente o balanceamento de carga entre os pods escalados.

&nbsp;

## Passo 6: Configurações Avançadas de HTTPRoute

Agora que temos o básico funcionando, vamos explorar **configurações avançadas** de **HTTPRoute** para um controle mais granular do tráfego.

### Redirecionamento de Requisições

Vamos configurar o **HTTPRoute** para redirecionar todas as requisições que correspondem ao prefixo `/` para `https://linuxtips.io` com o status code **302**. Isso é útil para direcionar usuários para outra página ou serviço sem interromper a experiência deles.

#### Criando o Arquivo de Redirecionamento

Crie o arquivo `http_redirect_route.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: http-redirect-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/"
      filters:
        - type: RequestRedirect
          requestRedirect:
            scheme: "https"
            hostname: "linuxtips.io"
            statusCode: 302
    - matches:
        - path:
            type: PathPrefix
            value: "/app2"
      backendRefs:
        - name: app2-service
          port: 80
```

**Explicação:**

- **Primeira Regra**: Redireciona todas as requisições que começam com `/` para `https://linuxtips.io` com status **302**.
- **Segunda Regra**: Roteia requisições que começam com `/app2` para o serviço `app2-service`.

&nbsp;

#### Aplicando a Configuração

Execute o comando abaixo para aplicar a configuração:

```bash
kubectl apply -f http_redirect_route.yaml
```

> **Dica:** Sempre verifique se o arquivo YAML está correto antes de aplicar para evitar erros de configuração.

&nbsp;

#### Testando o Redirecionamento

Faça uma requisição para o caminho raiz `/` para verificar o redirecionamento:

```bash
curl -v http://localhost:8080/
```

**Resposta Esperada:**

A requisição será redirecionada para `https://linuxtips.io` com o status code **302**.

> **Nota:** O status **302** indica um redirecionamento temporário. Se desejar um redirecionamento permanente, utilize o status **301**.

&nbsp;

### Roteamento com Balanceamento de Carga Ponderado

Neste exemplo, configuramos o **HTTPRoute** para distribuir o tráfego entre duas versões de um serviço com **pesos diferentes**, implementando um **balanceamento de carga ponderado**. Isso é útil para implementar testes **canary** ou distribuir tráfego de forma diferenciada.

#### Criando o Arquivo de Balanceamento Ponderado

Crie o arquivo `weighted_http_route.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: weighted-http-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/api"
      backendRefs:
        - name: api-service
          port: 80
          weight: 80
        - name: api-service-canary
          port: 80
          weight: 20
```

**Explicação:**

- **BackendRefs com Weights**: O tráfego é distribuído **80%** para `api-service` e **20%** para `api-service-canary`, permitindo testes canary.

&nbsp;

#### Aplicando a Configuração

Execute o comando abaixo para aplicar a configuração:

```bash
kubectl apply -f weighted_http_route.yaml
```

&nbsp;

#### Testando o Balanceamento de Carga

Execute múltiplas requisições para observar a distribuição do tráfego:

```bash
for i in {1..10}; do curl -s http://localhost:8080/api; done
```

**Resposta Esperada:**

A maioria das respostas virá de `api-service`, com aproximadamente **20%** das requisições sendo atendidas por `api-service-canary`.

> **Dica:** Utilize ferramentas como **Apache JMeter** ou **hey** para realizar testes de carga mais robustos.

&nbsp;

### Roteamento Baseado em Parâmetros de Consulta

Vamos configurar o **HTTPRoute** para direcionar o tráfego para diferentes serviços com base no parâmetro de consulta `version` presente na URL. Isso permite versões de API ou funcionalidades específicas.

#### Criando o Arquivo de Roteamento por Parâmetros

Crie o arquivo `query_param_route.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: query-param-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/api"
          queryParams:
            - name: version
              exact: "v1"
      backendRefs:
        - name: api-v1-service
          port: 80
    - matches:
        - path:
            type: PathPrefix
            value: "/api"
          queryParams:
            - name: version
              exact: "v2"
      backendRefs:
        - name: api-v2-service
          port: 80
```

**Explicação:**

- **Primeira Regra**: Direciona requisições com `version=v1` para `api-v1-service`.
- **Segunda Regra**: Direciona requisições com `version=v2` para `api-v2-service`.

&nbsp;

#### Aplicando a Configuração

Execute o comando abaixo para aplicar a configuração:

```bash
kubectl apply -f query_param_route.yaml
```

&nbsp;

#### Testando o Roteamento por Parâmetros

Para a versão v1:

```bash
curl http://localhost:8080/api?version=v1
```

Para a versão v2:

```bash
curl http://localhost:8080/api?version=v2
```

**Respostas Esperadas:**

- **Requisições com `version=v1`** retornam respostas do `api-v1-service`.
- **Requisições com `version=v2`** retornam respostas do `api-v2-service`.

> **Nota:** As respostas podem variar dependendo da implementação dos serviços `api-v1-service` e `api-v2-service`.

&nbsp;

### Implementando Rate Limiting

Implementar **Rate Limiting** é essencial para proteger suas aplicações contra picos de tráfego e ataques maliciosos. Isso ajuda a garantir a disponibilidade e estabilidade dos serviços.

#### Criando o Arquivo de Rate Limiting

Crie o arquivo `rate_limited_route.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: rate-limited-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/api"
      filters:
        - type: RequestRateLimit
          requestRateLimit:
            rateLimit:
              requestsPerUnit: 100
              unit: Minute
      backendRefs:
        - name: api-service
          port: 80
```

**Explicação:**

- **Rate Limiting**: Limita a **100 requisições por minuto** para a rota `/api`.

&nbsp;

#### Aplicando a Configuração

Execute o comando abaixo para aplicar a configuração:

```bash
kubectl apply -f rate_limited_route.yaml
```

&nbsp;

#### Testando o Rate Limiting

Execute um script para enviar mais de 100 requisições e observar o comportamento:

```bash
for i in {1..105}; do
  curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080/api &
done
wait
```

**Resultados Esperados:**

- **100 Requisições**: Retornam código **200**.
- **5 Requisições Adicionais**: Retornam código **429** ("Too Many Requests").

> **Dica:** Ajuste os limites conforme a necessidade do seu ambiente e perfil de tráfego.

&nbsp;

#### Verificando os Logs

Para confirmar que o **Rate Limiting** está funcionando, verifique os logs do **NGINX Gateway**:

```bash
kubectl logs -n nginx-gateway deployment/nginx-gateway
```

Procure por entradas indicando que o limite foi excedido, por exemplo:

```
[notice] 1234#1234: *567 rate limiting: client 192.168.1.100 exceeded rate limit: 100 requests per minute
```

> **Nota:** Logs detalhados ajudam na identificação e resolução rápida de problemas relacionados ao rate limiting.

&nbsp;

### Implementando Reescrita de URL (URL Rewrite)

Reescrever URLs é útil para migrar caminhos de API sem interromper o tráfego existente, melhorando a experiência do usuário e a manutenção das aplicações.

#### Criando o Arquivo de Reescrita de URL

Crie o arquivo `rewrite_http_route.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: rewrite-http-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/old-path"
      filters:
        - type: URLRewrite
          urlRewrite:
            path:
              replacePrefixMatch: "/new-path"
      backendRefs:
        - name: api-service
          port: 80
```

**Explicação:**

- **Reescrita de URL**: Substitui o prefixo `/old-path` por `/new-path` antes de encaminhar para o backend.

&nbsp;

#### Aplicando a Configuração

Execute o comando abaixo para aplicar a configuração:

```bash
kubectl apply -f rewrite_http_route.yaml
```

&nbsp;

#### Testando a Reescrita de URL

Faça uma requisição para `/old-path` para verificar a reescrita:

```bash
curl -v http://localhost:8080/old-path
```

**Resposta Esperada:**

A requisição será reescrita para `/new-path` e direcionada para o `api-service`, retornando a resposta apropriada.

> **Nota:** Verifique se o serviço `api-service` está preparado para responder às requisições no novo caminho `/new-path`.

&nbsp;

### Redirecionamento de Requisições e Roteamento para Aplicações Internas

Vamos combinar **Redirecionamento de Requisições** com **Roteamento Interno** para demonstrar uma configuração mais complexa e robusta.

#### Criando o Arquivo de Roteamento Combinado

Crie o arquivo `app1-app2-route.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: app1-app2-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/"
      filters:
        - type: RequestRedirect
          requestRedirect:
            scheme: "https"
            hostname: "linuxtips.io"
            statusCode: 302
    - matches:
        - path:
            type: PathPrefix
            value: "/app2"
      backendRefs:
        - name: app2-service
          port: 80
```

**Explicação:**

- **Primeira Regra**: Redireciona todas as requisições para `https://linuxtips.io`.
- **Segunda Regra**: Roteia requisições que começam com `/app2` para `app2-service`.

&nbsp;

#### Aplicando a Configuração

Execute o comando abaixo para aplicar a configuração:

```bash
kubectl apply -f app1-app2-route.yaml
```

&nbsp;

#### Testando o Redirecionamento e Roteamento

1. **Redirecionamento para o LINUXtips**:

   Execute a seguinte requisição para o caminho raiz `/`:

   ```bash
   curl -v http://localhost:8080/
   ```

   **Resposta Esperada:**

   A requisição será redirecionada para `https://linuxtips.io` com status **302**.

2. **Roteamento para app2**:

   Execute a seguinte requisição para `/app2`:

   ```bash
   curl -v http://localhost:8080/app2
   ```

   **Resposta Esperada:**

   Você receberá a resposta da aplicação **app2**, por exemplo:

   ```
   Giropops Strigus Girus na APP2
   ```

> **Dica:** Teste cada configuração individualmente para garantir que todas as regras estão funcionando conforme o esperado antes de combiná-las.

&nbsp;

### Tabela de Resumo das Configurações Avançadas

| Configuração                      | Descrição                                                     | Arquivo YAML                  | Comando de Aplicação                 |
|-----------------------------------|---------------------------------------------------------------|-------------------------------|--------------------------------------|
| Redirecionamento de Requisições   | Redireciona `/` para `https://linuxtips.io` com status **302** | `http_redirect_route.yaml`    | `kubectl apply -f http_redirect_route.yaml` |
| Balanceamento de Carga Ponderado  | Distribui tráfego 80% para `api-service` e 20% para `api-service-canary` | `weighted_http_route.yaml`    | `kubectl apply -f weighted_http_route.yaml` |
| Roteamento por Parâmetros         | Direciona com base no parâmetro `version` (`v1` ou `v2`)     | `query_param_route.yaml`      | `kubectl apply -f query_param_route.yaml` |
| Rate Limiting                     | Limita a 100 requisições por minuto para `/api`              | `rate_limited_route.yaml`     | `kubectl apply -f rate_limited_route.yaml` |
| Reescrita de URL                  | Substitui `/old-path` por `/new-path`                         | `rewrite_http_route.yaml`     | `kubectl apply -f rewrite_http_route.yaml` |
| Redirecionamento + Roteamento     | Combina redirecionamento para `/` e roteamento para `/app2`  | `app1-app2-route.yaml`        | `kubectl apply -f app1-app2-route.yaml` |

> **Nota:** Manter um **resumo** das configurações ajuda na organização e manutenção das regras aplicadas no cluster.

&nbsp;

**Dica do Jefim:** Lembre-se de sempre testar suas configurações em um ambiente de desenvolvimento antes de aplicar em produção. Isso ajuda a evitar interrupções inesperadas e garante que tudo funcione conforme o planejado. 😊

&nbsp;

### Verificando os Logs

Verifique os logs do **NGINX Gateway** para confirmar as ações realizadas nas configurações avançadas:

```bash
kubectl logs -n nginx-gateway deployment/nginx-gateway
```

Procure por entradas como:

```
[notice] 1234#1234: *567 RequestRedirect: / -> https://linuxtips.io com status 302
[info] 1234#1234: *568 forwarded request to backend app2-service:80
```

> **Dica:** Monitorar os logs é essencial para garantir que as regras de roteamento e redirecionamento estão funcionando conforme o esperado. Utilize ferramentas como `grep` para filtrar informações específicas nos logs, por exemplo:

```bash
kubectl logs -n nginx-gateway deployment/nginx-gateway | grep "RequestRedirect"
```

&nbsp;

## Passo 7: Configurando Roteamento com TLS

Neste passo, vamos adicionar uma camada de segurança ao nosso cluster Kubernetes configurando rotas **TLS** (Transport Layer Security) utilizando o **NGINX Gateway Fabric**. Isso permitirá que nossas aplicações se comuniquem de forma segura através de conexões **HTTPS**.

### Criando o Deployment da Secure Service

Primeiramente, criaremos uma aplicação segura que responderá às requisições **HTTPS**.

#### Criando o Arquivo `secure-service.yaml`

Crie um arquivo chamado `secure-service.yaml` com o seguinte conteúdo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-service
  labels:
    app: secure-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: secure-service
  template:
    metadata:
      labels:
        app: secure-service
    spec:
      containers:
        - name: secure-service
          image: hashicorp/http-echo
          args:
            - "-text=Giropops Strigus Girus no Secure Service"
          ports:
            - containerPort: 5678
```

**Explicação:**

- **Deployment**: Define a aplicação `secure-service` com **2 réplicas** para garantir alta disponibilidade.
- **Container**: Utiliza a imagem `hashicorp/http-echo` que retorna uma mensagem HTTP simples, ideal para testes rápidos.

&nbsp;

#### Aplicando o Deployment

Execute o comando abaixo para criar o Deployment:

```bash
kubectl apply -f secure-service.yaml
```

> **Observação:** Após aplicar o Deployment, o Kubernetes iniciará os pods correspondentes. Você pode verificar o status com:

```bash
kubectl get pods -l app=secure-service
```

&nbsp;

### Configurando o Gateway com Suporte a TLS

Agora, vamos atualizar o **Gateway** para suportar conexões **HTTPS** adicionando um listener TLS.

#### Criando o Arquivo `gateway-tls.yaml`

Crie um arquivo chamado `gateway-tls.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: my-gateway
spec:
  gatewayClassName: nginx
  listeners:
    - name: http
      protocol: HTTP
      port: 80
    - name: https
      protocol: HTTPS
      port: 443
      tls:
        mode: Terminate
        certificateRefs:
          - name: tls-secret
```

**Explicação:**

- **Listeners**:
  - **HTTP Listener**: Mantém o listener existente na porta `80` para tráfego **HTTP**.
  - **HTTPS Listener**: Adiciona um listener na porta `443` para tráfego **HTTPS**, referenciando o segredo `tls-secret` que contém os certificados TLS.

&nbsp;

#### Aplicando a Configuração do Gateway

Execute o comando abaixo para aplicar as alterações no Gateway:

```bash
kubectl apply -f gateway-tls.yaml
```

> **Dica:** Após aplicar, verifique se o Gateway foi atualizado corretamente:

```bash
kubectl get gateway my-gateway -o yaml
```

&nbsp;

### Criando o Serviço para a Secure Service

Para expor a aplicação `secure-service` internamente no cluster, criaremos um **Service**.

#### Criando o Arquivo `secure-service-svc.yaml`

Crie um arquivo chamado `secure-service-svc.yaml` com o seguinte conteúdo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: secure-service
spec:
  selector:
    app: secure-service
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5678
```

**Explicação:**

- **Service**: Exponibiliza a aplicação `secure-service` na porta `80` dentro do cluster, permitindo que outras aplicações e o **NGINX Gateway** se comuniquem com ela.

&nbsp;

#### Aplicando o Serviço

Execute o comando abaixo para criar o Serviço:

```bash
kubectl apply -f secure-service-svc.yaml
```

> **Observação:** Verifique se o Serviço foi criado corretamente:

```bash
kubectl get services
```

&nbsp;

### Configurando o Secret com o Certificado TLS

Para habilitar o **HTTPS**, precisamos fornecer certificados TLS ao **NGINX Gateway**. Neste exemplo, utilizaremos certificados autoassinados para fins de desenvolvimento.

#### Gerando Certificados TLS Autoassinados

Vamos gerar uma chave privada e um certificado TLS autoassinado usando o **OpenSSL**. Execute os seguintes comandos no seu terminal:

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout tls.key -out tls.crt \
  -subj "/CN=localhost"
```

**Explicação:**

- Gera uma chave RSA de 2048 bits (`tls.key`) e um certificado autoassinado (`tls.crt`) válido por 365 dias, com o **Common Name (CN)** definido como `localhost`.

&nbsp;

#### Criando o Secret TLS no Kubernetes

Com a chave e o certificado gerados, agora vamos criar um **Secret** do tipo `tls` no Kubernetes para armazená-los.

Execute o comando:

```bash
kubectl create secret tls tls-secret --cert=tls.crt --key=tls.key
```

> **Dica:** Verifique se o Secret foi criado corretamente:

```bash
kubectl get secrets tls-secret
```

&nbsp;

### Criando o HTTPRoute para a Rota Segura

Vamos criar um **HTTPRoute** que utiliza o protocolo HTTPS para rotear o tráfego.

#### Criando o Arquivo `https_route.yaml`

Crie o arquivo `https_route.yaml` com o seguinte conteúdo:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: secure-app-route
spec:
  parentRefs:
    - name: my-gateway
      sectionName: https
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/secure-app"
      backendRefs:
        - name: secure-service
          port: 80
```

**Explicação:**

- **parentRefs**: Referencia o listener `https` definido no **Gateway**.
- **sectionName**: Especifica que esta rota é para o listener `https`.
- **matches**: Define que as requisições com o prefixo `/secure-app` serão roteadas.
- **backendRefs**: Direciona para o serviço `secure-service`.

#### Aplicando a Configuração

Execute o comando:

```bash
kubectl apply -f https_route.yaml
```

&nbsp;

### Verificando a Instalação

#### Verificando os Pods

Execute:

```bash
kubectl get pods -l app=secure-service
```

Você deve ver algo como:

```
NAME                              READY   STATUS    RESTARTS   AGE
secure-service-xxxxxxxxxx-xxxxx   1/1     Running   0          xxm
```

&nbsp;

#### Verificando os Serviços

Execute:

```bash
kubectl get services
```

Você deve ver:

```
NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
secure-service    ClusterIP   10.xxx.xxx.xxx  <none>        80/TCP    xxm
```

&nbsp;

### Testando o Roteamento com TLS

#### Testando a Conexão HTTPS

Execute:

```bash
curl -k https://localhost:8443/secure-app
```

**Resposta Esperada:**

```
Giropops Strigus Girus no Secure Service
```

> **Nota:** A opção `-k` permite ignorar a verificação do certificado, já que estamos usando um certificado autoassinado.

&nbsp;

### Entendendo em Detalhes

#### Fluxo da Requisição com TLS

1. **Cliente faz a requisição**: `curl -k https://localhost:8443/secure-app`.
2. **NGINX Gateway recebe a requisição**: Conexão segura estabelecida através da porta `8443`.
3. **TLS Handshake**: Estabelece uma conexão **HTTPS** utilizando os certificados fornecidos.
4. **HTTPRoute processa a requisição**: Verifica o caminho da URL.
5. **Roteamento para o Serviço**: Direciona para `secure-service` na porta `80`.
6. **Serviço direciona para os Pods**: Encaminha para os pods com o label `app: secure-service`.
7. **Pod responde**: Retorna a mensagem `Giropops Strigus Girus no Secure Service`.
8. **Resposta é enviada ao cliente**: O cliente recebe a resposta através da conexão segura.

&nbsp;

### Verificando os Logs

#### Logs do NGINX Gateway

Execute:

```bash
kubectl logs -n nginx-gateway deployment/nginx-gateway
```

Procure por entradas relacionadas ao TLS e ao roteamento da `secure-service`.

#### Logs da Secure Service

Execute:

```bash
kubectl logs deployment/secure-service
```

Verifique se as requisições estão chegando corretamente à aplicação.

&nbsp;

## Resumo da Brincadeira toda 

Parabéns! \o/ Você conseguiu configurar **roteamento avançado** e **segurança TLS** no seu cluster Kubernetes com **NGINX Gateway Fabric** utilizando o **KIND**. Neste guia, abordamos desde a criação do cluster até configurações avançadas de roteamento e segurança, garantindo que suas aplicações estejam prontas para operar de forma eficiente e segura.

**Pontos-chave abordados:**

- **Configuração Inicial**: Instalando Docker, kubectl e KIND.
- **Implantação do NGINX Gateway Fabric**: Configurando CRDs e serviços.
- **Desenvolvimento e Exposição de Aplicações**: Deployments e Services.
- **Configuração de HTTPRoute**: Roteamento baseado em caminhos, parâmetros e balanceamento de carga.
- **Implementação de Segurança**: Configurando rotas TLS para comunicação segura.
- **Testes e Verificações**: Validando o funcionamento do cluster, das aplicações e das configurações avançadas.
- **Escalabilidade e Monitoramento**: Ajustando réplicas e monitorando logs para garantir alta disponibilidade e desempenho.

> **Dica Final:** Sempre mantenha seus componentes atualizados e monitore os logs para garantir o bom funcionamento do seu ambiente Kubernetes. Explore mais sobre **Gateway API**, **NGINX**, e práticas de **segurança** para aprimorar ainda mais suas habilidades e garantir ambientes robustos e seguros.

&nbsp;

## Recursos Adicionais

- [Documentação Oficial do Kubernetes](https://kubernetes.io/docs/home/)
- [Documentação do KIND](https://kind.sigs.k8s.io/docs/user/quick-start/)
- [Documentação do NGINX Gateway Fabric](https://github.com/nginxinc/nginx-gateway-fabric)
- [Vídeos da LINUXtips sobre Kubernetes](https://www.youtube.com/c/LINUXtips)
- [Cert-Manager para Gerenciamento de Certificados TLS](https://cert-manager.io/)
- [OpenSSL Documentation](https://www.openssl.org/docs/)
- [Gateway API Documentation](https://gateway-api.sigs.k8s.io/)
- [Ferramenta hey para Testes de Carga HTTP](https://github.com/rakyll/hey)
- [Apache JMeter](https://jmeter.apache.org/)

---

**Escrito por Jeferson Fernando para a LINUXtips**
