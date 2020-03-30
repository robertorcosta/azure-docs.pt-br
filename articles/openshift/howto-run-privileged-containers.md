---
title: Execute contêineres privilegiados em um cluster Azure Red Hat OpenShift | Microsoft Docs
description: Execute contêineres privilegiados para monitorar a segurança e a conformidade.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, chapéu vermelho
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271370"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Executar contêineres privilegiados em um cluster do Red Hat OpenShift no Azure

Você não pode executar contêineres privilegiados arbitrários em clusters Azure Red Hat OpenShift.
Duas soluções de monitoramento de segurança e conformidade são permitidas para serem executadas em clusters ARO.
Este documento descreve as diferenças da documentação genérica de implantação do OpenShift dos fornecedores de produtos de segurança.


Leia estas instruções antes de seguir as instruções do fornecedor.
Os títulos da seção nas etapas específicas do produto abaixo referem-se diretamente aos títulos de seção na documentação dos fornecedores.

## <a name="before-you-begin"></a>Antes de começar

A documentação da maioria dos produtos de segurança pressupõe que você tenha privilégios de administrador de cluster.
Os administradores do cliente não têm todos os privilégios no Azure Red Hat OpenShift. As permissões necessárias para modificar recursos em todo o cluster são limitadas.

Primeiro, certifique-se de que o usuário esteja logado no `oc get scc`cluster como administrador do cliente, executando . Todos os usuários que são membros do grupo administrador de clientes têm permissões para visualizar as Restrições de Contexto de Segurança (SCCs) no cluster.

Em seguida, `oc` certifique-se `3.11.154`de que a versão binária é .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Etapas específicas do produto para a Segurança Aqua
As instruções básicas que serão modificadas podem ser encontradas na [documentação](https://docs.aquasec.com/docs/openshift-red-hat)de implantação do Aqua Security . As etapas aqui serão executadas em conjunto com a documentação de implantação do Aqua.

O primeiro passo é anotar os SCCs necessários que serão atualizados. Essas anotações impedem que o Sync Pod do cluster reverta quaisquer alterações nesses SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Passo 1: Preparar pré-requisitos
Lembre-se de fazer login no cluster como administrador de clientes ARO em vez da função de administrador de cluster.

Crie o projeto e a conta de serviço.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Em vez de atribuir a função de leitor de cluster, atribua a função de cluster de administração do cliente à conta aquática com o seguinte comando.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Continue seguindo as instruções restantes no Passo 1.  Essas instruções descrevem a criação do segredo para o registro do Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Passo 2: Implantar o Aqua Server, Banco de Dados e Gateway
Siga as etapas previstas na documentação do Aqua para a instalação do aqua-console.yaml.

Modifique o `aqua-console.yaml`fornecido .  Remova os dois principais `kind: ClusterRole` `kind: ClusterRoleBinding`objetos rotulados e .  Esses recursos não serão criados, pois o admin do cliente `ClusterRole` não `ClusterRoleBinding` tem permissão neste momento para modificar e objetos.

A segunda modificação será `kind: Route` na `aqua-console.yaml`parte do . Substitua o seguinte inhame `kind: Route` pelo `aqua-console.yaml` objeto no arquivo.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Siga as instruções restantes.

### <a name="step-3-login-to-the-aqua-server"></a>Passo 3: Faça login no Aqua Server
Esta seção não foi modificada de forma alguma.  Siga a documentação do Aqua.

Use o seguinte comando para obter o endereço do Aqua Console.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Passo 4: Implantar executores aquáticos
Defina os seguintes campos ao implantar executores:

| Campo          | Valor         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | aqua-conta  |
| Project        | aqua-segurança |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Etapas específicas do produto para Prisma Cloud / Twistlock

As instruções básicas que vamos modificar podem ser encontradas na [documentação](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html) de implantação do Prisma Cloud

Comece instalando `twistcli` a ferramenta conforme descrito nas seções "Instalar Prisma Cloud" e "Baixar o software Prisma Cloud".

Crie um novo projeto OpenShift
```
oc new-project twistlock
```

Pule a seção opcional "Empurre as imagens do Prisma Cloud para um registro privado". Não funcionará no Azure Red Hat Openshift. Use o registro on-line em vez disso.

Você pode seguir a documentação oficial enquanto aplica as correções descritas abaixo.
Comece com a seção "Instalar console".

### <a name="install-console"></a>Instalar console

Durante `oc create -f twistlock_console.yaml` a Etapa 2, você terá um erro ao criar o namespace.
Você pode ignorá-lo com segurança, o namespace `oc new-project` foi criado anteriormente com o comando.

Use `azure-disk` para o tipo de armazenamento.

### <a name="create-an-external-route-to-console"></a>Crie uma rota externa para console

Você pode seguir a documentação ou as instruções abaixo se preferir o comando oc.
Copie a seguinte definição de rota para um arquivo chamado twistlock_route.yaml em seu computador
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
em seguida, executar:
```
oc create -f twistlock_route.yaml
```

Você pode obter a URL atribuída ao console Twistlock com este comando:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Configurar console

Siga a documentação do Twistlock.

### <a name="install-defender"></a>Instalar o Defender

Durante `oc create -f defender.yaml` a Etapa 2, você obterá erros ao criar a função de cluster e a vinculação de função de cluster.
Você pode ignorá-las.

Os defensores serão implantados apenas em nós computacionais. Você não tem que limitá-los com um seletor de nó.
