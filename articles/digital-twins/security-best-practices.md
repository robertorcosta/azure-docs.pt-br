---
title: Entenda as melhores práticas de segurança - Azure Digital Twins | Microsoft Docs
description: Conheça as melhores práticas de segurança para a Azure Digital Twins e a Internet das Coisas.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122552"
---
# <a name="azure-digital-twins-security-best-practices"></a>Azure gêmeos Digital práticas recomendadas de segurança

A segurança do Azure Digital Twins permite o acesso preciso a recursos e ações específicos no seu gráfico de IoT. Ele faz isso através do gerenciamento de função granular e permissão chamado [controle de acesso baseado em função](./security-role-based-access-control.md).

Os Gêmeos Digitais do Azure também usam outros recursos de segurança presentes no IoT do Azure, incluindo o Azure AD (Azure Active Directory). Por esse motivo, configurar e proteger aplicativos criados nos Gêmeos Digitais do Azure envolve o uso de muitas das mesmas [práticas de segurança do IoT do Azure](../iot-fundamentals/iot-security-best-practices.md) recomendadas atualmente.

Este artigo resume as principais práticas recomendadas a serem seguidas.

> [!IMPORTANT]
> Para garantir a segurança máxima para o seu espaço de IoT, analise os recursos de segurança adicional. Certifique-se de incluir seus fornecedores de dispositivos.

> [!TIP]
> Use [o Azure Security Center para IoT](https://docs.microsoft.com/azure/asc-for-iot/) para ajudar a detectar ameaças e vulnerabilidades de segurança de IoT.

## <a name="iot-security-best-practices"></a>Práticas recomendadas de segurança de IoT

Algumas práticas recomendadas de chave para proteger com segurança seus dispositivos IoT incluem:

> [!div class="checklist"]
> * Proteja cada dispositivo conectado ao seu espaço IoT de maneira inviolável.
> * Limite a função de cada dispositivo, sensor e pessoa no espaço da IoT. Se comprometido, o efeito é minimizado.
> * Considere o uso de potencial de IP do dispositivo de filtragem de endereço e porta de restrição.
> * Limitar largura de banda de e/s e o dispositivo para melhorar o desempenho. A limitação de taxa pode melhorar a segurança, evitando ataques de negação de serviço.
> * Mantenha o firmware do dispositivo, o sistema operacional e o software atualizados.
> * Auditoria e revisão periódica de dispositivos, software, redes e práticas recomendadas de segurança de gateway à medida que continuam a melhorar e evoluir.
> * Use sistemas de segurança, software e dispositivos de segurança confiáveis, certificados e compatíveis. Por exemplo, revise [as ofertas de conformidade](https://azure.microsoft.com/overview/trusted-cloud/compliance/) do Azure Cloud.

Algumas práticas importantes para proteger com segurança um espaço IoT incluem:

> [!div class="checklist"]
> * Criptografe dados persistentes, salvos ou armazenados.
> * Requerer que senhas ou chaves sejam periodicamente alteradas ou atualizadas.
> * Limite cuidadosamente o acesso e as permissões por função. Leia a seção [As melhores práticas de controle de acesso baseadas em papéis](#role-based-access-control-best-practices) abaixo.
> * Considere uma topologia de rede dividida para que os dispositivos em cada rede sejam isolados dos outros.
> * Use criptografia avançada. Exija senhas longas, use protocolos seguros e [autenticação multifatorial](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Monitore](./how-to-configure-monitoring.md) recursos de IoT para observar outliers, ameaças ou parâmetros de recursos que estão fora do intervalo de operação usual. Use o Azure Analytics para gerenciar o monitoramento.

> [!IMPORTANT]
> Leia as melhores práticas de segurança do Azure [IoT](../iot-fundamentals/iot-security-best-practices.md) para iniciar uma estratégia abrangente de segurança de IoT.

> [!NOTE]
> Para obter mais informações sobre processamento e monitoramento de eventos, leia [eventos e mensagens de rota com o Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Práticas recomendadas do Azure Active Directory

O Azure Digital Twins usa [o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) para autenticar usuários e proteger aplicativos. O Azure Active Directory dá suporte à autenticação para diversas arquiteturas modernas. Eles são todos baseados em protocolos padrão do setor, como OAuth 2.0 ou OpenID Connect. Algumas práticas importantes para proteger o seu espaço IoT para o Active Directory do Azure incluem:

> [!div class="checklist"]
> * Armazene segredos e chaves de aplicativos do Azure Active Directory em uma localização segura, assim como o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Usar um certificado emitido por um confiável [autoridade de certificação](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) em vez de segredos do aplicativo para autenticar.
> * Limite o escopo de acesso do OAuth 2.0 para um token.
> * Verifique o período de tempo que um token é válido e se um token permanece válido.
> * Defina períodos de tempo adequados para os tokens serem válidos. Atualize tokens expirados.
> * Remover **URIs de redirecionamento** não utilizados e permissões por [práticas recomendadas de controle de acesso baseadas em função](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Práticas recomendadas de controle de acesso baseado em função

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as práticas recomendadas de IoT do Azure, leia [práticas recomendadas de segurança de IoT](../iot-fundamentals/iot-security-best-practices.md).

* Para saber mais sobre o controle de acesso baseado em função, leia [o controle de acesso baseado em função](./security-role-based-access-control.md).

* Para saber sobre autenticação, leia [autenticar com APIs](./security-authenticating-apis.md).
