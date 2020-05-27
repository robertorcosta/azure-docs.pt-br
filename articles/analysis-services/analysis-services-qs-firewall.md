---
title: Início Rápido – Configurar o firewall para servidor do Azure Analysis Services | Microsoft Docs
description: Este início rápido ajuda você a configurar um firewall para o servidor do Azure Analysis Services usando o portal do Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 48618815519fad31bff5d6a8d2d2edc82535f437
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697907"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Início Rápido: Configurar firewall do servidor – Portal

Este guia de início rápido ajuda você a configurar um firewall para o servidor Azure Analysis Services. Habilitar um firewall e configurar intervalos de endereços IP para somente os computadores que acessam o servidor são uma parte importante para proteger o servidor e os dados.

## <a name="prerequisites"></a>Pré-requisitos

- Um servidor do Analysis Services na sua assinatura. Para obter mais informações, confira [Início Rápido: Criar um servidor – Portal](analysis-services-create-server.md) ou [Início Rápido: Criar um servidor – PowerShell](analysis-services-create-powershell.md)
- Um ou mais intervalos de endereços IP para computadores cliente (se necessário).
- No momento não há suporte a alguns cenários em que o Power BI Premium se conecta ao Azure Analysis Services, incluindo importação de dados (atualização) e relatórios paginados, mesmo quando Permitir acesso do Power BI está habilitado. Há suporte ao cenário mais comum do uso do Live Connect do Power BI Premium. Há suporte para todos os cenários do Power BI Pro.


## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure 

[Entre no portal](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Configure um firewall

1. Clique no seu servidor para abrir a página de Visão geral. 
2. Em **CONFIGURAÇÕES** > **Firewall** > **Habilitar firewall**, clique em **Ativar**.
3. Para permitir o acesso do DirectQuery do serviço do Power BI, em **Permitir o acesso do Power BI**, clique em **Ativar**.  
4. (Opcional) Especifique um ou mais intervalos de endereços IP. Insira um nome, um endereço IP inicial e um final para cada intervalo. O nome da regra de firewall deve ser limitado a 128 caracteres e somente pode conter caracteres maiúsculos, minúsculos, números, sublinhado e hífen. Espaços e outros caracteres especiais não são permitidos.
5. Clique em **Save** (Salvar).

     ![Configurações de firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua os intervalos de endereços IP ou desabilite o firewall.

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido você aprendeu a configurar um firewall para o seu servidor. Agora que você tem o servidor, e ele está protegido com um firewall, você pode adicionar um modelo de dados de exemplo básico a ele a partir do portal. Ter um modelo de exemplo é útil para saber mais sobre como configurar funções do modelo de banco de dados e testar conexões de cliente. Para saber mais, continue para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao seu servidor](analysis-services-create-sample-model.md)
