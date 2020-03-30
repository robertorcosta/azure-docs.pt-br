---
title: Termos de uso - Diretório Ativo do Azure | Microsoft Docs
description: Comece a usar os termos de uso do Azure Active Directory para apresentar informações a funcionários ou convidados antes de obter acesso.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480956"
---
# <a name="azure-active-directory-terms-of-use"></a>Termos de uso do Azure Active Directory

Os termos de uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais. Essa apresentação faz com que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais. Este artigo descreve como começar com os termos de uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de visão geral

O vídeo a seguir fornece uma visão geral rápida dos termos de uso.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para ver outros vídeos, consulte:
- [Como implantar termos de uso no Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como implementar os termos de uso no Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com termos de uso?

Os termos de uso do Azure AD têm os seguintes recursos:

- Exija que funcionários ou convidados aceitem seus termos de uso antes de obter acesso.
- Exija que funcionários ou convidados aceitem seus termos de uso em cada dispositivo antes de obter acesso.
- Exija que funcionários ou convidados aceitem seus termos de uso em um horário recorrente.
- Exija que funcionários ou convidados aceitem seus termos de uso antes de registrar informações de segurança no MFA (Multi-Factor Authentication, autenticação multifatorial) do Azure.
- Exija que os funcionários aceitem seus termos de uso antes de registrar informações de segurança no Reset de senha de autoatendimento do Azure AD (SSPR).
- Apresente os termos de uso gerais para todos os usuários da sua organização.
- Apresentar termos de uso específicos com base em atributos de usuário (ex. médicos, enfermeiras, funcionários locais ou internacionais, usando [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresente termos de uso específicos ao acessar aplicativos de alto impacto nos negócios, como o Salesforce.
- Presentes termos de uso em diferentes idiomas.
- Liste quem tem ou não aceitou seus termos de uso.
- Ajudar a atender às normas de privacidade.
- Exibir um registro de termos de atividade de uso para conformidade e auditoria.
- Crie e gerencie termos de uso usando [APIs do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (atualmente em visualização).

## <a name="prerequisites"></a>Pré-requisitos

Para usar e configurar os termos de uso do Azure AD, você deve ter:

- Assinatura do Azure AD Premium P1, P2, EMS E3 ou EMS E5.
   - Se você não tiver uma dessas assinaturas, poderá [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [habilitar a avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que você deseja configurar:
   - Administrador global
   - Administrador de segurança
   - Administrador de acesso condicional

## <a name="terms-of-use-document"></a>Documento Termos de uso

Os termos de uso do Azure AD usam o formato PDF para apresentar conteúdo. O arquivo em PDF pode ter qualquer conteúdo, por exemplo, contratos existentes, o que permite que você colete os contratos do usuário final na entrada do usuário. Para dar suporte a usuários em dispositivos móveis, o tamanho de fonte recomendado em PDF é de 24 pontos.

## <a name="add-terms-of-use"></a>Adicionar termos de uso

Depois de finalizar seus termos de uso, use o seguinte procedimento para adicioná-lo.

1. Entre no Azure como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Acesso Condicional - Termos de uso da lâmina](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

   ![Novo termo de painel de uso para especificar seus termos de uso configurações](./media/terms-of-use/new-tou.png)

1. Na caixa **Nome,** digite um nome para os termos de uso que serão usados no portal Azure.
1. Na caixa **Nome de exibição**, insira um título que os usuários visualizam ao entrar.
1. Para **termos de uso,** navegue pelos termos de uso finalizados pdf e selecione-o.
1. Selecione o idioma para seus termos de uso. A opção de idioma permite carregar vários termos de uso, cada um com um idioma diferente. A versão dos termos de uso que um usuário final verá terá base em suas preferências de navegador.
1. Para exigir que os usuários finais visualizem os termos de uso antes de aceitá-los, **defina exigir que os usuários expandam os termos de uso** para **On**.
1. Para exigir que os usuários finais aceitem seus termos de uso em todos os dispositivos de onde estão acessando, **defina exigir que os usuários consentam em cada dispositivo** para **On**. Os usuários podem ser obrigados a instalar aplicativos adicionais se essa opção estiver ativada. Para obter mais informações, consulte [os termos de uso por dispositivo](#per-device-terms-of-use).
1. Se você quiser expirar os termos de consentimento de uso em um cronograma, defina **os consentimentos de Expire** para **On**. Quando definido como On, duas configurações de cronograma adicionais são exibidas.

   ![Expirar as configurações de consentimentos para definir data de início, frequência e duração](./media/terms-of-use/expire-consents.png)

1. Use as **configurações Expire iniciando** e **Freqüência** para especificar o cronograma para os termos de expiração de uso. A tabela a seguir mostra o resultado para duas configurações de exemplo:

   | Expirar a partir de | Frequência | Result |
   | --- | --- | --- |
   | Data de hoje  | Mensal | A partir de hoje, os usuários devem aceitar os termos de uso e, em seguida, reaceitar todos os meses. |
   | Data no futuro  | Mensal | A partir de hoje, os usuários devem aceitar os termos de uso. Quando a data futura chegar, os consentimentos irão expirar e depois os usuários devem aceitar novamente a todos os meses.  |

   Por exemplo, se você definir a expiração a partir da data para **1 de janeiro** e a frequência para **Mensal**, segue como ocorreriam as expirações para dois usuários:

   | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |
   | Roberto | 15 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |

1. Use a duração antes que a **reaceitação exija (dias)** a configuração para especificar o número de dias antes que o usuário reaceite os termos de uso. Isso permite que os usuários sigam seu próprio cronograma. Por exemplo, se você definir a duração como **30** dias, segue como ocorreriam as expirações para dois usuários:

   | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de janeiro | 31 de janeiro | 2 de março | 1 de abril |
   | Roberto | 15 de janeiro | 14 de fevereiro | 16 de março | 15 de abril |

   É possível usar as configurações **Expirar consentimentos** e **Duração (dias) até exigir a nova aceitação** juntas, mas normalmente você usa uma ou a outra.

1. Em **Acesso Condicional,** use a lista **De formulário Impor com o Modelo de Diretiva de Acesso Condicional** para selecionar o modelo para impor os termos de uso.

   ![Lista de parada de acesso condicional para selecionar um modelo de diretiva](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | Descrição |
   | --- | --- |
   | **Acesso a aplicativos de nuvem para todos os convidados** | Uma política de Acesso Condicional será criada para todos os hóspedes e todos os aplicativos em nuvem. Essa política afeta o portal do Azure. Após ela ser criada, talvez seja necessário sair e entrar novamente. |
   | **Acesso a aplicativos de nuvem para todos os usuários** | Uma política de Acesso Condicional será criada para todos os usuários e todos os aplicativos em nuvem. Essa política afeta o portal do Azure. Após ela ser criada, será necessário sair e entrar novamente. |
   | **Política personalizada** | Selecione os usuários, grupos e aplicativos aos os que esses termos de uso serão aplicados. |
   | **Criar política de acesso condicional mais tarde** | Esses termos de uso aparecerão na lista de controle de subvenções ao criar uma política de Acesso Condicional. |

   >[!IMPORTANT]
   >Os controles de política de acesso condicional (incluindo termos de uso) não suportam a execução de contas de serviço. Recomendamos excluir todas as contas de serviço da política de Acesso Condicional.

    As políticas de acesso condicional personalizado permitem termos de uso granulares, até um aplicativo ou grupo específico de usuários em nuvem. Para obter mais informações, consulte [Quickstart: Exija que os termos de uso sejam aceitos antes de acessar aplicativos em nuvem](require-tou.md).

1. Clique em **Criar**.

   Se você selecionou um modelo de acesso condicional personalizado, aparecerá uma nova tela que permite criar a diretiva de acesso condicional personalizado.

   ![Novo painel de acesso condicional se você escolher o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Agora você deve ver seus novos termos de uso.

   ![Novos termos de uso listados nos termos de uso da lâmina](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Exibir relatório de quem aceitou e recusou

A folha Termos de uso mostra uma contagem de usuários que os aceitaram e recusaram. Estas contagens e que aceitaram/recusaram são armazenadas para a vida útil dos termos de uso.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Termos de uso da lâmina listando o número de programas de usuário aceitaram e recusaram](./media/terms-of-use/view-tou.png)

1. Para obter os termos de uso, clique nos números **em Aceito** ou **Recusado** para visualizar o estado atual para usuários.

   ![Termos de uso consentirem painel listando os usuários que aceitaram](./media/terms-of-use/accepted-tou.png)

1. Para exibir o histórico de um usuário individual, clique no botão de reticências (**... **) e, em seguida **Exibir histórico**.

   ![Exibir menu de contexto histórico para um usuário](./media/terms-of-use/view-history-menu.png)

   No painel de exibição de histórico, você vê um histórico de todos os aceites, recusas e expirações.

   ![Exibir painel histórico lista o histórico aceita, diminui e expira para um usuário](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Exibir logs de auditoria do Azure AD

Se você quiser exibir atividades adicionais, os termos de uso do Azure AD incluem logs de auditoria. Cada consentimento do usuário dispara um evento nos logs de auditoria que ficam armazenados durante **30 dias**. Você pode exibir esses logs no portal ou baixá-los como um arquivo .csv.

Para começar a usar os logs de auditoria do Microsoft Azure AD, use o procedimento a seguir:

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione um termo de uso.
1. Clique em **Exibir logs de auditoria**.

   ![Lâmina de termos de uso com a opção Exibir logs de auditoria destacada](./media/terms-of-use/audit-tou.png)

1. Na tela de logs de auditoria do Azure AD, é possível filtrar as informações usando as listas fornecidas para buscar informações de log de auditoria específico.

   Você também pode clicar em **Download** para baixar as informações em um arquivo .csv para uso local.

   ![Azure AD logs de auditoria data de listagem de tela, política de destino, iniciado por e atividade](./media/terms-of-use/audit-logs-tou.png)

   Se você clicar em um log, um painel é exibido com detalhes adicionais da atividade.

   ![Detalhes da atividade para um registro mostrando atividade, status de atividade, iniciado por, política de destino](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Quais são os termos de uso para os usuários

Uma vez que um termo de uso é criado e aplicado, os usuários, que estão no escopo, verão a seguinte tela durante o login.

![Exemplo de termos de uso que aparecem quando um usuário faz sinal](./media/terms-of-use/user-tou.png)

Os usuários podem visualizar os termos de uso e, se necessário, usar botões para ampliar e diminuir o zoom.

![Veja os termos de uso com botões de zoom](./media/terms-of-use/zoom-buttons.png)

A tela a seguir mostra como os termos de uso parecem em dispositivos móveis.

![Exemplo de termos de uso que aparecem quando um usuário faz o sinal em um dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os usuários só são obrigados a aceitar os termos de uso uma vez e eles não verão os termos de uso novamente em logins subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os usuários podem rever seus termos de uso

Os usuários podem rever e ver os termos de uso que aceitaram usando o procedimento a seguir.

1. Faça login [https://myapps.microsoft.com](https://myapps.microsoft.com)em .
1. No canto superior direito, clique em seu nome e selecione **Perfil**.

   ![Site do MyApps com o painel do usuário aberto](./media/terms-of-use/tou14.png)

1. Na página de seu perfil, clique em **Analisar termos de uso**.

   ![Página de perfil para um usuário que mostra os termos de uso da Revisão](./media/terms-of-use/tou13a.png)

1. A partir daí, você pode revisar os termos de uso aceitos.

## <a name="edit-terms-of-use-details"></a>Editar termos de uso detalhes

Você pode editar alguns detalhes dos termos de uso, mas não pode modificar um documento existente. O procedimento a seguir descreve como editar os detalhes.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que deseja editar.
1. Clique em **editar termos**.
1. Em termos de edição do painel de uso, altere o nome, nome de exibição ou exigir que os usuários expandam os valores.

   Se houver outras configurações que você gostaria de alterar, como documento PDF, exigir que os usuários consentam em cada dispositivo, expirar consentimentos, duração antes da reaceitação ou política de acesso condicional, você deve criar um novo termo de uso.

   ![Editar termos de uso mostrando nome e expandir opções](./media/terms-of-use/edit-tou.png)

1. Clique em **Salvar** para salvar as alterações.

   Depois de salvar as alterações, os usuários terão que aceitar novamente essas edições.

## <a name="add-a-terms-of-use-language"></a>Adicione um termo de uso

O procedimento a seguir descreve como adicionar uma linguagem de uso.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que deseja editar.
1. No painel de detalhes, clique na guia **Idiomas**.

   ![Termos de uso selecionados e mostrando a guia Idiomas no painel de detalhes](./media/terms-of-use/languages-tou.png)

1. Clique em **Adicionar idioma**.
1. Em termos do painel de linguagem do uso de Add, carregar o PDF localizado e selecione o idioma.

   ![Adicione termos de uso painel de idiomas com opções para carregar PDFs localizados](./media/terms-of-use/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de uso por dispositivo

A **exigência de que os usuários consentirem em todas as** configurações do dispositivo permite que você exija que os usuários finais aceitem seus termos de uso em todos os dispositivos de onde estão acessando. O usuário final será obrigado a registrar seu dispositivo no Azure AD. Quando o dispositivo é registrado, o ID do dispositivo é usado para impor os termos de uso em cada dispositivo.

Segue uma lista dos softwares e plataformas com suporte.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicativo nativo** | Sim | Sim | Sim |  |
> | **Microsoft Edge** | Sim | Sim | Sim |  |
> | **Internet Explorer** | Sim | Sim | Sim |  |
> | **Chrome (com extensão)** | Sim | Sim | Sim |  |

Os termos de uso por dispositivo têm as seguintes restrições:

- Um dispositivo somente pode ser unido a um locatário.
- Um usuário deve ter permissões para ingressar seu dispositivo.
- Não há suporte para o aplicativo de registro do Intune.
- Os usuários do Azure AD B2B não são suportados.

Se o dispositivo do usuário não estiver ingressado, ele receberá uma mensagem de que é necessário ingressar o dispositivo dele. A experiência dele será dependente da plataforma e do software.

### <a name="join-a-windows-10-device"></a>Ingressar um dispositivo com Windows 10

Se um usuário estiver usando o Windows 10 e o Microsoft Edge, ele receberá uma mensagem semelhante à seguinte para [ingressar o dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - Mensagem indicando que seu dispositivo deve ser registrado](./media/terms-of-use/per-device-win10-edge.png)

Se estiver usando o Chrome, o usuário será solicitado a instalar a [Extensão de Contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registre um dispositivo iOS

Se um usuário estiver usando um dispositivo iOS, ele será solicitado a instalar o [aplicativo Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registre um dispositivo Android

Se um usuário estiver usando um dispositivo Android, ele será solicitado a instalar o [aplicativo Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Navegadores

Se um usuário estiver usando um navegador sem suporte, ele será solicitado a usar um navegador diferente.

![Mensagem indicando que seu dispositivo deve ser registrado, mas o navegador não é suportado](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Excluir termos de uso

Você pode excluir os termos de uso antigos usando o procedimento a seguir.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja remover.
1. Clique em **Excluir termos**.
1. Na mensagem que será exibida perguntando se deseja continuar, clique em **Sim**.

   ![Mensagem pedindo confirmação para excluir termos de uso](./media/terms-of-use/delete-tou.png)

   Você não deve mais ver seus termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Usuários excluídos e termos de uso ativos

Por padrão, um usuário excluído permanece excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador, se necessário. Após 30 dias, esse usuário será excluído permanentemente. Além disso, usando o portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir permanentemente um usuário excluído recentemente](../fundamentals/active-directory-users-restore.md) antes de atingir o período de tempo. Um usuário foi excluído permanentemente, dados subseqüentes sobre esse usuário serão removidos dos termos de uso ativos. Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="policy-changes"></a>Alterações na política

As políticas de acesso condicional entrarão em vigor imediatamente. Quando isso acontece, o administrador começa a ver uma imagem de "nuvens tristes" ou "problemas de token do Azure AD". O administrador precisa sair e entrar novamente para atender à nova política.

> [!IMPORTANT]
> Os usuários que estão no escopo precisarão sair e entrar novamente para atender a uma nova política se:
>
> - uma política de acesso condicional é habilitada em termos de uso
> - ou se outros termos de uso forem criados

## <a name="b2b-guests-preview"></a>Convidados B2B (versão prévia)

A maioria das organizações tem um processo em vigor para que seus funcionários concordem com os termos de uso e declarações de privacidade de sua organização. Mas como você pode impor os mesmos consentimentos para convidados B2B (business-to-business) do Azure AD quando eles forem adicionados por meio do SharePoint ou Teams? Usando acesso condicional e termos de uso, você pode aplicar uma política diretamente para usuários convidados B2B. Durante o fluxo de resgate do convite, o usuário é apresentado com os termos de uso. No momento, esse suporte está na versão prévia.

Termos de uso só serão exibidos quando o usuário tiver uma conta de convidado no Azure AD. O SharePoint Online atualmente tem [uma experiência de destinatário de compartilhamento externo ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para compartilhar um documento ou uma pasta que não exige que o usuário tenha uma conta de convidado. Neste caso, um termo de uso não é exibido.

![Painéis de usuários e grupos - Incluir guia com todas as opções de usuários convidados verificadas](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicativos de nuvem (versão prévia)

Termos de uso podem ser usados para diferentes aplicativos de nuvem, como Proteção de Informações do Azure e Microsoft Intune. No momento, esse suporte está na versão prévia.

### <a name="azure-information-protection"></a>Proteção de Informações do Azure

Você pode configurar uma política de acesso condicional para o aplicativo de proteção de informações do Azure e exigir termos de uso quando um usuário acessa um documento protegido. Isso desencadeará um termo de uso antes de um usuário acessar um documento protegido pela primeira vez.

![Painel de aplicativos em nuvem com o aplicativo Microsoft Azure Information Protection selecionado](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registro do Microsoft Intune

Você pode configurar uma política de acesso condicional para o aplicativo Microsoft Intune Registration e exigir um termo de uso antes da inscrição de um dispositivo em Intune. Para obter mais informações, leia [Escolher a solução de Termos correta para a postagem no blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Painel de aplicativos em nuvem com o aplicativo Microsoft Intune selecionado](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> O aplicativo Intune Registration não é suportado para [termos de uso por dispositivo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Como vejo quando/se um usuário aceitou um termo de uso?**<br />
R: Em termos de uso de folha, clique no número em **Aceito**. Também é possível exibir ou pesquisar a atividade de aceitação nos logs de auditoria do Azure AD. Para obter mais informações, confira Exibir relatório de quem aceitou e recusou e [Exibir logs de auditoria do Azure AD](#view-azure-ad-audit-logs).

**P: por quanto tempo as informações ficam armazenadas?**<br />
R: O usuário conta nos termos do relatório de uso e quem aceitou/recusou são armazenados durante a vida útil dos termos de uso. Os logs de auditoria do Azure AD são armazenados por 30 dias.

**P: Por que vejo um número diferente de consentimentos nos termos do relatório de uso versus os registros de auditoria do Azure AD?**<br />
R: O relatório de termos de uso é armazenado durante a vida útil desses termos de uso, enquanto os logs de auditoria Azure AD são armazenados por 30 dias. Além disso, o relatório de termos de uso exibe apenas o estado de consentimento atual dos usuários. Por exemplo, se um usuário recusar e aceitar, o relatório de termos de uso só mostrará a aceitação do usuário. Se você precisar ver o histórico, poderá usar os logs de auditoria do Azure AD.

**P: Se eu editar os detalhes para um termo de uso, isso exigirá que os usuários aceitem novamente?**<br />
R: Não, se um administrador emite os detalhes para um termo de uso (nome, nome de exibição, exige que os usuários expandam ou adicionem um idioma), não é necessário que os usuários reaceitem os novos termos.

**P: Posso atualizar um documento de uso existente?**<br />
A: Atualmente, você não pode atualizar um documento de uso existente. Para alterar um documento de uso, você terá que criar uma nova instância de uso.

**P: Se os hiperlinks estiverem nos termos de uso do documento PDF, os usuários finais poderão clicar neles?**<br />
R: Sim, os usuários finais podem selecionar hiperlinks para páginas adicionais, mas os links para seções dentro do documento não são suportados.

**P: os termos de uso possuem suporte para vários idiomas?**<br />
R: Sim. Atualmente existem 108 idiomas diferentes que um administrador pode configurar para um único termo de uso. Um administrador pode carregar vários documentos em PDF e marcar esses documentos com um idioma correspondente (até 108). Quando os usuários finais entram, examinamos a preferência de idioma do navegador deles e exibimos o documento correspondente. Se não houver nenhuma correspondência, exibimos o documento padrão, que é o primeiro documento carregado.

**P: Quando os termos de uso são acionados?**<br />
R: Os termos de uso são acionados acionada no momento em que o usuário faz o logon.

**P: Para quais aplicativos posso empregar os termos de uso?**<br />
R: Você pode criar uma política de acesso condicional nos aplicativos corporativos usando autenticação moderna. Para obter mais informações, consulte [aplicativos empresariais](./../manage-apps/view-applications-portal.md).

**P: Posso adicionar vários termos de uso para um determinado usuário ou aplicativo?**<br />
R: Sim, criando várias políticas de acesso condicional direcionadas a esses grupos ou aplicativos. Se um usuário cair no escopo de vários termos de uso, ele aceita um termo de uso por vez.

**P: O que acontece se um usuário recusar os termos de uso?**<br />
R: O usuário é impedido de acessar o aplicativo. O usuário precisa entrar novamente e aceitar os termos para obter acesso.

**P: É possível não aceitar um termo de uso que foi previamente aceito?**<br />
R: Você pode [rever os termos de uso anteriormente aceitos,](#how-users-can-review-their-terms-of-use)mas atualmente não há uma maneira de não aceitar.

**P: o que acontece se também usando o Intune termos e condições?**<br />
R: Se você configurou os termos de uso do Azure AD e os termos e condições do [Intune,](/intune/terms-and-conditions-create)o usuário será obrigado a aceitar ambos. Para obter mais informações, consulte a [Escolha da solução de Termos correta para a publicação do seu blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**P: Quais pontos finais os termos de uso do serviço usam para autenticação?**<br />
A: Os termos de uso utilizam os seguintes pontos finais para autenticação: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com. Se sua organização tiver uma lista de permitir URLs para inscrição, você precisará adicionar esses pontos finais à sua lista de desbloqueios, juntamente com os pontos finais do Azure AD para login.

## <a name="next-steps"></a>Próximas etapas

- [Guia de início rápido: Exigem termos de uso para ser aceito antes de acessar a aplicativos de nuvem](require-tou.md)
- [Melhores práticas para acesso condicional no Diretório Ativo do Azure](best-practices.md)
