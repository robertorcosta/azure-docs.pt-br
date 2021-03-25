---
title: Guia do administrador do Azure IoT Central
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral da função de administrador no IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/22/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 95bf9693916c20533455c76f1e137779bb81754c
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105039005"
---
# <a name="iot-central-administrator-guide"></a>Guia do administrador do IoT Central

Este artigo fornece uma visão geral da função de administrador no IoT Central. 

Para acessar e usar a seção Administração, você deve estar na função Administrador no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função Administrador desse aplicativo.

Como _administrador_, você é responsável por tarefas administrativas, como:

* Gerenciando funções
* Permissões de organização
* Gerenciar aplicativo alterando o nome e a URL do aplicativo
* Carregando imagem
* Excluindo um aplicativo em seu aplicativo IoT Central do Azure.

## <a name="manage-application-settings"></a>Gerenciar configurações de aplicativo
Você tem a capacidade de [gerenciar as configurações do aplicativo](howto-administer.md).

## <a name="manage-billing"></a>Gerenciar a cobrança
Você pode [gerenciar sua cobrança de IOT central do Azure](howto-view-bill.md). Você pode mover seu aplicativo do plano de preços gratuito para um plano de preços padrão e também atualizar ou fazer downgrade de seu plano de preços.

## <a name="export-applications"></a>Exportar aplicativos
Você pode [exportar seu aplicativo de IOT do Azure](howto-use-app-templates.md) para que possa reutilizá-lo.

## <a name="manage-migration-between-versions"></a>Gerenciar a migração entre versões
Quando você cria um novo aplicativo IoT Central, ele é um aplicativo v3. Se você tiver criado um aplicativo anteriormente, dependendo de quando ele foi criado, ele poderá ser v2. Você pode [migrar uma V2 para um aplicativo v3](howto-migrate.md).

## <a name="monitor-application-health"></a>Monitorar a integridade do aplicativo
Você pode definir as métricas fornecidas pelo IoT Central para [avaliar a integridade dos dispositivos](howto-monitor-application-health.md) conectados ao seu aplicativo IOT central e a integridade de suas exportações de dados em execução.

## <a name="manage-security-x509-sas-keys-api-tokens"></a>Gerenciar a segurança (X. 509, chaves SAS, tokens de API)
Como _administrador_ , você pode fazer o seguinte:
* Gerenciar [certificados X. 509](how-to-roll-x509-certificates.md)
* Chaves de [SAS](concepts-get-connected.md) da organização
* Examinar [tokens de API](https://docs.microsoft.com/rest/api/iotcentral/)

## <a name="configure-file-uploads"></a>Configurar carregamentos de arquivo
Você pode configurar como fazer [uploads de arquivos](howto-configure-file-uploads.md)

## <a name="tools---azure-cli-azure-powershell-azure-portal"></a>Ferramentas – CLI do Azure, Azure PowerShell, portal do Azure

Aqui estão algumas ferramentas às quais você tem acesso como _administrador_.
* [CLI do Azure](howto-manage-iot-central-from-cli.md)
* [PowerShell do Azure](howto-manage-iot-central-from-powershell.md)
* [Azure portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre como administrar seu aplicativo de IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar usuários e funções](howto-manage-users-roles.md) no Azure IOT central.
