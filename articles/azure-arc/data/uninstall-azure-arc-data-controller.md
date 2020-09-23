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
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933205"
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

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Opcionalmente, exclua o namespace do controlador de dados Arc do Azure


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Próximas etapas

[O que são os serviços de dados habilitados para o Azure Arc?](overview.md)