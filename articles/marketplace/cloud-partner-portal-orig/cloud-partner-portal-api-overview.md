---
title: Referência de API do Portal parceiro em nuvem | Mercado Azure
description: Descrição dos pré-requisitos a serem usados e lista de operações de API do Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256409"
---
# <a name="cloud-partner-portal-api-reference"></a>Referência de API do Portal do Cloud Partner

> [!NOTE]
> As APIs do Portal de Parceiros em Nuvem são integradas ao Partner Center e continuarão a funcionar depois que suas ofertas forem migradas para o Partner Center. A integração introduz pequenas mudanças. Revise as [Alterações nas APIs do CPP](#changes-to-cpp-apis-after-the-migration-to-partner-center) listadas neste documento para garantir que seu código continue funcionando após a migração para o Partner Center.

As APIs REST do Portal do Cloud Partner permitem a recuperação programática e a manipulação de cargas de trabalho, ofertas e perfis de publicação. As APIs usam RBAC (controle de acesso baseado em função) para impor permissões corretas no tempo de processamento.

Essa referência fornece os detalhes técnicos para as APIs REST do Portal do Cloud Partner. Os exemplos de conteúdo deste documento são apenas para referência e estão sujeitos a alterações à medida que novas funcionalidades são adicionadas.

## <a name="prerequisites-and-considerations"></a>Pré-requisitos e considerações

Antes de usar as APIs, você deverá revisar:

- O artigo de [Pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para saber como adicionar uma entidade de serviço à sua conta e obter um token de acesso do Azure AD (Azure Active Directory) para autenticação.
- As duas estratégias [de controle de simultuários](./cloud-partner-portal-api-concurrency-control.md) disponíveis para chamar essas APIs.
- As [considerações](./cloud-partner-portal-api-considerations.md) adicionais da API como controle de versão e tratamento de erros.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Alterações nas APIs do CPP após a migração para o Partner Center

| **API** | **Descrição da alteração** | **Impacto** |
| ------- | ---------------------- | ---------- |
| POST Publique, GoLive, Cancele | Para ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o status da operação. | Ao enviar qualquer uma das solicitações POST correspondentes para uma oferta, o cabeçalho de localização terá um de dois formatos, dependendo do status de migração da oferta:<ul><li>Ofertas não migradas<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operação GET | Para tipos de oferta que anteriormente suportavam o campo 'notificação-e-mail' na resposta, este campo será preterido e não retornará para ofertas migradas. | Para ofertas migradas, não enviaremos mais notificações para a lista de e-mails especificados nas solicitações. Em vez disso, o serviço de API se alinhará com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações serão enviadas para o endereço de e-mail definido na seção informações de contato do Vendedor das configurações da sua conta no Partner Center, para notificá-lo sobre o progresso da operação.<br><br>Por favor, revise o endereço de e-mail definido na seção informações de contato do Vendedor das configurações da sua conta na Central de [Parceiros](https://partner.microsoft.com/dashboard/account/management) para garantir que o e-mail correto seja fornecido para notificações.  |

## <a name="common-tasks"></a>Tarefas comuns

Essa referência detalha as APIs para executar as seguintes tarefas comuns.

### <a name="offers"></a>Ofertas

- [Recuperar todas as ofertas](./cloud-partner-portal-api-retrieve-offers.md)
- [Recuperar uma oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Recuperar status da oferta](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Criar uma oferta](./cloud-partner-portal-api-creating-offer.md)
- [Publicar uma oferta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operações

- [Recuperar operações](./cloud-partner-portal-api-retrieve-operations.md)
- [Cancelar operações](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicar um aplicativo

- [Entrar no ar](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Outras tarefas

- [Definir preços para ofertas de máquinas virtuais](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Solução de problemas

- [Solucionando problemas de erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
