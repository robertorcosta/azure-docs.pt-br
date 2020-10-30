---
title: Grade de eventos do Azure-guia de solução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1dd464339e7654f8886224ff07cf368b4724ff82
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041399"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Solucionar erros de grade de eventos do Azure
Este guia de solução de problemas fornece uma lista de códigos de erro da grade de eventos do Azure, mensagens de erro, suas descrições e ações recomendadas que você deve executar ao receber esses erros. 

## <a name="error-code-400"></a>Código de erro: 400
| Código do erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | O nome do tópico deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome do tópico personalizado deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas: <ul><li>O</li><li>EventGrid</li><li>Sistema</li></ul> | Escolha um nome de tópico diferente que esteja de acordo com os requisitos de nome do tópico. |
| HttpStatusCode. BadRequest<br/>400 | O nome de domínio deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome de domínio deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome de domínio. Além disso, o nome não deve começar com as seguintes palavras reservadas:<ul><li>O</li><li>EventGrid</li><li>Sistema</li> | Escolha um nome de domínio diferente que atenda aos requisitos de nome de domínio. |
| HttpStatusCode. BadRequest<br/>400 | Tempo de expiração inválido. | O tempo de expiração da assinatura de evento determina quando a assinatura do evento será desativada. Esse valor deve ser um valor DateTime válido no futuro.| Verifique se o tempo de expiração da assinatura do evento está em um formato válido de DateTime e se está definido como no futuro. |

## <a name="error-code-409"></a>Código de erro: 409
| Código do erro | Mensagem de erro | Description | Ação recomendada |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. Conflict <br/>409 | Já existe um tópico com o nome especificado. Escolha um nome de tópico diferente.   | O nome do tópico personalizado deve ser exclusivo em uma única região do Azure para garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o tópico. |
| HttpStatusCode. Conflict <br/> 409 | Já existe um domínio com o especificado. Escolha um nome de domínio diferente. | O nome de domínio deve ser exclusivo em uma única região do Azure para garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o domínio. |
| HttpStatusCode. Conflict<br/>409 | Limite de cota atingido. Para obter mais informações sobre esses limites, consulte [limites de grade de eventos do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Cada assinatura do Azure tem um limite para o número de recursos da grade de eventos do Azure que ele pode usar. Algumas ou todas essas cotas foram excedidas e não foi possível criar mais recursos. |    Verifique o uso dos recursos atuais e exclua os que não forem necessários. Se você ainda precisar aumentar sua cota, envie um email para [aeg@microsoft.com](mailto:aeg@microsoft.com) com o número exato de recursos necessários. |

## <a name="error-code-403"></a>Código de erro: 403

| Código do erro | Mensagem de erro | Description | Ação recomendada |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. proibido <br/>403 | A publicação em {topic/Domain} pelo cliente {IpAddress} foi rejeitada devido a regras de filtragem IpAddress. | O tópico ou domínio tem regras de firewall IP configuradas e o acesso é restrito apenas a endereços IP configurados. | Adicionar o endereço IP às regras de firewall de IP, consulte [Configurar o firewall de IP](configure-firewall.md) |
| HttpStatusCode. proibido <br/> 403 | A publicação em {topic/Domain} pelo cliente é rejeitada, pois a solicitação provém do ponto de extremidade privado e nenhuma conexão de ponto de extremidade privada correspondente foi encontrada para o recurso. | O tópico ou domínio tem pontos de extremidade privados configurados e a solicitação de publicação veio de um ponto de extremidade privado que não está configurado/aprovado. | Configure um ponto de extremidade privado para o tópico/domínio. [Configurar pontos de extremidade privados](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>Solucionar problemas de validação de assinatura de evento

Durante a criação da assinatura do evento, se você estiver vendo uma mensagem de erro como `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , isso indica que há uma falha no handshake de validação. Para resolver esse erro, verifique os seguintes aspectos:

- Faça um HTTP POST para a URL do webhook com um corpo de solicitação [SubscriptionValidationEvent de exemplo](webhook-event-delivery.md#validation-details) usando o postmaster ou a ondulação ou ferramenta semelhante.
- Se o webhook estiver implementando o mecanismo de handshake de validação síncrona, verifique se o ValidationCode é retornado como parte da resposta.
- Se o seu webhook estiver implementando o mecanismo de handshake de validação assíncrona, verifique se você é o HTTP POST está retornando 200 OK.
- Se seu webhook estiver retornando 403 (Proibido) na resposta, verifique se o webhook está atrás de um Gateway de Aplicativo Azure AD ou de um firewall do aplicativo Web. Se for, você precisará desabilitar essas regras de firewall e executar um HTTP POST novamente:

  920300 (Pedido Sem Cabeçalho de Aceitação, podemos consertar isso)

  942430 (Detecção de Anomalias de Caractere SQL Restrito (args): # de caracteres especiais excedido (12))

  920230 (Várias Codificações de URL Detectadas)

  942130 (Ataque de Injeção de SQL: Tautologia do SQL Detectada.)

  931130 (Possível Ataque de Remoção de Arquivo (RFI) = link/referência fora do domínio)


## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste seu problema no [fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
