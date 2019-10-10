---
title: Integração de Log do Azure com os logs de auditoria do Azure Active Directory | Microsoft Docs
description: Saiba como instalar o serviço de Integração de Logs do Azure e integre os registros de logs de auditoria do Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: e7f3a9bc9aade5f8ade857e9c2f14f8e9898a7da
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244666"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrar o Azure Active Directory a logs de auditoria

Eventos de auditoria do Azure AD (Azure Active Directory) ajudam a identificar ações privilegiadas que ocorreram no Azure Active Directory. Você pode ver os tipos de eventos que você pode acompanhar examinando os [eventos de relatório de auditoria do Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> O recurso integração de logs do Azure será preterido por 06/15/2019. Downloads de AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer para prosseguir com a análise da postagem [Usar o Azure Monitor para a integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Etapas para integrar os logs de auditoria do Azure Active Directory

> [!NOTE]
> Antes de experimentar as etapas neste artigo, você deve examinar o artigo [Introdução](azure-log-integration-get-started.md) e concluir as etapas relevantes dele.

1. Abra o prompt de comando e execute este comando:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Execute este comando: 
 
   ``azlog createazureid``

   Esse comando solicitará o logon do Azure. O comando cria uma entidade de serviço do Azure Active Directory em locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um administrador, um coadministrador ou um proprietário. O comando falhará se o usuário conectado for apenas um usuário convidado no locatário do Azure AD. A autenticação do Azure é feita por meio do AD do Azure. A criação de uma entidade de serviço para a Integração de Logs do Azure criará a identidade do Azure AD que receberá o acesso de leitura das assinaturas do Azure.

3. Execute o seguinte comando para fornecer sua ID de locatário. Você precisa ser membro da função de administrador de locatário para executar o comando.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exemplo:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de Auditoria do Azure Active Directory são criados nelas:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

O vídeo a seguir demonstra as etapas abordadas neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Para obter instruções específicas sobre como colocar as informações nos arquivos JSON em seu SIEM (sistema de gerenciamento de eventos e informações de segurança), contate o fornecedor do SIEM.

Ajuda da comunidade está disponível por meio de [Fórum do MSDN de integração de Log do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na comunidade de Integração de Log do Azure deem suporte umas às outras com perguntas, respostas, dicas e truques. Além disso, a equipe de Integração de Logs do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../../azure-supportability/how-to-create-azure-support-request.md). Selecione **Integração de Log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Integração de Log do Azure, veja:

* [Integração de Logs do Microsoft Azure para logs do Azure](https://azure.microsoft.com/services/monitor/): Esta página do Centro de Download fornece detalhes, requisitos de sistema e instruções de instalação para a Integração de Logs do Azure.
* [Introdução à Integração de Logs do Azure](azure-log-integration-overview.md): Este documento apresenta a Integração de Logs do Azure, seus principais recursos e como ele funciona.
* [Perguntas frequentes sobre a Integração de Logs do Azure](azure-log-integration-faq.md): Este artigo de perguntas frequentes responde às perguntas sobre a Integração de Logs do Azure.
* [Novos recursos para o Diagnóstico do Azure e os logs de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Esta postagem de blog apresenta os logs de auditoria do Azure e outros recursos que ajudam a obter informações sobre as operações de recursos do Azure.
