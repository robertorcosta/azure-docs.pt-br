---
title: Locais de dados para a área de trabalho virtual do Windows-Azure
description: Uma breve visão geral de quais locais os dados e metadados da área de trabalho virtual do Windows são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b1a061b2dfcdd212178a3f534a1a951697940685
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292616"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Locais de dados e metadados para a área de trabalho virtual do Windows

>[!IMPORTANT]
>Esse conteúdo se aplica à área de trabalho virtual do Windows com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows (clássico) sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/data-locations-2019.md).

A área de trabalho virtual do Windows está disponível atualmente para todas as localizações geográficas. Os administradores podem escolher o local para armazenar os dados do usuário ao criarem as máquinas virtuais do pool de hosts e serviços associados, como servidores de arquivos. Saiba mais sobre as geografias do Azure no [mapa de datacenter do Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft não controla nem limita as regiões nas quais você ou seus usuários podem acessar seus dados específicos do aplicativo e do usuário.

>[!IMPORTANT]
>A área de trabalho virtual do Windows armazena informações de metadados globais, como nomes de locatário, nomes de pool de hosts, nomes de grupos de aplicativos e nomes de entidades de usuário em um datacenter. Sempre que um cliente cria um objeto de serviço, ele deve inserir um local para o objeto de serviço. O local inserido determina onde os metadados do objeto serão armazenados. O cliente escolherá uma região do Azure e os metadados serão armazenados na geografia relacionada. Para obter uma lista de todas as regiões do Azure e geografias relacionados, consulte [geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

No momento, só damos suporte ao armazenamento de metadados na geografia Estados Unidos (EUA) do Azure. Os metadados armazenados são criptografados em repouso e os espelhos com redundância geográfica são mantidos na geografia. Todos os dados do cliente, como configurações do aplicativo e dados do usuário, residem no local escolhido pelo cliente e não são gerenciados pelo serviço. Mais regiões geográficas ficarão disponíveis conforme o serviço cresce.

Os metadados de serviço são replicados na geografia do Azure para fins de recuperação de desastre.