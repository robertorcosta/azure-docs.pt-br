---
title: Integrar soluções de segurança na Central de Segurança do Azure | Microsoft Docs
description: Saiba como a Central de Segurança do Azure se integra aos parceiros para aprimorar a segurança geral dos recursos do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758069"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança na Central de Segurança do Azure
Este documento ajuda você a gerenciar soluções de segurança já conectadas à Central de Segurança do Azure e a adicionar novas.

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança integradas do Azure
A Central de Segurança facilita a criação de soluções de segurança integradas no Azure. Os benefícios incluem:

- **Implantação simplificada**: a Central de Segurança oferece provisionamento simplificado das soluções integradas de parceiros. Para soluções como antimalware e avaliação de vulnerabilidades, o Security Center pode provisionar o agente em suas máquinas virtuais. Para dispositivos de firewall, o Security Center pode cuidar de grande parte da configuração de rede necessária.
- **Detecções integradas**: Os eventos de segurança das soluções de parceiros são coletados, agregados e exibidos automaticamente como parte dos alertas e incidentes do Security Center. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Unificação de gerenciamento e monitoramento de integridade**: os clientes podem usar eventos de integridade integrados para monitorar todas as soluções de parceiro em um relance. O gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.

Atualmente, as soluções integradas de segurança incluem avaliação de vulnerabilidades por [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e firewall de aplicativos Web do Microsoft Application Gateway.

> [!NOTE]
> O Security Center não instala o agente Log Analytics em dispositivos virtuais parceiros porque a maioria dos fornecedores de segurança proíbe agentes externos que executam em seus aparelhos.

Para saber mais sobre a integração de ferramentas de varredura de vulnerabilidades da Qualys, incluindo um scanner integrado disponível para clientes de nível padrão, consulte: 

- [Scanner de vulnerabilidade integrado para máquinas virtuais](built-in-vulnerability-assessment.md).
- [Implantando uma solução de varredura de vulnerabilidade satisfaz o parceiro](partner-vulnerability-assessment.md).

O Security Center também oferece análise de vulnerabilidades para o seu:

* Bancos de dados SQL - veja [Explorar relatórios de avaliação de vulnerabilidades no painel de avaliação de vulnerabilidades](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Imagens do Registro de Contêineres do Azure - veja [a integração do Registro de Contêineres do Azure com o Security Center (Preview)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>Como as soluções de segurança são integradas
As soluções de segurança do Azure implantadas da Central de Segurança serão conectadas automaticamente. Você também pode conectar outras fontes de dados de segurança, incluindo computadores em execução no local ou em outras nuvens.

[![Integração de soluções de parceiros](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerenciar soluções de segurança integrada do Azure e outras fontes de dados

1. Do [portal Azure,](https://azure.microsoft.com/features/azure-portal/)abra o **Security Center.**

1. No menu do Security Center, selecione **soluções de segurança**.

A partir da página **de soluções de Segurança,** você pode ver a saúde das soluções integradas de segurança do Azure e executar tarefas básicas de gerenciamento.

### <a name="connected-solutions"></a>Soluções conectadas

A seção **soluções conectadas** inclui soluções de segurança atualmente conectadas ao Security Center. Também mostra o estado de saúde de cada solução.  

![Soluções conectadas](./media/security-center-partner-integration/connected-solutions.png)

O status de uma solução de parceiro pode ser:

* **Saudável** (verde) - sem problemas de saúde.
* **Insalubre** (vermelho) - há um problema de saúde que requer atenção imediata.
* **Parou de relatar** (laranja) - a solução parou de relatar sua saúde.
* **Não informado** (cinza) - a solução ainda não informou nada e nenhum dado de saúde está disponível. O status de uma solução pode não ser informado se foi conectado recentemente e ainda está sendo implantado.

> [!NOTE]
> Se os dados de status de integridade não estiverem disponíveis, a Central de Segurança mostrará a data e a hora do último evento recebido para indicar se a solução está reportando ou não. Se não houver dados de saúde disponíveis e nenhum alerta for recebido nos últimos 14 dias, o Security Center indica que a solução é insalubre ou não está relatando.
>
>

Selecione **VIEW** para obter informações adicionais e opções como:

   - **Console de solução** - Abre a experiência de gerenciamento para esta solução.
   - **Link VM** - Abre a página Aplicativos de Link. Nela, você pode conectar recursos à solução de parceiro.
   - **Excluir solução**
   - **Configurar**

   ![Detalhes da solução de parceiro](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Soluções descobertas

O Security Center descobre automaticamente as soluções de segurança em execução no Azure, mas não está conectada ao Security Center e exibe as soluções na seção **Soluções Descobertas.** Essas soluções incluem soluções Azure, como [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), e soluções de parceiros.

> [!NOTE]
> A camada Padrão da Central de Segurança é necessária no nível de assinatura para o recurso de descoberta de soluções. Consulte [Pricing](security-center-pricing.md) para saber mais sobre os níveis de preços.
>

Selecione **CONNECT** em uma solução para se integrar ao Security Center e seja notificado sobre alertas de segurança.

### <a name="add-data-sources"></a>Adicionar fontes de dados

A seção **Adicionar fontes de dados** inclui outras fontes de dados disponíveis que podem ser conectadas. Para obter instruções sobre como adicionar dados de qualquer uma dessas fontes, clique em **ADICIONAR**.

![Fontes de dados](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a integrar as soluções de parceiro à Central de Segurança. Para obter informações relacionadas, consulte os seguintes artigos:

* [Alertas e recomendações](continuous-export.md)de segurança de exportação . Saiba como configurar uma integração com o Azure Sentinel ou qualquer outro SIEM.
* [Monitoramento da integridade de segurança na Central de Segurança](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.