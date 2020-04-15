---
title: Residência de dados de autenticação multifatorial do Azure
description: Saiba quais dados pessoais e organizacionais o Azure Multi-Factor Authentication armazena sobre você e seus usuários e quais dados permanecem dentro do país de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309793"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Residência de dados e dados do cliente para autenticação multifatorial do Azure

Os dados do cliente são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao se inscrever em um serviço Microsoft Online, como o Office 365 e o Azure. Para obter informações sobre onde os dados do cliente estão armazenados, você pode usar a seção [Onde estão seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

O azure Multi-Factor Authentication e o Azure Multi-Factor Authentication Server processam e armazenam alguma quantidade de dados pessoais e dados organizacionais. Este artigo descreve o que e onde os dados são armazenados.

As seguintes atividades de Autenticação Multifatorial atualmente se originam de data centers dos EUA, exceto quando observado:

* A autenticação de dois fatores usando chamadas telefônicas ou SMS normalmente se origina de data centers nos EUA e é roteada por provedores globais.
    * As solicitações de autenticação de usuários de propósito geral de outras regiões, como Europa ou Austrália, são atualmente processadas por data centers nessa região. Outros eventos, como redefinições de senha de autoatendimento, eventos Azure B2C ou cenários híbridos usando o adaptador NPS Extension ou AD FS, são todos processados atualmente por data centers dos EUA.
* As notificações push usando o aplicativo Microsoft Authenticator são originárias de data centers nos EUA. Além disso, serviços específicos para fornecedores de dispositivos também podem entrar em jogo de diferentes regiões.
* Os códigos OATH são validados atualmente nos EUA.
    * Mais uma vez, eventos de autenticação de usuários de uso geral que se originam em outras regiões, como Europa ou Austrália, são processados por data centers nessa região. Eventos adicionais são atualmente processados por data centers dos EUA.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dados pessoais armazenados pela Autenticação Multifatorial do Azure

Dados pessoais são informações de nível de usuário associadas a uma pessoa específica. Os seguintes armazenamentos de dados contêm informações pessoais:

* Usuários bloqueados
* Usuários ignorados
* Solicitações de alteração de token de dispositivo do Microsoft Authenticator
* Relatórios de atividades de autenticação multifatorial
* Ativações do Microsoft Authenticator

Essas informações são retidas por 90 dias.

A autenticação multifatorial do Azure não registra dados pessoais, como nome de usuário, número de telefone ou endereço IP, mas há um *UserObjectId* que identifica tentativas de autenticação multifatorial aos usuários. Os dados de log são armazenados por 30 dias.

### <a name="azure-multi-factor-authentication"></a>Autenticação Multifator do Azure

Para as nuvens públicas do Azure, excluindo a autenticação Azure B2C, a extensão NPS e o Adaptador AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em registros de autenticação multifatorial     |
| SMS unidirecional                          | Em registros de autenticação multifatorial     |
| Chamada de voz                           | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores<br />Usuários bloqueados se a fraude for relatada |
| Notificação do Autenticador microsoft | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores<br />Usuários bloqueados se a fraude for relatada<br />Alterar solicitações quando o token do dispositivo Microsoft Authenticator muda |

> [!NOTE]
> O armazenamento de dados do relatório de atividade de autenticação multifatorial é armazenado nos Estados Unidos para todas as nuvens, independentemente da região que processa a solicitação de autenticação. Microsoft Azure Germany, Microsoft Azure Operated by 21Vianet e Microsoft Government Cloud têm seus próprios armazenamentos de dados independentes separados dos armazenamentos de dados da região da nuvem pública, no entanto esses dados são sempre armazenados nos Estados Unidos.

Para o Governo do Microsoft Azure, Microsoft Azure Alemanha, Microsoft Azure Operado por 21Vianet, autenticação Azure B2C, Extensão NPS e Windows Server 2016 ou 2019 AD FS Adapter, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores |
| SMS unidirecional                          | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores |
| Chamada de voz                           | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores<br />Usuários bloqueados se a fraude for relatada |
| Notificação do Autenticador microsoft | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores<br />Usuários bloqueados se a fraude for relatada<br />Alterar solicitações quando o token do dispositivo Microsoft Authenticator muda |

### <a name="multi-factor-authentication-server"></a>Servidor de Autenticação Multifator

Se você implantar e executar o Azure Multi-Factor Authentication Server, os seguintes dados pessoais são armazenados:

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o Multi-Factor Authentication Server para novas implantações. Novos clientes que gostariam de exigir autenticação multifatorial de seus usuários devem usar a Autenticação Multifatorial baseada na nuvem. Os clientes existentes que ativaram o Multi-Factor Authentication Server antes de 1º de julho poderão baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores |
| SMS unidirecional                          | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores |
| Chamada de voz                           | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores<br />Usuários bloqueados se a fraude for relatada |
| Notificação do Autenticador microsoft | Em registros de autenticação multifatorial<br />Armazenamento de dados de relatório de atividade de autenticação de vários fatores<br />Usuários bloqueados se a fraude for relatada<br />Alterar solicitações quando o token do dispositivo Microsoft Authenticator muda |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dados organizacionais armazenados pela Autenticação Multifatorial do Azure

Os dados organizacionais são informações de nível de inquilino que podem expor a configuração ou a configuração do ambiente. As configurações do inquilino das seguintes páginas de autenticação multifatorial do portal Azure podem armazenar dados organizacionais, como limites de bloqueio ou informações de identificação de chamadas para solicitações de autenticação de telefone recebidas:

* Bloqueio de conta
* Alerta de fraude
* Notificações
* Configurações de chamada telefônica

E para o Azure Multi-Factor Authentication Server, as seguintes páginas do portal Azure podem conter dados organizacionais:

* Configurações do servidor
* Desvio único
* Regras de cache
* Status do servidor de autenticação de vários fatores

## <a name="log-data-location"></a>Localização de dados de registro

Onde as informações de registro são armazenadas depende de qual região elas são processadas. A maioria das geografias possui recursos nativos de autenticação multifatorial do Azure, de modo que os dados de log são armazenados na mesma região que processa a solicitação de Autenticação Multifatorial. Em geografias sem suporte nativo de autenticação multifatorial do Azure, eles são atendidos pelas geografias dos Estados Unidos ou da Europa e os dados de registro são armazenados na mesma região que processa a solicitação de Autenticação Multifatorial.

Alguns dados de registro de autenticação principais são armazenados apenas nos Estados Unidos. Microsoft Azure Germany e Microsoft Azure Operados pela 21Vianet são sempre armazenados em sua respectiva nuvem. Os dados de log do Microsoft Government Cloud são sempre armazenados nos Estados Unidos.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre quais informações do usuário são coletadas pela autenticação multifatorial baseada na nuvem e pelo azure Multi-Factor Authentication Server, consulte [a coleta de dados do usuário do Azure Multi-Factor Authentication](howto-mfa-reporting-datacollection.md).
