---
title: Como implantar dependências de nuvem OPC Twin no Azure | Microsoft Docs
description: Este artigo descreve como implantar as dependências oPC Twin Azure necessárias para fazer o desenvolvimento local e a depuração.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824108"
---
# <a name="deploying-dependencies-for-local-development"></a>Implantação de dependências para o desenvolvimento local

Este artigo explica como implantar apenas os Serviços de Plataforma Azure necessários para fazer o desenvolvimento local e a depuração.   No final, você terá um grupo de recursos implantado que contém tudo o que você precisa para o desenvolvimento local e depuração.

## <a name="deploy-azure-platform-services"></a>Implantar serviços de plataforma Azure

1. Certifique-se de que as extensões PowerShell e [AzureRM PowerShell estão](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instaladas.  Abra um prompt de comando ou terminal e execute:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Siga as instruções para atribuir um nome ao grupo de recursos para sua implantação.  O script implanta apenas as dependências para este grupo de recursos em sua assinatura do Azure, mas não os micro serviços.  O script também registra um aplicativo no Diretório Ativo do Azure.  Isso é necessário para suportar a autenticação baseada em OAUTH.  A implantação pode levar vários minutos.

3. Uma vez que o script é concluído, você pode selecionar para salvar o arquivo .env.  O arquivo de ambiente .env é o arquivo de configuração de todos os serviços e ferramentas que você deseja executar em sua máquina de desenvolvimento.  

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de solução de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de usar um nome de grupo de recursos curto e simples.  O nome também é usado para nomear recursos como tal, ele deve cumprir os requisitos de nomeação de recursos.  

### <a name="azure-active-directory-aad-registration"></a>Registro do Azure Active Directory (AAD)

O script de implantação tenta registrar aplicativos AAD no Azure Active Directory.  Dependendo dos seus direitos para o inquilino AAD selecionado, isso pode falhar.   Há três opções:

1. Se você escolheu um inquilino AAD de uma lista de inquilinos, reinicie o script e escolha um diferente da lista.
2. Alternativamente, implante um inquilino AAD privado, reinicie o script e selecione para usá-lo.
3. Continue sem autenticação.  Uma vez que você está executando seus micro serviços localmente, isso é aceitável, mas não imita ambientes de produção.  

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou com sucesso os serviços OPC Twin para um projeto existente, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Saiba como implantar módulos OPC Twin](howto-opc-twin-deploy-modules.md)
