---
title: Implantar uma instância prometheus autônoma em um cluster Azure Red Hat OpenShift | Microsoft Docs
description: Crie uma instância Prometheus em um cluster Azure Red Hat OpenShift para monitorar as métricas do seu aplicativo.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometeu, aro, openshift, métricas, chapéu vermelho
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69875132"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Implantar uma instância prometheus autônoma em um cluster Azure Red Hat OpenShift

Este artigo descreve como configurar uma instância prometheus autônoma que usa a descoberta de serviços em um cluster Azure Red Hat OpenShift.

> [!NOTE]
> O acesso do administrador do cliente ao cluster Azure Red Hat OpenShift não é necessário.

Configuração do destino:

- Um projeto (prometheus-project), que contém Prometeu e Gerente de Alerta.
- Dois projetos (app-project1 e app-project2), que contêm os aplicativos para monitorar.

Você vai preparar alguns arquivos de configuração Prometheus localmente. Crie uma nova pasta para armazená-los. Os arquivos de config são armazenados no cluster como segredos, caso tokens secretos sejam adicionados mais tarde ao cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Faça login no cluster usando a ferramenta OC

1. Abra um navegador web e, em seguida,https://openshiftvá para o console web do seu cluster ( .* id aleatório*. *região*(azmosa.io).
2. Entre com suas credenciais do Azure.
3. Selecione seu nome de usuário no canto superior direito e, em seguida, selecione **Comando de login de cópia .**
4. Cole seu nome de usuário no terminal que você usará.

> [!NOTE]
> Para ver se você está conectado ao cluster `oc whoami -c` correto, execute o comando.

## <a name="prepare-the-projects"></a>Preparar os projetos

Para criar os projetos, execute os seguintes comandos:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Você pode usar `-n` `--namespace` o parâmetro ou selecionar um projeto `oc project` ativo executando o comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Prepare o arquivo de configuração Prometheus
Crie um arquivo prometheus.yml inserindo o seguinte conteúdo:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Crie um segredo chamado Prom inserindo a seguinte configuração:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

O arquivo prometheus.yml é um arquivo de configuração prometheus básico. Ele define os intervalos e configura a detecção automática em três projetos (prometheus-project, app-project1, app-project2). No arquivo de configuração anterior, os pontos finais descobertos automaticamente são raspados em HTTP sem autenticação.

Para obter mais informações sobre os pontos finais de raspagem, consulte [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Prepare o arquivo de configuração do Alertmanager
Crie um arquivo alertmanager.yml inserindo o seguinte conteúdo:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Crie um segredo chamado Prom-Alerts inserindo a seguinte configuração:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml é o arquivo de configuração do Alert Manager.

> [!NOTE]
> Para verificar as duas etapas anteriores, execute o `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Inicie o Prometheus e o Alertmanager
Vá para [o repositório openshift/origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e baixe o modelo [prometheus-standalone.yaml.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) Aplique o modelo ao projeto prometheus inserindo a seguinte configuração:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
O arquivo prometheus-standalone.yaml é um modelo OpenShift. Ele criará uma instância Prometheus com proxy oauth na frente e uma instância do Gerenciador de Alerta, também protegida com proxy oauth. Neste modelo, o oauth-proxy é configurado para permitir que qualquer usuário que possa "obter" o espaço de nome do projeto prometheus (veja o `-openshift-sar` sinalizador).

> [!NOTE]
> Para verificar se o prom StatefulSet tem réplicas de `oc get statefulset -n prometheus-project` número IGUAIS E ATUAIS, execute o comando. Para verificar todos os recursos `oc get all -n prometheus-project` do projeto, execute o comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Adicionar permissões para permitir a descoberta de serviços

Crie um arquivo prometheus-sdrole.yml inserindo o seguinte conteúdo:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Para aplicar o modelo a todos os projetos dos quais deseja permitir a descoberta de serviços, execute os seguintes comandos:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Para verificar se a Função e o RoleBinding foram criados corretamente, execute os `oc get role` comandos e. `oc get rolebinding`

## <a name="optional-deploy-example-application"></a>Opcional: Implantar aplicativo de exemplo

Tudo está funcionando, mas não há fontes métricas. Vá para a URL prometheus (https://prom-prometheus-project.apps.* id aleatório*. *região*(azmosa.io/). Você pode encontrá-lo usando o seguinte comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Lembre-se de adicionar o prefixo https:// ao início do nome do host.

A página **Status > Service Discovery** mostrará 0/0 alvos ativos.

Para implantar um aplicativo de exemplo, que expõe métricas básicas do Python o ponto final /métricas, execute os seguintes comandos:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Os novos aplicativos devem aparecer como alvos válidos na página Do Sumidé.

Para obter mais detalhes, selecione **'Metas de status ''Metas de** **status'** > 

> [!NOTE]
> Para cada alvo raspado com sucesso, O Prometeu adiciona um ponto de dados na métrica up. Selecione **Prometeu** no canto superior esquerdo, **insira** como a expressão e selecione **Execute**.

## <a name="next-steps"></a>Próximas etapas

Você pode adicionar instrumentação Prometheus personalizada às suas aplicações. A biblioteca Prometheus Client, que simplifica a preparação das métricas prometheus, está pronta para diferentes linguagens de programação.

Para obter mais informações, consulte as seguintes bibliotecas do GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Ir](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
