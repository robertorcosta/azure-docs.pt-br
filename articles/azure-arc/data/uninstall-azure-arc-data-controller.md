---
title: Excluir controlador de dados de arco do Azure
description: Excluir controlador de dados de arco do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dce70441e5e8487bfc015df0a946ab3cd74c14f0
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397582"
---
# <a name="delete-azure-arc-data-controller"></a>Excluir controlador de dados de arco do Azure

O artigo a seguir descreve como excluir um controlador de dados de arco do Azure.

Antes de prosseguir, verifique se todos os serviços de dados que foram criados no controlador de dados foram removidos da seguinte maneira:

## <a name="log-in-to-the-data-controller"></a>Fazer logon no controlador de dados

Faça logon no controlador de dados que você deseja excluir:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Listar & excluir os serviços de dados existentes

Execute o seguinte comando para verificar se há qualquer instância gerenciada do SQL criada:

```
azdata arc sql mi list
```

Para cada instância gerenciada do SQL da lista acima, execute o comando delete da seguinte maneira:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Da mesma forma, para verificar instâncias de hiperescala do PostgreSQL, execute:

```
azdata arc postgres server list
```

E, para cada instância de hiperescala PostgreSQL, execute o comando delete da seguinte maneira:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Excluir controlador

Depois que todas as instâncias gerenciadas do SQL e as instâncias de hiperescala do PostgreSQL tiverem sido removidas, o controlador de dados poderá ser excluído da seguinte maneira:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Remover SCCs (somente Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Excluir objetos de nível de cluster

Além dos objetos com escopo de namespace, se você também quiser excluir os objetos de nível de cluster, como CRDs, `clusterroles` e `clusterrolebindings` , execute os seguintes comandos:

```
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete postgresql-11s.arcdata.microsoft.com 
kubectl delete postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Opcionalmente, exclua o namespace do controlador de dados Arc do Azure


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Próximas etapas

[O que são os serviços de dados habilitados para o Azure Arc?](overview.md)
