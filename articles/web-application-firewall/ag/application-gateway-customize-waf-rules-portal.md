---
title: Personalizar regras usando o portal - Firewall de aplicativos Web do Azure
description: Este artigo fornece informações sobre como personalizar as regras do Firewall de Aplicativos da Web no Application Gateway com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048371"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalize as regras do Firewall de Aplicativos da Web usando o portal Azure

O WAF (Azure Application Gateway Web Application Firewall, firewall de aplicativos web) do Azure Application oferece proteção para aplicativos web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (Open Web Application Security Project). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de Aplicativo possibilita que a capacidade personalize regras e grupos de regras. Para obter mais informações sobre os grupos e regras de regras específicas, consulte [Lista de grupos e regras do CRS do Firewall de Aplicativos da Web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se o gateway de aplicativo não estiver usando a camada WAF, será exibida no painel direito a opção de atualizar o gateway de aplicativo para a camada WAF. 

![Habilitar WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

**Para exibir grupos de regras e regras**
1. Navegue até o gateway de aplicativo e selecione **Firewall do aplicativo Web**.  
2. Selecione sua **política WAF**.
2. Selecione **Regras gerenciadas**.

   Essa exibição mostra uma tabela na página com todos os grupos de regras fornecidos com o conjunto de regras escolhido. Todas as caixas de seleção de regra são selecionadas.

## <a name="disable-rule-groups-and-rules"></a>Desabilitar regras e grupos de regras

> [!IMPORTANT]
> Tenha cuidado ao desativar quaisquer grupos de regras ou regras. Isso pode expô-lo a riscos de segurança aumentados.

Quando você está desabilitando as regras, você pode desativar um grupo de regras inteiro ou regras específicas um ou mais grupos de regras. 

**Para desabilitar regras específicas ou grupos de regras**

   1. Pesquise pelas regras ou os grupos de regras que você deseja desabilitar.
   2. Selecione as caixas de seleção para as regras que deseja desativar. 
   3. Selecione a ação na parte superior da página (habilitar/desativar) para as regras selecionadas.
   2. Selecione **Salvar**. 

![Salvar alterações][3]

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que o WAF bloqueie a solicitação enquanto estiver no Modo de Prevenção. No Modo de Detecção, eles são registrados como exceções.

Estes não podem ser configurados ou desativados:

* A não análise do órgão de solicitação resulta no bloqueio da solicitação, a menos que a inspeção corporal seja desligada (XML, JSON, dados de formulário)
* O comprimento dos dados do corpo de solicitação (sem arquivos) é maior do que o limite configurado
* O corpo de solicitação (incluindo arquivos) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedeu o limite

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as regras desabilitadas, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [os diagnósticos do Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
