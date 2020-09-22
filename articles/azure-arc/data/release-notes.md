---
title: Serviços de dados habilitados para Arc do Azure – notas de versão
description: Notas de versão mais recentes
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 5908083be4e6ed389b606754ffef41a4a371c3e3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933128"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notas de versão – serviços de dados habilitados para o Azure Arc (versão prévia)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Setembro de 2020

Os serviços de dados habilitados para Arc do Azure são liberados para visualização pública. Os serviços de dados habilitados para Arc permitem que você gerencie serviços de dados em qualquer lugar.

- Instância Gerenciada de SQL
- Hiperescala do PostgreSQL

Para obter instruções, consulte [o que são os serviços de dados habilitados para o Azure Arc?](overview.md)

## <a name="next-steps"></a>Próximas etapas

> **Quer apenas experimentar as novidades?**  
> Comece rapidamente com o [Azure Arc JumpStart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) no Azure kubernetes Service (AKs), AWS elástico kubernetes Service (EKS), Google Cloud kubernetes Engine (GKE) ou em uma VM do Azure.

[Instalar as ferramentas de cliente](install-client-tools.md)

[Criar o controlador de dados de arco do Azure](create-data-controller.md) (requer a instalação das ferramentas de cliente primeiro)

[Criar uma instância gerenciada do SQL do Azure no arco do Azure](create-sql-managed-instance.md) (requer a criação de um controlador de dados de arco do Azure primeiro)

[Criar um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL no arco do Azure](create-postgresql-hyperscale-server-group.md) (requer a criação de um controlador de dado de arco do Azure primeiro)

## <a name="known-limitations-and-issues"></a>Limitações e problemas conhecidos

- Os nomes de instância gerenciada do SQL não podem ter mais de 13 caracteres
- Nenhuma atualização in-loco para o controlador de dados do Arc do Azure ou instâncias de banco de dado.
- As imagens de contêiner de serviços de dados habilitados para ARC não são assinadas.  Talvez seja necessário configurar os nós do kubernetes para permitir que as imagens de contêiner não assinadas sejam puxadas.  Por exemplo, se você estiver usando o Docker como o tempo de execução do contêiner, poderá definir a variável de ambiente DOCKER_CONTENT_TRUST = 0 e reiniciar.  Outros tempos de execução de contêiner têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias gerenciadas SQL do ARC ou grupos de servidores de hiperescala PostgreSQL habilitados do Azure no portal do Azure.
- Por enquanto, se você estiver usando o NFS, precisará definir allowRunAsRoot como true no seu arquivo de perfil de implantação antes de criar o controlador de dados de arco do Azure.
- Somente autenticação de logon do SQL e PostgreSQL.  Não há suporte para Azure Active Directory ou Active Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança relaxadas.  Confira a documentação para obter detalhes.
- Não há suporte para o dimensionamento do número _de nós de_ trabalho de hiperescala Postgres.
- Se você estiver usando o mecanismo de serviço kubernetes do Azure (mecanismo de AKS) no Hub Azure Stack com o controlador de dados Arc do Azure e instâncias de banco de dado, não haverá suporte para a atualização para uma versão mais recente do kubernetes. Desinstale o controlador de dados Arc do Azure e todas as instâncias de banco de dado antes de atualizar o cluster kubernetes.
- A visualização não oferece suporte a backup/restauração para o mecanismo do postgres versão 11. Ele só dá suporte a backup/restauração para postgres versão 12.
