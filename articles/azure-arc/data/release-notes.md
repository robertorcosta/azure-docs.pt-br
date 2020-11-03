---
title: Serviços de dados habilitados para Arc do Azure – notas de versão
description: Notas de versão mais recentes
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: e7312ffd4d55f0403359f8aad2d0a8433a716f77
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280372"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notas de versão – serviços de dados habilitados para o Azure Arc (versão prévia)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Outubro de 2020 

### <a name="breaking-changes"></a>Alterações de quebra

Esta versão apresenta as seguintes alterações significativas: 

* Os arquivos de definição de recurso personalizado do PostgreSQL (CRD) substituem que o termo `shards` foi renomeado para `workers` . Este termo ( `workers` ) corresponde ao nome do parâmetro de linha de comando.

* `azdata arc postgres server delete` solicita confirmação antes de excluir uma instância de Postgres.  Use `--force` para ignorar o prompt.

### <a name="additional-changes"></a>Alterações adicionais

* Um novo parâmetro opcional foi adicionado ao `azdata arc postgres server create` chamado `--volume-claim mounts` . O valor é uma lista separada por vírgulas de montagens de declaração de volume. Uma montagem de declaração de volume é um par de tipo de volume e nome de PVC. O tipo de volume, por enquanto, só permite `backup` .  No PostgreSQL, quando o tipo de volume é `backup` , o PVC é montado no `/mnt/db-backups` .  Isso permite o compartilhamento de backups entre instâncias de postgres para que o backup de uma instância postgres possa ser restaurado em outro.

* Um novo nome curto para definições de recursos personalizados do PostgresSQL: 

  * `pg11` 

  * `pg12`

* O upload de telemetria fornece ao usuário:

   * Número de pontos carregados no Azure

     ou 

   * Se nenhum dado tiver sido carregado no Azure, um prompt para tentar novamente.

* `azdata arc dc debug copy-logs` Agora também lê da `/var/opt/controller/log` pasta e coleta logs do Postgres.

*   Exibir um indicador de funcionamento durante a criação e restauração de um postgres de backup.

* `azdata arc postrgres backup list` Agora inclui informações de tamanho do backup.

* A propriedade nome do administrador do SQL Instância Gerenciada foi adicionada à coluna direita da folha visão geral na portal do Azure.



## <a name="september-2020"></a>Setembro de 2020

Os serviços de dados habilitados para Arc do Azure são liberados para visualização pública. Os serviços de dados habilitados para Arc permitem que você gerencie serviços de dados em qualquer lugar.

- Instância Gerenciada de SQL
- Hiperescala do PostgreSQL

Para obter instruções, consulte [o que são os serviços de dados habilitados para o Azure Arc?](overview.md)

## <a name="known-limitations-and-issues"></a>Limitações e problemas conhecidos

- Os nomes de instância gerenciada do SQL não podem ter mais de 13 caracteres
- Nenhuma atualização in-loco para o controlador de dados do Arc do Azure ou instâncias de banco de dado.
- As imagens de contêiner dos serviços de dados habilitados para Arc não foram assinadas.  Talvez seja necessário configurar os nós do Kubernetes para permitir que as imagens de contêiner não assinadas sejam recebidas.  Por exemplo, se você estiver usando o Docker como o tempo de execução do contêiner, poderá definir a variável de ambiente DOCKER_CONTENT_TRUST = 0 e reiniciar.  Outros runtimes de contêiner têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias gerenciadas SQL do ARC ou grupos de servidores de hiperescala PostgreSQL habilitados do Azure no portal do Azure.
- Por enquanto, se você estiver usando o NFS, precisará definir `allowRunAsRoot` como `true` em seu arquivo de perfil de implantação antes de criar o controlador de dados de arco do Azure.
- Somente autenticação de logon do SQL e PostgreSQL.  Não há suporte para Azure Active Directory ou Active Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança relaxadas.  Confira a documentação para obter detalhes.
- Não há suporte para o dimensionamento do número _de nós de_ trabalho de hiperescala PostgresSQL.
- Se você estiver usando o mecanismo de serviço kubernetes do Azure (mecanismo de AKS) no Hub Azure Stack com o controlador de dados Arc do Azure e instâncias de banco de dado, não haverá suporte para a atualização para uma versão mais recente do kubernetes. Desinstale o controlador de dados Arc do Azure e todas as instâncias de banco de dado antes de atualizar o cluster kubernetes.
- A visualização não oferece suporte a backup/restauração para o mecanismo do postgres versão 11. Ele só dá suporte a backup/restauração para postgres versão 12.
- O AKS (serviço kubernetes do Azure), clusters que abrangem [várias zonas de disponibilidade](../../aks/availability-zones.md) não têm suporte no momento para serviços de dados habilitados para Arc do Azure. Para evitar esse problema, ao criar o cluster AKS no portal do Azure, se você selecionar uma região em que as zonas estão disponíveis, desmarque todas as zonas do controle de seleção. Veja a seguinte imagem:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Desmarque as caixas de seleção de cada zona para especificar nenhuma.":::

## <a name="next-steps"></a>Próximas etapas
  
> **Quer apenas experimentar as novidades?**  
> Obtenha uma introdução rápida com o artigo [Começar a usar o Azure Arc rapidamente](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) no AKS (Serviço de Kubernetes do Azure), no AWS EKS (Elastic Kubernetes Service), no GKE (Google Cloud Kubernetes Engine) ou em uma VM do Azure.

[Instalar as ferramentas de cliente](install-client-tools.md)

[Criar o controlador de dados do Azure Arc](create-data-controller.md) (é necessária a instalação das ferramentas de cliente primeiro)

[Criar uma instância gerenciada de SQL do Azure no Azure Arc](create-sql-managed-instance.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)

[Criar um grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL no Azure Arc](create-postgresql-hyperscale-server-group.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)
