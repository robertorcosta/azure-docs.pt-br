---
title: Locais de dados para área de trabalho virtual do Windows Outono 2019 – Azure
description: Uma breve visão geral de quais locais os dados e metadados da área de trabalho virtual do Windows são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fa998ddf8225475bbcf657ed9feee951263d2743
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077588"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Locais de dados para área de trabalho virtual do Windows

>[!IMPORTANT]
>Esse conteúdo se aplica à versão Outono 2019 que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager introduzidos na atualização Spring 2020, confira [este artigo](../data-locations.md).

A área de trabalho virtual do Windows está disponível atualmente para todas as localizações geográficas. Inicialmente, os metadados de serviço só podem ser armazenados na geografia de Estados Unidos (EUA). Os administradores podem escolher o local para armazenar os dados do usuário ao criarem as máquinas virtuais do pool de hosts e serviços associados, como servidores de arquivos. Saiba mais sobre as geografias do Azure no [mapa de datacenter do Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft não controla nem limita as regiões nas quais você ou seus usuários podem acessar seus dados específicos do aplicativo e do usuário.

>[!IMPORTANT]
>A área de trabalho virtual do Windows armazena informações de metadados globais, como nomes de locatário, nomes de pool de hosts, nomes de grupos de aplicativos e nomes de entidades de usuário em um Datacenter localizado no Estados Unidos. Os metadados armazenados são criptografados em repouso e os espelhos com redundância geográfica são mantidos dentro do Estados Unidos. Todos os dados do cliente, como configurações do aplicativo e dados do usuário, residem no local escolhido pelo cliente e não são gerenciados pelo serviço.

Os metadados de serviço são replicados no Estados Unidos para fins de recuperação de desastre.