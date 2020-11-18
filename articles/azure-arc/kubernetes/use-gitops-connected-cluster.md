---
title: Implantar configurações usando o GitOps em um cluster kubernetes habilitado para Arc (visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usar o GitOps para configurar um cluster habilitado para Azure Arc (versão prévia)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Serviço de Kubernetes do Azure, contêineres
ms.openlocfilehash: ce6c754c308d2979db9b1b8eb36e7858e8a91c3c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659787"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Implantar configurações usando o GitOps em um cluster kubernetes habilitado para Arc (visualização)

GitOps é a prática da declaração do estado desejado da configuração do kubernetes (implantações, namespaces e assim por diante) em um repositório git seguido por uma implantação baseada em pesquisa e recepção dessas configurações no cluster usando um operador. Este documento aborda a configuração de fluxos de trabalho em clusters kubernetes habilitados para Arc do Azure.

A conexão entre o cluster e um ou mais repositórios Git é controlada no Azure Resource Manager como um recurso de extensão de `sourceControlConfiguration`. As propriedades de recurso de `sourceControlConfiguration` representam onde e como os recursos de Kubernetes devem fluir do Git para o cluster. Os `sourceControlConfiguration` dados são armazenados criptografados em repouso em um banco de dados Azure Cosmos DB para garantir a confidencialidade dos dados.

A `config-agent` execução no seu cluster é responsável por assistir a recursos de extensão novos ou atualizados `sourceControlConfiguration` no recurso kubernetes habilitado para Arc do Azure, implantando um operador de fluxo para assistir ao repositório git e propagar todas as atualizações feitas no `sourceControlConfiguration` . É possível, até mesmo, criar vários `sourceControlConfiguration` recursos com `namespace` escopo no mesmo cluster de kubernetes habilitado para Arc do Azure para obter multilocação. Nesse caso, cada operador só pode implantar configurações em seu respectivo namespace.

O repositório Git pode conter quaisquer recursos válidos do Kubernetes, incluindo Namespaces, ConfigMaps, Implantações, DaemonSets etc.  Ele também pode conter gráficos Helm para implantar aplicativos. Um conjunto comum de cenários inclui a definição de uma configuração de linha de base para sua organização, que pode incluir funções e associações comuns do Azure, agentes de monitoramento ou registro em log ou serviços de todo o cluster.

O mesmo padrão pode ser usado para gerenciar uma coleção maior de clusters, que podem ser implantados em ambientes heterogêneos. Por exemplo, você pode ter um repositório que defina a configuração da linha de base da sua organização e aplicá-lo a dezenas de clusters do Kubernetes de uma só vez. A [política do Azure pode automatizar](use-azure-policy.md) a criação de um `sourceControlConfiguration` com um conjunto específico de parâmetros em todos os recursos de kubernetes habilitados para o Azure ARC em um escopo (assinatura ou grupo de recursos).

Este guia de introdução orientará você pela aplicação de um conjunto de configurações com escopo de administrador de cluster.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster conectado ao Kubernetes e habilitado para Azure Arc. Se você precisar de um cluster conectado, confira o [guia de início rápido sobre conexão a um cluster](./connect-cluster.md).

## <a name="create-a-configuration"></a>Criar uma configuração

O [repositório de exemplo](https://github.com/Azure/arc-k8s-demo) usado neste documento é estruturado em todo o persona de um operador de cluster que gostaria de provisionar alguns namespaces, implantar uma carga de trabalho comum e fornecer uma configuração específica da equipe. O uso desse repositório cria os seguintes recursos no cluster:

**Namespaces:** `cluster-config`, `team-a`, `team-b`
**Implantação:** `cluster-config/azure-vote`
**ConfigMap** `team-a/endpoints`

O `config-agent` pesquisa o Azure para novo ou atualizado a `sourceControlConfiguration` cada 30 segundos, que é o tempo máximo gasto pelo `config-agent` para selecionar uma configuração nova ou atualizada.
Se você estiver associando um repositório privado com o `sourceControlConfiguration` , certifique-se de também concluir as etapas em [aplicar configuração de um repositório git privado](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Usando a extensão de CLI do Azure para `k8sconfiguration` , vamos vincular nosso cluster conectado a um [repositório git de exemplo](https://github.com/Azure/arc-k8s-demo). Daremos a essa configuração o nome `cluster-config`, instruiremos o agente a implantar o operador no namespace `cluster-config` e concederemos ao operador permissões de `cluster-admin`.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>Parâmetro repository-url

Confira a seguir os cenários que dão suporte para o valor do parâmetro repository-url.

| Cenário | Formatar | Descrição |
| ------------- | ------------- | ------------- |
| Repositório Git público | http [s]://Server/repo.git ou git://server/repo.git   | Repositório Git público  |
| Repositório git privado – SSH – chaves criadas por fluxo | SSH://[user@] servidor/repositório. git ou [user@] servidor: repositório. git | A chave pública gerada pelo fluxo precisa ser adicionada à conta de usuário em seu provedor de serviços git. Se a chave de implantação for adicionada ao repositório em vez da conta de usuário, use `git@` no lugar de `user@` . Encontre mais detalhes [aqui](#apply-configuration-from-a-private-git-repository) |

Esses cenários têm suporte pelo fluxo, mas ainda não pelo sourceControlConfiguration.

| Cenário | Formatar | Descrição |
| ------------- | ------------- | ------------- |
| Repositório git privado-HTTPS | https://server/repo.git | Em breve (dará suporte a nome de usuário/senha, nome de usuário/token, certificado) |
| Repositório git privado-SSH – chaves fornecidas pelo usuário | SSH://[user@] servidor/repositório. git ou [user@] servidor: repositório. git | Em breve |
| Host git privado – SSH – known_hosts personalizado | SSH://[user@] servidor/repositório. git ou [user@] servidor: repositório. git | Em breve |

#### <a name="additional-parameters"></a>Parâmetros Adicionais

Para personalizar a criação de configuração, confira alguns parâmetros adicionais:

`--enable-helm-operator`: comutador *opcional* para habilitar o suporte para implantações de gráficos Helm.

`--helm-operator-chart-values`: valores de gráfico *opcionais* para o operador Helm (se estiver habilitado).  Por exemplo: '--set helm.versions=v3'.

`--helm-operator-chart-version`: versão de gráfico *opcional* para o operador Helm (se estiver habilitada). Padrão: '0.6.0'.

`--operator-namespace`: nome do namespace *opcional* do operador. Padrão: 'default'

`--operator-params`: parâmetros *opcionais* do operador. Deve ser fornecido entre aspas simples. Por exemplo, ```--operator-params='--git-readonly --git-path=releases' ```

Opções com suporte en -operator-params

| Opção | Descrição |
| ------------- | ------------- |
| --git-branch  | Ramificação do repositório git a ser usada para manifestos do Kubernetes. O padrão é 'master'. |
| --git-path  | Caminho relativo dentro do repositório Git no Flux para localizar manifestos do Kubernetes. |
| --git-readonly | O repositório Git será considerado somente leitura. O Flux não tentará gravar nele. |
| --manifest-generation  | Se habilitado, o Flux procurará .flux.yaml e executará o Kustomize ou outros geradores de manifesto. |
| --git-poll-interval  | Período durante o qual a pesquisa de novos repositórios Git ocorrerá. O padrão é '5m' (cinco minutos). |
| --sync-garbage-collection  | Se habilitado, o Flux excluirá os recursos que ele criou, mas não estará mais presente no Git. |
| --git-label  | Rótulo para acompanhar o progresso da sincronização, usado para marcar a ramificação Git.  O padrão é 'flux-sync'. |
| --git-user  | Nome de usuário para confirmação de git. |
| --git-email  | Email a ser usado para confirmação de git. |

* Se '--git-user' ou '--git-email' não estiver definido (o que significa que você não deseja que o Flux grave no repositório), então --git-readonly será definido automaticamente (caso ainda não tenha sido definido).

* Se enableHelmOperator for verdadeiro, as cadeias de caracteres operatorInstanceName + operatorNamespace não poderão exceder 47 caracteres combinados.  Se você não aderir a esse limite, você receberá o seguinte erro:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Para obter mais informações, consulte a [documentação do fluxo](https://aka.ms/FluxcdReadme).

> [!TIP]
> É possível criar um sourceControlConfiguration no portal do Azure também na guia **configurações** da folha de recursos do kubernetes habilitado para Arc do Azure.

## <a name="validate-the-sourcecontrolconfiguration"></a>Validar o sourceControlConfiguration

Como usar a CLI do Azure para validar a criação do `sourceControlConfiguration` com sucesso.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Observe que o recurso `sourceControlConfiguration` é atualizado com status de conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Durante a criação de `sourceControlConfiguration`, algumas coisas acontecem nos bastidores:

1. O `config-agent` do Azure Arc monitora o Azure Resource Manager quanto a configurações novas ou atualizadas (`Microsoft.KubernetesConfiguration/sourceControlConfiguration`)
1. `config-agent` detecta a nova configuração `Pending`
1. `config-agent` lê as propriedades de configuração e prepara a implantação de uma instância gerenciada do `flux`
    * `config-agent` cria o namespace de destino
    * `config-agent` prepara uma conta de serviço do Kubernetes com a permissão apropriada (escopo de `cluster` ou `namespace`)
    * `config-agent` implanta uma instância do `flux`
    * `flux` gera uma chave SSH e registra a chave pública
1. `config-agent` relata o status de volta para `sourceControlConfiguration`

Enquanto o processo de provisionamento ocorre, `sourceControlConfiguration` passa por algumas alterações de estado. Monitore o progresso com o comando `az k8sconfiguration show ...` acima:

1. `complianceStatus` -> `Pending`: representa os estados inicial e em andamento
1. `complianceStatus` -> `Installed`: o `config-agent` conseguiu configurar o cluster com êxito e implantar `flux` sem erros
1. `complianceStatus` -> `Failed`: o `config-agent` encontrou um erro ao implantar o `flux`; os detalhes devem estar disponíveis no corpo da resposta `complianceStatus.message`

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório git privado

Se você estiver usando um repositório git privado, precisará executar mais uma tarefa para fechar o loop: Adicione a chave pública gerada pelo `flux` como uma chave de **implantação** no repositório.

**Obter a chave pública usando a CLI do Azure**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Obter a chave pública por meio do portal do Azure**

1. No portal do Azure, navegue até o recurso de cluster conectado.
2. Na página de recursos, selecione "Configurações" e confira a lista de configurações desse cluster.
3. Selecione a configuração que usa o repositório git privado.
4. Na janela de contexto exibida, na parte inferior da janela, copie a **Chave pública do repositório**.

Se você estiver usando o GitHub, use uma das duas opções a seguir:

**Opção 1: adicionar a chave pública à sua conta de usuário**

1. Abra o GitHub, clique no ícone do seu perfil no canto superior direito da página.
2. Clique em **configurações**
3. Clique nas **chaves SSH e GPG**
4. Clique em **nova chave SSH**
5. Forneça um título
6. Cole a chave pública (sem as aspas)
7. Clique em **Adicionar chave SSH**

**Opção 2: adicionar a chave pública como uma chave de implantação ao repositório git**

1. Abra o GitHub, navegue até seu repositório, para **configurações** e, em seguida, para **implantar chaves**
2. Clique em **Adicionar implantar chave**
3. Forneça um título
4. Marque **permitir acesso de gravação**
5. Cole a chave pública (sem as aspas)
6. Clique em **Adicionar chave**

**Se você está usando um repositório DevOps do Azure, adicione a chave às chaves SSH**

1. Em **Configurações de Usuário** no canto superior direito (ao lado da imagem de perfil), clique em **Chaves públicas SSH**
1. Selecione **+ Nova Chave**
1. Forneça um nome
1. Cole a chave pública sem as aspas
1. Clique em **Adicionar**

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração do Kubernetes

Depois que o `config-agent` instalar a instância do `flux`, os recursos mantidos no repositório git deverão começar a fluir para o cluster. Verifique se os namespaces, as implantações e os recursos foram criados:

```console
kubectl get ns --show-labels
```

**Saída:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Podemos ver que os namespaces `team-a`, `team-b`, `itops` e `cluster-config` foram criados.

O operador `flux` foi implantado no namespace `cluster-config`, conforme indicado por nosso `sourceControlConfig`:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Saída:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Exploração adicional

Você pode explorar outros recursos implantados como parte do repositório de configuração:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Excluir uma configuração

Exclua um `sourceControlConfiguration` usando o CLI do Azure ou portal do Azure.  Depois de iniciar o comando delete, o `sourceControlConfiguration` recurso será excluído imediatamente no Azure, mas pode levar até uma hora para a exclusão completa dos objetos associados do cluster (temos um item de pendência para reduzir esse intervalo de tempo).

> [!NOTE]
> Depois que um sourceControlConfiguration com escopo de namespace é criado, é possível que os usuários com `edit` Associação de função no namespace implantem cargas de trabalho nesse namespace. Quando isso `sourceControlConfiguration` com o escopo do namespace é excluído, o namespace é deixado intacto e não será excluído para evitar a interrupção dessas outras cargas de trabalho.
> As alterações no cluster que foram o resultado de implantações do repositório git rastreado não são excluídas quando o `sourceControlConfiguration` é excluído.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Próximas etapas

- [Usar o Helm com a configuração de controle do código-fonte](./use-gitops-with-helm.md)
- [Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
