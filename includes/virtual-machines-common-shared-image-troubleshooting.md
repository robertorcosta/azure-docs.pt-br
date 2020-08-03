---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: c043cfb6cf437ab55cc4a48ced716798738adbef
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507426"
---
Se você encontrar problemas ao executar quaisquer operações em galerias de imagens compartilhadas, definições de imagem e versões de imagem, execute o comando com falha novamente no modo de depuração. O modo de depuração é ativado passando a `--debug` opção com a CLI e a `-Debug` opção com o PowerShell. Depois de localizar o erro, siga este documento para solucionar os erros.


## <a name="unable-to-create-a-shared-image-gallery"></a>Não é possível criar uma galeria de imagens compartilhadas

Possíveis causas:

*O nome da galeria é inválido.*

Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços. Altere o nome da galeria e tente novamente. 

*O nome da galeria não é exclusivo na sua assinatura.*

Escolha outro nome de galeria e tente novamente.


## <a name="unable-to-create-an-image-definition"></a>Não é possível criar uma definição de imagem 

Possíveis causas:

*o nome da definição de imagem é inválido.*

Os caracteres permitidos para a definição de imagem são letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos. Altere o nome da definição de imagem e tente novamente.

*As propriedades obrigatórias para criar uma definição de imagem não são preenchidas.*

As propriedades como nome, editor, oferta, sku e tipo de sistema operacional são obrigatórias. Verifique se todas as propriedades estão sendo passadas.

Verifique se o **OSType**, Linux ou Windows, da definição da imagem é igual à origem que você está usando para criar a versão da imagem. 


## <a name="unable-to-create-an-image-version"></a>Não é possível criar uma versão da imagem 

Possíveis causas:

*O nome de versão da imagem é inválido.*

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion. MinorVersion. patch*. Altere o nome da versão de imagem e tente novamente.

*A imagem gerenciada de origem da qual a versão da imagem está sendo criada não foi encontrada.* 

Verifique se a imagem de origem existe e está na mesma região que a versão da imagem.

*A imagem gerenciada não terminou de ser provisionada.*

Verifique se o estado de provisionamento da imagem gerenciada de origem é **Êxito**.

*A lista região de destino não inclui a região de origem.*

A lista de regiões de destino deve incluir a região de origem da versão da imagem. Inclua a região de origem na lista de regiões de destino para as quais você deseja que o Azure replique sua versão da imagem.

*A replicação para todas as regiões de destino não foi concluída.*

Use o sinalizador **--expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. Caso contrário, aguarde até 6 horas até a conclusão do trabalho. Se falhar, execute o comando novamente para criar e replicar a versão da imagem. Se houver muitas regiões de destino para as quais a versão da imagem esteja sendo replicada, considere fazer a replicação em fases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Não é possível criar uma VM ou conjunto de dimensionamento 

Possíveis causas:

*O usuário que está tentando criar uma VM ou um conjunto de dimensionamento de máquinas virtuais ou não tem acesso de leitura para a versão da imagem.*

Entre em contato com o proprietário da assinatura e peça para conceder acesso de leitura à versão da imagem ou aos recursos pai (como a Galeria de imagens compartilhada ou definição de imagem) por meio do [controle de acesso baseado em função do Azure (RBAC do Azure)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

*A versão da imagem não foi encontrada.*

Verifique se a região em que você está tentando criar uma escala de VM ou máquina virtual está incluída na lista de regiões de destino da versão da imagem. Se a região já estiver na lista de regiões de destino, verifique se o trabalho de replicação foi concluído. Você pode usar o sinalizador **--expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. 

*A criação do conjunto de dimensionamento de máquinas virtuais ou VM leva muito tempo.*

Verifique se o **OSType** da versão da imagem da qual você está tentando criar a VM ou o conjunto de dimensionamento de máquinas virtuais tem o mesmo **OSType** da fonte que você usou para criar a versão da imagem. 

## <a name="unable-to-share-resources"></a>Não é possível compartilhar recursos

O compartilhamento de galeria de imagens compartilhada, definição de imagem e recursos de versão de imagem entre assinaturas é habilitado usando o RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) ). 

## <a name="replication-is-slow"></a>A replicação é lenta

Use o sinalizador **--expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. Caso contrário, aguarde até 6 horas até a conclusão do trabalho. Se falhar, dispare o comando novamente para criar e replicar a versão da imagem. Se houver muitas regiões de destino para as quais a versão da imagem esteja sendo replicada, considere fazer a replicação em fases.

## <a name="azure-limits-and-quotas"></a>Limites e cotas do Azure 

[Limites e cotas do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) se aplicam a todos os recursos de versão de imagem, definição da imagem e galeria de imagens compartilhadas. Fique dentro dos limites para suas assinaturas. 
