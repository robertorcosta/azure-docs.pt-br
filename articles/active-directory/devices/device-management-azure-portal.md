---
title: Como gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262237"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gerenciar identidades de dispositivos usando o portal Azure

Com o gerenciamento de identidade do dispositivo no Azure Active Directory (Azure AD), você pode garantir que seus usuários estejam acessando seus recursos a partir de dispositivos que atendam aos seus padrões de segurança e conformidade.

Este artigo:

- Assume que você está familiarizado com a [introdução ao gerenciamento de identidade de dispositivos no Azure Active Directory](overview.md)
- Fornece informações sobre o gerenciamento de suas identidades de dispositivos usando o portal Azure AD

## <a name="manage-device-identities"></a>Gerenciar identidades do dispositivo

O portal Azure AD fornece um lugar central para gerenciar suas identidades de dispositivo. Você pode chegar a este lugar usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou:

1. Faça login no [portal Azure](https://portal.azure.com).
1. Navegue até dispositivos de diretório ativo do >  **Azure****.**

A página de **Dispositivos** permite que você:

- Configurar suas configurações de dispositivo
- Localizar dispositivos
- Executar tarefas de gerenciamento de identidade de dispositivos
- Revisar registros de auditoria relacionados a dispositivos  
  
## <a name="configure-device-settings"></a>Definir configurações do dispositivo

Para gerenciar as identidades dos dispositivos usando o portal Azure AD, seus dispositivos precisam estar [registrados ou unidos ao](overview.md) Azure AD. Como administrador, você pode ajustar o processo de registro e ingresso de dispositivos definindo as configurações do dispositivo.

A página de configurações do dispositivo permite configurar configurações relacionadas às identidades do dispositivo:

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/21.png)

- **Os usuários podem juntar dispositivos ao Azure AD** - Esta configuração permite selecionar os usuários que podem registrar seus dispositivos como dispositivos azure AD. O padrão é **All**.

> [!NOTE]
> Os usuários podem juntar dispositivos à configuração **do Azure AD** só é aplicável ao adesão do Azure AD no Windows 10.

- **Outros administradores locais nos dispositivos associados ao Azure AD** – você pode selecionar os usuários que têm direitos de administrador local em um dispositivo. Os usuários adicionados aqui são adicionados à função *Administradores do dispositivo* no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade Premium Edition disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).
- **Os usuários podem registrar seus dispositivos com o Azure AD** - Você precisa configurar essa configuração para permitir que dispositivos pessoais, iOS, Android e macOs do Windows 10 sejam registrados no Azure AD. Se você selecionar **Nenhum,** os dispositivos não poderão se registrar no Azure AD. O registro com o Microsoft Intune ou o MDM (Gerenciamento de Dispositivo Móvel) para o Office 365 exige registro. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NENHUM** não estará disponível.
- **Exigir que o Multi-Factor Auth se junte aos dispositivos** - Você pode escolher se os usuários são obrigados a fornecer um fator de autenticação adicional para juntar seu dispositivo ao Azure AD. O padrão é **No**. É recomendável exigir a autenticação multifator ao registrar um dispositivo. Antes de habilitar a autenticação multifator para este serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para saber mais sobre os diferentes serviços de autenticação multifator do Azure, consulte [Introdução à autenticação multifator do Azure](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Exigir que o Multi-Factor Auth se junte à** configuração de dispositivos que se aplica a dispositivos que estejam aderidos ao Azure AD ou ao Azure AD. Esta configuração não se aplica aos dispositivos aderidos a Azure AD híbridos.

- **Número máximo de dispositivos** - Esta configuração permite selecionar o número máximo de dispositivos registrados no Azure AD ou no Azure AD que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos. O valor padrão é **20**.

> [!NOTE]
> **O número máximo de configurações de dispositivos** se aplica a dispositivos que são aderidos ao Azure AD ou ao Azure AD registrados. Esta configuração não se aplica aos dispositivos aderidos a Azure AD híbridos.

- **Os usuários podem sincronizar configurações e dados de aplicativo em dispositivos** – por padrão, essa configuração é definida como **NENHUM**. Selecionar usuários específicos ou grupos ou TODOS permite que as configurações do usuário e os dados de aplicativo sejam sincronizados em seus dispositivos Windows 10. Saiba mais sobre como a sincronização funciona no Windows 10.
Essa opção é uma funcionalidade premium disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).

## <a name="locate-devices"></a>Localizar dispositivos

Você tem duas opções para localizar dispositivos registrados e associados:

- **Todos os dispositivos** na seção **Gerenciar** da página **Dispositivos**  
- **Dispositivos** na seção **Gerenciar** de uma página **Usuário**

Com as duas opções, você pode obter uma exibição que:

- Permite que você pesquise dispositivos usando o nome de exibição ou iD do dispositivo como filtro.
- Fornece visão geral detalhada de dispositivos registrados e associados
- Permite que você execute tarefas comuns de gerenciamento de dispositivo

![Todos os dispositivos](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Se você vir um dispositivo que é "Hybrid Azure AD unido" com um estado "Pendente" na coluna REGISTRADO, ele indica que o dispositivo foi sincronizado a partir do azure AD connect e está esperando para concluir o registro do cliente. Leia mais sobre como [planejar sua implementação do Azure AD híbrido.](hybrid-azuread-join-plan.md) Podem ser encontradas informações adicionais no artigo, [dispositivos com perguntas frequentes.](faq.md)
>
>   ![Dispositivos pendentes](./media/device-management-azure-portal/75.png)
>
>* Para alguns dispositivos iOS, os nomes de dispositivo que contém apóstrofos podem usar caracteres diferentes que se parecem com apóstrofos. Portanto, procurar por tais dispositivos é um pouco complicado - se você não estiver vendo os resultados da pesquisa corretamente, certifique-se de que a seqüência de pesquisa contém caractere apóstrofo correspondente.

## <a name="device-identity-management-tasks"></a>Tarefas de gerenciamento de identidade de dispositivos

Como administrador global ou administrador de dispositivos em nuvem, você pode gerenciar os dispositivos registrados ou unidos. Os administradores de Serviço do Intune podem:

- Atualizar dispositivos – exemplos são operações diárias, como habilitar/desabilitar dispositivos
- Excluir dispositivos – quando um dispositivo está desativado e deve ser excluído do Azure AD

Esta seção fornece informações sobre tarefas comuns de gerenciamento de identidade de dispositivos.

### <a name="manage-an-intune-device"></a>Gerenciar um dispositivo do Intune

Se você for um administrador do Intune, será possível gerenciar dispositivos marcados como **Microsoft Intune**. Se o dispositivo não estiver inscrito no Microsoft Intune, a opção "Gerenciar" será acinzentada.

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Habilitar/desabilitar um dispositivo do Azure Active Directory

Para ativar / desativar um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **Dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/32.png)

**Comentários:**

- Você precisa ser um administrador global ou administrador de dispositivos em nuvem no Azure AD para ativar / desativar um dispositivo. 
- Desativar um dispositivo impede que um dispositivo seja autenticado com sucesso com o Azure AD, impedindo assim que o dispositivo acesse os recursos do Azure AD que são guardados pela CA do dispositivo ou usando suas credenciais WH4B.
- A desativação do dispositivo revogará tanto o Primary Refresh Token (PRT) quanto qualquer Rt (Refresh Tokens) no dispositivo.

### <a name="delete-an-azure-ad-device"></a>Excluir um dispositivo do Azure Active Directory

Para excluir um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **Dispositivos**

   ![Excluir um dispositivo](./media/device-management-azure-portal/34.png)

**Comentários:**

- Você precisa ser um administrador global ou um administrador do Intune no Azure AD para excluir um dispositivo.
- Excluindo um dispositivo:
   - Impede que um dispositivo acesse seus recursos do Azure Active Directory.
   - Remove todos os detalhes que estão relacionados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  
   - Representa uma atividade não recuperável e não é recomendável a menos que necessário.

Se um dispositivo for gerenciado por outra autoridade de gerenciamento (por exemplo, Microsoft Intune), certifique-se de que o dispositivo foi apagado / aposentado antes de excluir o dispositivo no Azure AD. Revise como [gerenciar dispositivos obsoletos](device-management-azure-portal.md) antes de excluir quaisquer dispositivos.

### <a name="view-or-copy-device-id"></a>Exibir ou copiar a ID do dispositivo

Você pode usar uma ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou usar o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Exibir ou copiar as chaves do BitLocker

É possível exibir e copiar as chaves do BitLocker para ajudar os usuários a recuperar sua unidade criptografada. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode copiar essas chaves ao acessar detalhes do dispositivo.

![Exibir chaves do BitLocker](./media/device-management-azure-portal/36.png)

Para exibir ou copiar as chaves do BitLocker, você precisa ser o proprietário do dispositivo ou um usuário que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de Dispositivo de Nuvem
- Administrador global
- Administrador de assistência técnica
- Administrador de serviços do Intune
- Administrador de segurança
- Leitor de segurança

> [!NOTE]
> Os dispositivos do Windows 10 ingressados no Azure AD híbrido não têm um proprietário. Portanto, se você está procurando por um dispositivo pelo proprietário e não o encontrou, pesquise pela ID do dispositivo.

## <a name="audit-logs"></a>Logs de auditoria

As atividades de dispositivo estão disponíveis por meio dos logs de atividade. Esses registros incluem atividades desencadeadas pelo serviço de registro de dispositivos e pelos usuários:

- Criação de dispositivo e adição de usuários/proprietários no dispositivo
- Alterações nas configurações do dispositivo
- Operações de dispositivo como excluir ou atualizar um dispositivo

O ponto de entrada para os dados de auditoria é **Logs de auditoria**, na seção **Atividade** da página **Dispositivos**.

O registro de auditoria tem uma exibição de lista padrão que mostra:

- A data e a hora da ocorrência
- Os destinos
- O iniciador/ator (quem) de uma atividade
- A atividade (o quê)

![Logs de auditoria](./media/device-management-azure-portal/63.png)

Você pode personalizar a exibição da lista clicando em **Colunas** na barra de ferramentas.

![Logs de auditoria](./media/device-management-azure-portal/64.png)

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (ator)

Além dos filtros, você pode pesquisar itens específicos.

![Logs de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Próximas etapas

[Como gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
