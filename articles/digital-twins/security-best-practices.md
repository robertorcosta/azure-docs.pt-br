---
title: Understand security best practices - Azure Digital Twins | Microsoft Docs
description: Learn about security best practices for Azure Digital Twins and the Internet of Things.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: f5c0f6d9f6f7f35722f3df5f35dc1da945f21b9a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229049"
---
# <a name="azure-digital-twins-security-best-practices"></a>Azure gêmeos Digital práticas recomendadas de segurança

A segurança do Azure Digital Twins permite o acesso preciso a recursos e ações específicos no seu gráfico de IoT. Isso é feito através de um gerenciamento granular de funções e permissões chamado [controle de acesso baseado em função](./security-role-based-access-control.md).

Os Gêmeos Digitais do Azure também usam outros recursos de segurança presentes no IoT do Azure, incluindo o Azure AD (Azure Active Directory). Por esse motivo, configurar e proteger aplicativos criados nos Gêmeos Digitais do Azure envolve o uso de muitas das mesmas [práticas de segurança do IoT do Azure](../iot-fundamentals/iot-security-best-practices.md) recomendadas atualmente.

Este artigo resume as principais práticas recomendadas a serem seguidas.

> [!IMPORTANT]
> Para garantir a segurança máxima para o seu espaço de IoT, analise os recursos de segurança adicional. Certifique-se de incluir seus fornecedores de dispositivos.

> [!TIP]
> Use [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/) to help detect IoT security threats and vulnerabilities.

## <a name="iot-security-best-practices"></a>Práticas recomendadas de segurança da IoT

Algumas práticas recomendadas de chave para proteger com segurança seus dispositivos IoT incluem:

> [!div class="checklist"]
> * Proteja cada dispositivo conectado ao seu espaço IoT de maneira inviolável.
> * Limite a função de cada dispositivo, sensor e pessoa no espaço da IoT. Se comprometido, o efeito é minimizado.
> * Considere o uso de potencial de IP do dispositivo de filtragem de endereço e porta de restrição.
> * Limitar largura de banda de e/s e o dispositivo para melhorar o desempenho. A limitação de taxa pode melhorar a segurança, evitando ataques de negação de serviço.
> * Keep device firmware, operating system, and software up to date.
> * Periodically audit and review device, software, network, and gateway security best practices as they continue to improve and evolve.

Algumas práticas importantes para proteger com segurança um espaço IoT incluem:

> [!div class="checklist"]
> * Criptografe dados persistentes, salvos ou armazenados.
> * Requerer que senhas ou chaves sejam periodicamente alteradas ou atualizadas.
> * Limite cuidadosamente o acesso e as permissões por função. Confira a seção [Melhores práticas de controle de acesso baseado em função](#role-based-access-control-best-practices) abaixo.
> * Consider a divided network topology so that devices on each network are isolated from the others.
> * Use criptografia avançada. Require long passwords, use secure protocols, and [multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Monitore](./how-to-configure-monitoring.md) recursos de IoT para observar outliers, ameaças ou parâmetros de recursos que estão fora do intervalo de operação usual. Use o Azure Analytics para gerenciar o monitoramento.

> [!IMPORTANT]
> Read Azure [IoT security best practices](../iot-fundamentals/iot-security-best-practices.md) to begin a comprehensive IoT security strategy.

> [!NOTE]
> Para obter mais informações sobre processamento e monitoramento de eventos, consulte [Rotear eventos e mensagens com os Gêmeos Digitais do Azure](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Práticas recomendadas do Azure Active Directory

Azure Digital Twins uses [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) to authenticate users and protect applications. O Azure Active Directory dá suporte à autenticação para diversas arquiteturas modernas. Eles são todos baseados em protocolos padrão do setor, como OAuth 2.0 ou OpenID Connect. Algumas práticas importantes para proteger o seu espaço IoT para o Active Directory do Azure incluem:

> [!div class="checklist"]
> * Armazene segredos e chaves de aplicativos do Azure Active Directory em uma localização segura, assim como o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Usar um certificado emitido por um confiável [autoridade de certificação](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) em vez de segredos do aplicativo para autenticar.
> * Limite o escopo de acesso do OAuth 2.0 para um token.
> * Verifique o período de tempo que um token é válido e se um token permanece válido.
> * Defina períodos de tempo adequados para os tokens serem válidos.
> * Atualize tokens expirados.

## <a name="role-based-access-control-best-practices"></a>Práticas recomendadas de controle de acesso baseado em função

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre as práticas recomendadas de IoT do Azure, leia [práticas recomendadas de segurança de IoT](../iot-fundamentals/iot-security-best-practices.md).

* Para saber mais sobre o controle de acesso baseado em função, leia [Controle de acesso baseado em função](./security-role-based-access-control.md).

* Para saber sobre autenticação, leia [autenticar com APIs](./security-authenticating-apis.md).