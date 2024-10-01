# Instrumentando Aplicações em Kubernetes com OpenTelemetry

## Introdução

Este repositório contém os arquivos e instruções necessárias para instrumentar uma aplicação Spring Boot em Kubernetes utilizando OpenTelemetry. Através deste guia, você aprenderá a coletar e analisar dados de telemetria, configurando um ambiente robusto de observabilidade.

## O Que é Telemetria?

Telemetria é o processo de coleta, transmissão e análise de dados de diferentes parâmetros de um sistema, em tempo real. No contexto de TI, isso inclui métricas, traces e logs, que são cruciais para monitorar e diagnosticar aplicações.

## Os Três Pilares da Observabilidade

1. **Métricas**: Dados quantitativos que mostram o desempenho do sistema ao longo do tempo.
2. **Traces**: Rastream o caminho de uma requisição ao longo de diferentes serviços e componentes.
3. **Logs**: Registro detalhado de eventos que ocorreram dentro do sistema.

## Onde o OpenTelemetry se Encaixa?

OpenTelemetry é uma solução de código aberto que fornece uma estrutura unificada para coletar métricas, traces e logs de aplicações distribuídas, facilitando a instrumentação automática das aplicações.

## Caso de Uso: Instrumentando uma Aplicação Spring em Kubernetes

Neste caso de uso iremos instrumentar uma aplicação em java exportando trace para o collector do open telemetry, onde o collector processa os traces e exporta para o Grafana Tempo, cujo pode ser visualizado pelo grafana.

![Arquitetura do caso de uso](./images/arch.png)

Este repositório contém os seguintes arquivos:

- **collector.yaml**: Configuração do OpenTelemetry Collector para coletar traces.
- **instrumentation.yaml**: Configuração da instrumentação do OpenTelemetry para a aplicação.
- **pod-sending-span.yaml**: Configuração do pod da aplicação Java que envia spans.
- **grafana.yaml**: Configuração do Grafana para visualizar os rastros coletados.

- **minio.yaml**: Configuração do Mini-O para servir como armazenamento de objetos.
- **tempo-operador.yaml**: Configuração do Operator do Grafana Tempo.
- **tempo-stack.yaml**: Configuração do Tempo sendo realizado o deploy com a stack em micro serviços.


### Requisitos

- Um cluster Kubernetes rodando (utilizaremos Minikube).
- Cert-manager instalado no cluster Kubernetes.
- `kubectl` instalado e configurado.

### Configurando o Minikube

1. Instale o Minikube:
    ```bash
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    sudo install minikube-linux-amd64 /usr/local/bin/minikube
    ```

2. Inicie o Minikube:
    ```bash
    minikube start
    ```

### Instalando o Cert-Manager

1. Instale o Cert-Manager:
    ```bash
    kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.7.1/cert-manager.yaml
    ```

2. Verifique se os pods do Cert-Manager estão em execução:
    ```bash
    kubectl get pods --namespace cert-manager
    ```
![Cert Manager running example](./images/check-cert-manager-running.png)

### Instalando o OpenTelemetry Operator

1. Aplique o OpenTelemetry Operator no seu cluster:
    ```bash
    kubectl apply -f https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml
    ```

### Configurando o OpenTelemetry Collector

1. Aplique a configuração do Collector:
    ```bash
    kubectl apply -f collector.yaml
    ```

### Configurando a Instrumentação

1. Aplique a configuração de Instrumentação:
    ```bash
    kubectl apply -f instrumentation.yaml
    ```
### Implantando o miniO

1. Aplique o arquivo de instalação do miniO:
    ```bash
    kubectl apply -f minio.yaml
    ```
### Instalando o Tempo Operator

1. Aplique a instalação do operator
    ```bash
    kubectl apply -f tempo-operator.yaml
    ```

### Configurando a Stack do Grafana Tempo

1. Aplique o arquivo de configuração da stack em micro serviços do grafana tempo:
    ```bash
    kubectl apply -f tempo-stack.yaml
    ```

### Implantando a Aplicação Java

1. Aplique a configuração do Pod:
    ```bash
    kubectl apply -f pod-sending-span.yaml
    ```

### Realizar Testes de Verificação

1. Realize uma requisição à aplicação Java:
    ```bash
    kubectl exec -it myapp -- curl "http://localhost:8080"
    ```
    
### Configurando o Grafana para Visualização de Rastros

1. Aplique a configuração do Grafana:
    ```bash
    kubectl apply -f grafana.yaml
    ```

2. Acesse o Grafana:
    ```bash
    minikube service grafana --url
    ```

3. Acesse o rastro no grafana:
    - No painel do Grafana, vá para **Explore**.
    - No campo "Query Type", escolha a opção: `Search`.
    - Clique em **Run Query**.
    - Clique em algum **TraceID**.



![Captura de tela com o exemplo de rastro](./images/trace-example.png)


## Conclusão

Este repositório e guia fornecem uma base sólida para a instrumentação de aplicações em Kubernetes utilizando OpenTelemetry. Com estas configurações, você terá um ambiente de observabilidade robusto que facilita a identificação e resolução de problemas de desempenho e comportamento em suas aplicações.

Se tiver dúvidas ou precisar de assistência, sinta-se à vontade para abrir uma issue.
