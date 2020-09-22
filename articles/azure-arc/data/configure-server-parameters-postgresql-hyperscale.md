---
title: Configurar parâmetros do servidor do mecanismo postgres para seu grupo de servidores de hiperescala do PostgreSQL no arco do Azure
titleSuffix: Azure Arc enabled data services
description: Configurar parâmetros do servidor do mecanismo postgres para seu grupo de servidores de hiperescala do PostgreSQL no arco do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4e8813647211e0adbfe43a45ae0d19dc12a4a165
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933224"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Definir as configurações do mecanismo de banco de dados para a hiperescala PostgreSQL habilitada para Arc do Azure

Este documento descreve as etapas para definir as configurações do mecanismo de banco de dados do seu grupo de servidores de hiperescala do PostgreSQL para valores personalizados (não padrão). Para obter detalhes sobre quais parâmetros do mecanismo de banco de dados podem ser definidos e qual é o valor padrão, consulte a documentação do PostgreSQL [aqui](https://www.postgresql.org/docs/current/runtime-config.html).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> A visualização não oferece suporte à definição dos seguintes parâmetros: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Sintaxe

O formato geral do comando para definir as configurações do mecanismo de banco de dados é:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-the-current-custom-values-of-the-parameters-settings"></a>Mostrar os valores personalizados atuais das configurações de parâmetros

## <a name="with-azdata-cli-command"></a>Com o comando da CLI do azdata

```console
azdata arc postgres server show -n <server group name>
```

Por exemplo:

```console
azdata arc postgres server show -n postgres01
```

Esse comando retorna a especificação do grupo de servidores no qual você verá os parâmetros que você definiu. Se não houver nenhuma seção engine\settings, significa que todos os parâmetros estão sendo executados em seu valor padrão:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

## <a name="with-kubectl-command"></a>Com o comando kubectl

Siga as etapas abaixo.

### <a name="1-retrieve-the-kind-of-custom-resource-definition-for-your-server-group"></a>1. recupere o tipo de definição de recurso personalizado para seu grupo de servidores

Execute:

```console
azdata arc postgres server show -n <server group name>
```

Por exemplo:

```console
azdata arc postgres server show -n postgres01
```

Esse comando retorna a especificação do grupo de servidores no qual você verá os parâmetros que você definiu. Se não houver nenhuma seção engine\settings, significa que todos os parâmetros estão sendo executados em seu valor padrão:

```
> {
  >"apiVersion": "arcdata.microsoft.com/v1alpha1",
  >"**kind**": "**postgresql-12**",
  >"metadata": {
    >"creationTimestamp": "2020-08-25T14:32:23Z",
    >"generation": 1,
    >"name": "postgres01",
    >"namespace": "arc",
```

Lá, procure o campo "Kind" e reserve o valor, por exemplo: `postgresql-12` .

### <a name="2-describe-the-kubernetes-custom-resource-corresponding-to-your-server-group"></a>2. descrever o recurso personalizado kubernetes correspondente ao seu grupo de servidores 

O formato geral do comando é:

```console
kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
```

Por exemplo:

```console
kubectl describe postgresql-12 postgres01
```

Se houver valores personalizados definidos para as configurações do mecanismo, ele os retornará. Por exemplo:

```console
Engine:
...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
```

Se você não tiver definido valores personalizados para nenhuma das configurações do mecanismo, a seção Configurações do mecanismo do ResultSet estará vazia, como:

```console
Engine:
...
    Settings:
      Default:
```

## <a name="set-custom-values-for-the-engine-settings"></a>Definir valores personalizados para as configurações do mecanismo

Os comandos abaixo definem os parâmetros do nó de coordenador e os nós de trabalho de seu subdimensionamento do PostgreSQL com os mesmos valores. Ainda não é possível definir parâmetros por função em seu grupo de servidores. Ou seja, ainda não é possível configurar um determinado parâmetro para um específico no nó de coordenador e para outro valor para os nós de trabalho.

## <a name="set-a-single-parameter"></a>Definir um único parâmetro

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

## <a name="set-multiple-parameters-with-a-single-command"></a>Definir vários parâmetros com um único comando

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

## <a name="reset-a-parameter-to-its-default-value"></a>Redefinir um parâmetro para seu valor padrão

Para redefinir um parâmetro para seu valor padrão, defina-o sem indicar um valor. 

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

## <a name="reset-all-parameters-to-their-default-values"></a>Redefinir todos os parâmetros para seus valores padrão

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Considerações especiais

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Definir um parâmetro cujo valor contém uma vírgula, um espaço ou um caractere especial

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Passar uma variável de ambiente em um valor de parâmetro

A variável de ambiente deve ser encapsulada dentro de "' '" para que ela não seja resolvida antes de ser definida.

Por exemplo: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```



## <a name="next-steps"></a>Próximas etapas
- Leia sobre como [escalar horizontalmente (adicionando nós de trabalho)](scale-out-postgresql-hyperscale-server-group.md) seu grupo de servidores
- Leia sobre como escalar verticalmente [(aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) seu grupo de servidores
