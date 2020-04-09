---
title: Publicar oferta de aplicativo Power BI | Mercado Azure
description: Publique uma oferta de aplicativo Power BI no mercado Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: a5eb5b652f7a419c6c2d1b3e6880c3c3003fb679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985517"
---
# <a name="publish-a-power-bi-app-offer"></a>Publique uma oferta de aplicativo Power BI

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento das ofertas do seu aplicativo Power BI para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções na [visão geral de criação de aplicativos do Power BI](https://aka.ms/AzureCreatePBIServiceApp) para gerenciar suas ofertas migradas.

O último passo, depois de definir uma oferta no Portal de Parceiros em Nuvem e criar os ativos técnicos associados, é submeter a oferta para publicação. Para iniciar esse processo, no painel esquerdo da janela **Nova Oferta,** selecione **Publicar**. Para obter mais informações, consulte [Publicar ofertas no Azure Marketplace e no AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Etapas de publicação

Estas são as principais etapas do processo de publicação:

![Publicando etapas do processo de publicação para oferta de aplicativo Power BI](./media/publishing-process-steps.png)

Esta tabela descreve cada passo e fornece seu tempo estimado de conclusão:

|   Etapa de publicação            |   Hora     |   Descrição                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validar os pré-requisitos       | 15 minutos     | Informações de oferta e configurações de oferta são validadas.                            |
| Certificação                | Um a sete dias   | A Equipe de Certificação power bi analisa sua oferta. A equipe executa seu aplicativo Power BI através de um teste de verificação manual, instalando o aplicativo através da URL de instalação fornecida. As validações primárias são realizadas como parte do processo de certificação do aplicativo (descrito posteriormente neste documento).         |
| Empacotamento                    | \< 1 hora  | Os ativos técnicos da oferta são embalados para uso do cliente.                        |
| Registro de geração de leads | \< 1 hora  | Os sistemas de clientes potenciais são configurados e implantados.                                      |
| Aprovação do publicador            | \-         | Você completa uma revisão final e confirmação antes da oferta ir ao ar. Você também terá agora um link para visualizar sua oferta. Depois de ficar feliz com a aparência da sua visualização, selecione **Go Live** na guia **Status.** Isso envia uma solicitação à equipe de onboarding para listar seu aplicativo no AppSource.    |
| Live                         | \< 3 horas | Sua oferta agora está listada publicamente ("ao vivo") no AppSource, e os clientes podem visualizar seu aplicativo e implantá-lo em suas assinaturas de Power BI. Você também receberá um e-mail de confirmação. Na coluna à direita na guia Todas as **ofertas,** você pode ver o status de todas as suas ofertas. Na guia **Status,** você pode ver o status de fluxo de publicação detalhado para sua oferta. |
|   |   |

Aguarde até oito dias para que este processo seja concluído. Depois de passar por essas etapas de publicação, sua oferta do Power BI App será listada na seção de aplicativos [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI.


### <a name="app-certification-process"></a>Processo de certificação de aplicativo

A equipe de onboarding da Microsoft usa esse processo para validar o envio da oferta do Power BI App:

1. Revise documentos legais e links de ajuda.
2. Valide as informações de contato de suporte.
3. Use a URL do instalador para verificar a instalação adequada.
4. Escaneie o aplicativo em busca de malware e outros conteúdos maliciosos.
5. Verifique se o conteúdo exibido corresponde à descrição do aplicativo.
6. Verifique se as operações relacionadas ao aplicativo funcionam conforme o esperado no Power BI. A equipe abre relatórios e dashboards com dados de amostra, conecta-se a fontes de dados personalizadas, atualiza dados e assim por diante.

A Equipe de Certificação faz comentários quando encontra problemas.  Para obter mais informações sobre os requisitos do aplicativo Power BI, consulte a [documentação](https://go.microsoft.com/fwlink/?linkid=2028636)do aplicativo Power BI .


## <a name="next-steps"></a>Próximas etapas

Recomendamos que você monitore regularmente seu aplicativo no [mercado AppSource](https://appsource.microsoft.com).  Você também deve usar o recurso [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) do [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) para obter informações sobre seus clientes de marketplace e uso de aplicativos. Finalmente, você pode [atualizar sua oferta](./cpp-update-existing-offer.md).
