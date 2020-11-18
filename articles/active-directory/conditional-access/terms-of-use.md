---
title: Termos de uso-Azure Active Directory | Microsoft Docs
description: Comece a usar Azure Active Directory termos de uso para apresentar informações a funcionários ou convidados antes de obter acesso.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c1b07534c702e509b2b664fbee585aa2cff69f6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837593"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory termos de uso

Os termos de uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais. Essa apresentação faz com que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais. Este artigo descreve como começar a usar os termos de uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de visão geral

O vídeo a seguir fornece uma visão geral rápida dos termos de uso.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para ver outros vídeos, consulte:
- [Como implantar os termos de uso no Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como distribuir os termos de uso no Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os termos de uso?

Os termos de uso do Azure AD têm os seguintes recursos:

- Exigir que os funcionários ou convidados aceitem seus termos de uso antes de obter acesso.
- Exigir que os funcionários ou convidados aceitem seus termos de uso em cada dispositivo antes de obter acesso.
- Exigir que os funcionários ou convidados aceitem seus termos de uso em um agendamento recorrente.
- Exigir que os funcionários ou convidados aceitem seus termos de uso antes de registrar as informações de segurança na MFA (autenticação multifator) do Azure AD.
- Exigir que os funcionários aceitem seus termos de uso antes de registrar informações de segurança na redefinição de senha de autoatendimento (SSPR) do Azure AD.
- Apresente os termos de uso gerais para todos os usuários em sua organização.
- Apresentar termos de uso específicos com base em atributos de usuário (por exemplo, médicos, enfermeiras, funcionários locais ou internacionais, usando [grupos dinâmicos](../enterprise-users/groups-dynamic-membership.md)).
- Apresente termos de uso específicos ao acessar aplicativos de alto impacto nos negócios, como o Salesforce.
- Apresente os termos de uso em diferentes idiomas.
- Liste quem tem ou não aceito para seus termos de uso.
- Ajudar a atender às normas de privacidade.
- Exibir um log de atividades de termos de uso para conformidade e auditoria.
- Crie e gerencie os termos de uso usando [APIs de Microsoft Graph](/graph/api/resources/agreement?view=graph-rest-beta) (atualmente em versão prévia).

## <a name="prerequisites"></a>Pré-requisitos

Para usar e configurar os termos de uso do Azure AD, você deve ter:

- Assinatura do Azure AD Premium P1, P2, EMS E3 ou EMS E5.
   - Se você não tiver uma dessas assinaturas, poderá [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [habilitar a avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que você deseja configurar:
   - Administrador global
   - Administrador de Segurança
   - Administrador de Acesso Condicional

## <a name="terms-of-use-document"></a>Documento Termos de uso

Os termos de uso do Azure AD usam o formato PDF para apresentar o conteúdo. O arquivo em PDF pode ter qualquer conteúdo, por exemplo, contratos existentes, o que permite que você colete os contratos do usuário final na entrada do usuário. Para dar suporte a usuários em dispositivos móveis, o tamanho de fonte recomendado em PDF é de 24 pontos.

## <a name="add-terms-of-use"></a>Adicionar termos de uso

Depois de ter finalizado seu documento de termos de uso, use o procedimento a seguir para adicioná-lo.

1. Entre no Azure como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Acesso condicional-folha Termos de uso](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

   ![Novo termo do painel de uso para especificar as configurações dos termos de uso](./media/terms-of-use/new-tou.png)

1. Na caixa **nome** , insira um nome para os termos de uso que serão usados no portal do Azure.
1. Na caixa **Nome de exibição**, insira um título que os usuários visualizam ao entrar.
1. Para **termos de uso documento**, navegue até o PDF de termos de uso finalizado e selecione-o.
1. Selecione o idioma do documento dos termos de uso. A opção de idioma permite carregar vários termos de uso, cada um com um idioma diferente. A versão dos termos de uso que um usuário final verá terá base em suas preferências de navegador.
1. Para exigir que os usuários finais exibam os termos de uso antes de aceitá-los, defina **exigir que os usuários expandam os termos de uso** para **ativado**.
1. Para exigir que os usuários finais aceitem os termos de uso em cada dispositivo do qual estão acessando, defina **exigir que os usuários consigam em cada dispositivo** para **o.** Os usuários podem ser solicitados a instalar aplicativos adicionais se essa opção estiver habilitada. Para obter mais informações, consulte [termos de uso por dispositivo](#per-device-terms-of-use).
1. Se você quiser expirar os termos de uso de consentir de acordo com uma agenda, defina **expirar consentir** como **ativado**. Quando definido como On, duas configurações de cronograma adicionais são exibidas.

   ![Expirar as configurações para definir a data de início, a frequência e a duração](./media/terms-of-use/expire-consents.png)

1. Use as configurações de **expiração de início** e de **frequência** para especificar o agendamento de expirações de termos de uso. A tabela a seguir mostra o resultado para duas configurações de exemplo:

   | Expirar a partir de | Frequência | Resultado |
   | --- | --- | --- |
   | Data de hoje  | Mensal | A partir de hoje, os usuários devem aceitar os termos de uso e, em seguida, reaceitar todos os meses. |
   | Data no futuro  | Mensal | A partir de hoje, os usuários devem aceitar os termos de uso. Quando a data futura chegar, os consentimentos irão expirar e depois os usuários devem aceitar novamente a todos os meses.  |

   Por exemplo, se você definir a expiração a partir da data para **1 de janeiro** e a frequência para **Mensal**, segue como ocorreriam as expirações para dois usuários:

   | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |
   | Bob | 15 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |

1. Use a configuração **duração antes da reaceitação requer (dias)** para especificar o número de dias antes que o usuário precise aceitar os termos de uso novamente. Isso permite que os usuários sigam seu próprio cronograma. Por exemplo, se você definir a duração como **30** dias, segue como ocorreriam as expirações para dois usuários:

   | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de janeiro | 31 de janeiro | 2 de março | 1 de abril |
   | Bob | 15 de janeiro | 14 de fevereiro | 16 de março | 15 de abril |

   É possível usar as configurações **Expirar consentimentos** e **Duração (dias) até exigir a nova aceitação** juntas, mas normalmente você usa uma ou a outra.

1. Em **acesso condicional**, use a lista **impor com modelo de política de acesso condicional** para selecionar o modelo para impor os termos de uso.

   ![Lista suspensa acesso condicional para selecionar um modelo de política](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | Descrição |
   | --- | --- |
   | **Acesso a aplicativos de nuvem para todos os convidados** | Uma política de acesso condicional será criada para todos os convidados e todos os aplicativos de nuvem. Essa política afeta o portal do Azure. Após ela ser criada, talvez seja necessário sair e entrar novamente. |
   | **Acesso a aplicativos de nuvem para todos os usuários** | Uma política de acesso condicional será criada para todos os usuários e todos os aplicativos de nuvem. Essa política afeta o portal do Azure. Após ela ser criada, será necessário sair e entrar novamente. |
   | **Política personalizada** | Selecione os usuários, grupos e aplicativos aos quais esses termos de uso serão aplicados. |
   | **Criar política de acesso condicional mais tarde** | Esses termos de uso aparecerão na lista de controle de concessão ao criar uma política de acesso condicional. |

   >[!IMPORTANT]
   >Controles de política de acesso condicional (incluindo termos de uso) não dão suporte à imposição em contas de serviço. É recomendável excluir todas as contas de serviço da política de acesso condicional.

    As políticas de acesso condicional personalizadas permitem termos de uso granulares, até um aplicativo de nuvem ou grupo de usuários específico. Para obter mais informações, consulte [início rápido: exigir que os termos de uso sejam aceitos antes de acessar aplicativos de nuvem](require-tou.md).

1. Clique em **Criar**.

   Se você selecionou um modelo de acesso condicional personalizado, uma nova tela será exibida, permitindo que você crie a política de acesso condicional personalizada.

   ![Novo painel de acesso condicional se você escolheu o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Agora você deve ver seus novos termos de uso.

   ![Novos termos de uso listados na folha termos de uso](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Exibir relatório de quem aceitou e recusou

A folha Termos de uso mostra uma contagem de usuários que os aceitaram e recusaram. Essas contagens e quem aceitou/recusou são armazenados durante a vida útil dos termos de uso.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Termos de uso folha listando o número de usuários mostrar foram aceitos e recusados](./media/terms-of-use/view-tou.png)

1. Para obter os termos de uso, clique nos números em **aceito** ou **recusado** para exibir o estado atual dos usuários.

   ![Termos de uso painel consentis listando os usuários que aceitaram](./media/terms-of-use/accepted-tou.png)

1. Para exibir o histórico de um usuário individual, clique no botão de reticências (**...**) e, em seguida **Exibir histórico**.

   ![Exibir o menu de contexto do histórico para um usuário](./media/terms-of-use/view-history-menu.png)

   No painel de exibição de histórico, você vê um histórico de todos os aceites, recusas e expirações.

   ![Painel exibir histórico lista o histórico aceita, recusa e expirações de um usuário](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Exibir logs de auditoria do Azure AD

Se você quiser exibir atividades adicionais, os termos de uso do Azure AD incluirão logs de auditoria. Cada consentimento do usuário dispara um evento nos logs de auditoria que ficam armazenados durante **30 dias**. Você pode exibir esses logs no portal ou baixá-los como um arquivo .csv.

Para começar a usar os logs de auditoria do Microsoft Azure AD, use o procedimento a seguir:

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso.
1. Clique em **Exibir logs de auditoria**.

   ![Termos de uso folha com a opção Exibir logs de auditoria realçada](./media/terms-of-use/audit-tou.png)

1. Na tela de logs de auditoria do Azure AD, é possível filtrar as informações usando as listas fornecidas para buscar informações de log de auditoria específico.

   Você também pode clicar em **Download** para baixar as informações em um arquivo .csv para uso local.

   ![Lista de logs de auditoria do Azure AD data de listagem, política de destino, iniciada por e atividade](./media/terms-of-use/audit-logs-tou.png)

   Se você clicar em um log, um painel é exibido com detalhes adicionais da atividade.

   ![Detalhes da atividade para um log que mostra a atividade, o status da atividade, iniciado por, política de destino](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>A aparência dos termos de uso para os usuários

Depois que os termos de uso forem criados e impostos, os usuários, que estão no escopo, verão a tela a seguir durante a entrada.

![Exemplo de termos de uso que aparece quando um usuário entra](./media/terms-of-use/user-tou.png)

Os usuários podem exibir os termos de uso e, se necessário, usar os botões para ampliar e reduzir.

![Exibição dos termos de uso com botões de zoom](./media/terms-of-use/zoom-buttons.png)

A tela a seguir mostra como os termos de uso são examinados em dispositivos móveis.

![Exemplo de termos de uso que aparece quando um usuário entra em um dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os usuários só precisam aceitar os termos de uso uma vez e não verão os termos de uso novamente em entradas subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os usuários podem revisar seus termos de uso

Os usuários podem examinar e ver os termos de uso que aceitaram usando o procedimento a seguir.

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, clique em seu nome e selecione **Perfil**.

   ![Site myapps com o painel do usuário aberto](./media/terms-of-use/tou14.png)

1. Na página de seu perfil, clique em **Analisar termos de uso**.

   ![Página de perfil para um usuário que mostra o link revisar termos de uso](./media/terms-of-use/tou13a.png)

1. A partir daí, você pode revisar os termos de uso aceitos.

## <a name="edit-terms-of-use-details"></a>Editar detalhes dos termos de uso

Você pode editar alguns detalhes dos termos de uso, mas não pode modificar um documento existente. O procedimento a seguir descreve como editar os detalhes.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja editar.
1. Clique em **editar termos**.
1. Em termos de edição do painel de uso, altere o nome, nome de exibição ou exigir que os usuários expandam os valores.

   Se houver outras configurações que você queira alterar, como documento PDF, exigir que os usuários consigam em cada dispositivo, expirem consentidos, duração antes da reaceitação ou política de acesso condicional, você deve criar novos termos de uso.

   ![Editar o painel termos de uso mostrando as opções nome e expandir](./media/terms-of-use/edit-tou.png)

1. Clique em **Salvar** para salvar as alterações.

   Depois de salvar as alterações, os usuários terão que aceitar novamente essas edições.

## <a name="add-a-terms-of-use-language"></a>Adicionar um idioma de termos de uso

O procedimento a seguir descreve como adicionar um idioma de termos de uso.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja editar.
1. No painel de detalhes, clique na guia **Idiomas**.

   ![Termos de uso selecionado e mostrando a guia idiomas no painel de detalhes](./media/terms-of-use/languages-tou.png)

1. Clique em **Adicionar idioma**.
1. Em termos do painel de linguagem do uso de Add, carregar o PDF localizado e selecione o idioma.

   ![Adicionar o painel de idiomas termos de uso com opções para carregar PDFs localizados](./media/terms-of-use/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de uso por dispositivo

A configuração **exigir que os usuários consentim em cada dispositivo** permite que você exija que os usuários finais aceitem os termos de uso em cada dispositivo do qual estão acessando. O usuário final será solicitado a registrar seu dispositivo no Azure AD. Quando o dispositivo é registrado, a ID do dispositivo é usada para impor os termos de uso em cada dispositivo.

Segue uma lista dos softwares e plataformas com suporte.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outro |
> | --- | --- | --- | --- | --- |
> | **Aplicativo nativo** | Sim | Sim | Sim |  |
> | **Microsoft Edge** | Sim | Sim | Sim |  |
> | **Internet Explorer** | Sim | Sim | Sim |  |
> | **Chrome (com extensão)** | Sim | Sim | Sim |  |

Os termos de uso por dispositivo têm as seguintes restrições:

- Um dispositivo somente pode ser unido a um locatário.
- Um usuário deve ter permissões para ingressar seu dispositivo.
- Não há suporte para o aplicativo de registro do Intune. Certifique-se de que ele seja excluído de qualquer política de acesso condicional que exija os termos de uso.
- Não há suporte para usuários B2B do Azure AD.

Se o dispositivo do usuário não estiver ingressado, ele receberá uma mensagem de que é necessário ingressar o dispositivo dele. A experiência dele será dependente da plataforma e do software.

### <a name="join-a-windows-10-device"></a>Ingressar um dispositivo com Windows 10

Se um usuário estiver usando o Windows 10 e o Microsoft Edge, ele receberá uma mensagem semelhante à seguinte para [ingressar o dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge-mensagem indicando que o dispositivo deve ser registrado](./media/terms-of-use/per-device-win10-edge.png)

Se estiver usando o Chrome, o usuário será solicitado a instalar a [Extensão de Contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrar um dispositivo iOS

Se um usuário estiver usando um dispositivo iOS, será solicitado que ele instale o [aplicativo Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registrar um dispositivo Android

Se um usuário estiver usando um dispositivo Android, será solicitado que ele instale o [aplicativo Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Navegadores

Se um usuário estiver usando um navegador sem suporte, ele será solicitado a usar um navegador diferente.

![Mensagem indicando que o dispositivo deve ser registrado, mas não há suporte para o navegador](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Excluir termos de uso

Você pode excluir os termos de uso antigos usando o procedimento a seguir.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja remover.
1. Clique em **Excluir termos**.
1. Na mensagem que será exibida perguntando se deseja continuar, clique em **Sim**.

   ![Mensagem solicitando a confirmação para excluir os termos de uso](./media/terms-of-use/delete-tou.png)

   Você não deve mais ver seus termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Usuários excluídos e termos de uso ativos

Por padrão, um usuário excluído permanece excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador, se necessário. Após 30 dias, esse usuário será excluído permanentemente. Além disso, usando o portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir permanentemente um usuário excluído recentemente](../fundamentals/active-directory-users-restore.md) antes de atingir o período de tempo. Um usuário foi excluído permanentemente, os dados subsequentes sobre esse usuário serão removidos dos termos de uso ativos. Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="policy-changes"></a>Alterações na política

As políticas de acesso condicional entram em vigor imediatamente. Quando isso acontece, o administrador começa a ver uma imagem de "nuvens tristes" ou "problemas de token do Azure AD". O administrador precisa sair e entrar novamente para atender à nova política.

> [!IMPORTANT]
> Os usuários que estão no escopo precisarão sair e entrar novamente para atender a uma nova política se:
>
> - uma política de acesso condicional está habilitada em termos de uso
> - ou se outros termos de uso forem criados

## <a name="b2b-guests-preview"></a>Convidados B2B (versão prévia)

A maioria das organizações tem um processo em vigor para seus funcionários consentirem com os termos de uso e as declarações de privacidade de sua organização. Mas como você pode impor os mesmos consentimentos para convidados B2B (business-to-business) do Azure AD quando eles forem adicionados por meio do SharePoint ou Teams? Usando o acesso condicional e os termos de uso, você pode impor uma política diretamente para usuários de convidados B2B. Durante o fluxo de resgate de convite, o usuário recebe os termos de uso. No momento, esse suporte está na versão prévia.

Termos de uso só serão exibidos quando o usuário tiver uma conta de convidado no Azure AD. O SharePoint Online atualmente tem uma [experiência de destinatário de compartilhamento externo ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para compartilhar um documento ou uma pasta que não exige que o usuário tenha uma conta de convidado. Nesse caso, os termos de uso não são exibidos.

![Painel usuários e grupos – incluir guia com todos os usuários convidados opção marcada](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicativos de nuvem (versão prévia)

Termos de uso podem ser usados para diferentes aplicativos de nuvem, como Proteção de Informações do Azure e Microsoft Intune. No momento, esse suporte está na versão prévia.

### <a name="azure-information-protection"></a>Proteção de Informações do Azure

Você pode configurar uma política de acesso condicional para o aplicativo de proteção de informações do Azure e exigir os termos de uso quando um usuário acessa um documento protegido. Isso irá disparar os termos de uso antes de um usuário acessar um documento protegido pela primeira vez.

![Painel de aplicativos de nuvem com o aplicativo Proteção de Informações do Microsoft Azure selecionado](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registro do Microsoft Intune

Você pode configurar uma política de acesso condicional para o aplicativo de registro de Microsoft Intune e exigir os termos de uso antes do registro de um dispositivo no Intune. Para obter mais informações, leia [Escolher a solução de Termos correta para a postagem no blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Painel de aplicativos de nuvem com o aplicativo Microsoft Intune selecionado](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> O aplicativo de registro do Intune não tem suporte para os [termos de uso por dispositivo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Como fazer ver quando/se um usuário aceitou os termos de uso?**<br />
R: Em termos de uso de folha, clique no número em **Aceito**. Também é possível exibir ou pesquisar a atividade de aceitação nos logs de auditoria do Azure AD. Para obter mais informações, confira Exibir relatório de quem aceitou e recusou e [Exibir logs de auditoria do Azure AD](#view-azure-ad-audit-logs).

**P: por quanto tempo as informações ficam armazenadas?**<br />
R: o usuário conta no relatório termos de uso e quem aceitou/recusou são armazenados durante a vida útil dos termos de uso. Os logs de auditoria do Azure AD são armazenados por 30 dias.

**P: por que vejo um número diferente de consentidos no relatório termos de uso versus nos logs de auditoria do Azure AD?**<br />
R: o relatório de termos de uso é armazenado durante o tempo de vida dos termos de uso, enquanto os logs de auditoria do Azure AD são armazenados por 30 dias. Além disso, o relatório termos de uso exibe apenas o estado de consentimento atual dos usuários. Por exemplo, se um usuário recusar e, em seguida, aceitar, o relatório de termos de uso mostrará apenas a aceitação desse usuário. Se você precisar ver o histórico, poderá usar os logs de auditoria do Azure AD.

**P: se eu editar os detalhes para os termos de uso, ele exigirá que os usuários aceitem novamente?**<br />
R: não, se um administrador editar os detalhes de um termo de uso (nome, nome de exibição, exigir que os usuários expandam ou adicionem um idioma), ele não exigirá que os usuários aceitem novamente os novos termos.

**P: posso atualizar um documento de termos de uso existente?**<br />
R: atualmente, você não pode atualizar um documento de termos de uso existente. Para alterar um documento de termos de uso, você precisará criar uma nova instância de termos de uso.

**P: se os hiperlinks estiverem no documento PDF termos de uso, os usuários finais poderão clicar neles?**<br />
R: Sim, os usuários finais podem selecionar hiperlinks para páginas adicionais, mas não há suporte para links para seções dentro do documento. Além disso, os hiperlinks em termos de uso de PDFs não funcionam quando acessados no portal de myapps/minha conta do Azure AD.

**P: os termos de uso possuem suporte para vários idiomas?**<br />
A: Sim. Atualmente, há 108 diferentes idiomas que um administrador pode configurar para um único termo de uso. Um administrador pode carregar vários documentos em PDF e marcar esses documentos com um idioma correspondente (até 108). Quando os usuários finais entram, examinamos a preferência de idioma do navegador deles e exibimos o documento correspondente. Se não houver nenhuma correspondência, exibimos o documento padrão, que é o primeiro documento carregado.

**P: quando os termos de uso são disparados?**<br />
R: Os termos de uso são acionados acionada no momento em que o usuário faz o logon.

**P: Para quais aplicativos posso empregar os termos de uso?**<br />
R: você pode criar uma política de acesso condicional nos aplicativos empresariais usando a autenticação moderna. Para obter mais informações, consulte [aplicativos empresariais](./../manage-apps/view-applications-portal.md).

**P: Posso adicionar vários termos de uso para um determinado usuário ou aplicativo?**<br />
R: Sim, criando várias políticas de acesso condicional direcionadas a esses grupos ou aplicativos. Se um usuário estiver no escopo de vários termos de uso, eles aceitarão um termo de uso de cada vez.

**P: O que acontece se um usuário recusar os termos de uso?**<br />
R: O usuário é impedido de acessar o aplicativo. O usuário precisa entrar novamente e aceitar os termos para obter acesso.

**P: é possível não aceitar os termos de uso que foram aceitos anteriormente?**<br />
R: você pode [revisar os termos de uso aceitos anteriormente](#how-users-can-review-their-terms-of-use), mas atualmente não há uma maneira de não aceitar.

**P: o que acontece se também usando o Intune termos e condições?**<br />
R: se você tiver configurado os termos de uso do Azure AD e os [termos e condições do Intune](/intune/terms-and-conditions-create), o usuário será solicitado a aceitar ambos. Para obter mais informações, consulte a [Escolha da solução de Termos correta para a publicação do seu blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**P: quais pontos de extremidade os termos de uso do serviço usam para autenticação?**<br />
R: Termos de uso utiliza os seguintes pontos de extremidade para autenticação: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com . Se sua organização tiver uma lista de permissões de URLs para registro, você precisará adicionar esses pontos de extremidade à sua lista de permissões, juntamente com os pontos de extremidade do Azure AD para entrar.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Exigir a aceitação dos termos de uso antes de acessar os aplicativos de nuvem](require-tou.md)