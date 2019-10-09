---
title: Solução de problemas de redefinição de senha por autoatendimento-Azure Active Directory
description: Solução de problemas de autoatendimento de redefinição de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4f236f2f2fdbf2736f87f754f48387f9f41850d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024627"
---
# <a name="troubleshoot-self-service-password-reset"></a>Solucionar problemas de autoatendimento de redefinição de senha

Você está tendo um problema com a SSPR (redefinição de senha de autoatendimento) do Azure AD (Azure Active Directory)? As informações a seguir podem ajudá-lo a fazer as coisas funcionarem novamente.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Como solucionar problemas de erros de redefinição de senha de autoatendimento que um usuário pode ver

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Não é possível redefinir sua senha neste momento porque o administrador desabilitou a redefinição de senha para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite a habilitação deste recurso. Para saber mais, confira [Preciso de ajuda, esqueci a minha senha do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Detectamos que a redefinição de senha não foi habilitada pelo administrador. Entre em contato com o seu administrador e solicite a habilitação da redefinição de senha para a sua organização. |
| WritebackNotEnabled = 10 |Não é possível redefinir sua senha neste momento porque o administrador não habilitou um serviço necessário para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a verificação da configuração da sua organização. Para saber mais sobre o serviço necessário, confira [Como configurar o write-back de senha](howto-sspr-writeback.md). | SSPR_0010: Detectamos que o write-back de senha não foi habilitado. Entre em contato com o seu administrador e solicite a habilitação do write-back de senha. |
| SsprNotEnabledInUserPolicy = 11 | Não é possível redefinir sua senha neste momento porque o administrador não configurou a redefinição de senha para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a configuração da redefinição de senha. Para saber mais sobre a configuração da redefinição de senha, confira [Início rápido: Redefinição de senha self-service do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Sua organização não tem uma política de redefinição de senha definida. Entre em contato com seu administrador e solicite a implantação de uma política de redefinição de senha. |
| UserNotLicensed = 12 | Não é possível redefinir sua senha neste momento porque a sua organização não tem as licenças necessárias. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite que verifique sua atribuição de licença. Para saber mais sobre requisitos de licenciamento, confira os [Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Sua organização não tem as licenças obrigatórias necessárias para realizar a redefinição de senha. Entre em contato com seu administrador e solicite a revisão das atribuições de licença. |
| UserNotMemberOfScopedAccessGroup = 13 | Não é possível redefinir sua senha neste momento porque o administrador não configurou sua conta para usar a redefinição de senha. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite a configuração da sua conta para redefinição de senha. Para saber mais sobre a configuração de conta para redefinição de senha, confira [Como executar a redefinição de senha para usuários](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Você não é membro de um grupo habilitado para a redefinição de senha. Entre em contato com o administrador e peça para ser adicionado ao grupo. |
| UserNotProperlyConfigured = 14 | Não é possível redefinir sua senha neste momento porque a sua conta não tem as informações necessárias. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a redefinição da sua senha. Depois de ter acesso à sua conta novamente, você precisará registrar as informações necessárias. Para registrar informações, siga as etapas no artigo [Registro de redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register). | SSPR_0014: São necessárias informações de segurança adicionais para redefinir a senha. Para continuar, entre em contato com o seu administrador e solicite a redefinição da sua senha. Depois de acessar sua conta, registre as informações de segurança adicionais em https://aka.ms/ssprsetup. O administrador pode adicionar informações de segurança adicionais à sua conta seguindo as etapas em [Configuração e leitura de dados de autenticação para redefinição de senha](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Não é possível redefinir a sua senha no momento devido a um problema com a configuração de redefinição de senha da sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite uma investigação. Para saber mais sobre o problema, confira [Solucionar problemas de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Não é possível redefinir a senha devido a um erro na configuração local. Entre em contato com seu administrador e solicite uma investigação. |
| OnPremisesConnectivityError = 30 | Não é possível redefinir a sua senha no momento devido a problemas de conectividade para a sua organização. Não há nenhuma ação a ser executada no momento, mas o problema pode ser resolvido se você tentar novamente mais tarde. Se o problema persistir, entre em contato com o seu administrador e solicite uma investigação. Para saber mais sobre problemas de conectividade, confira [Solução de problemas de conectividade de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Não é possível redefinir a senha devido a uma conexão fraca com o ambiente local. Entre em contato com seu administrador e solicite uma investigação.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Solução de problemas de configuração de redefinição de senha no portal do Azure

| Erro | Solução |
| --- | --- |
| Não consigo ver a seção **Redefinição de Senha** em Azure AD no portal do Azure. | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Atribua uma licença à conta de administrador em questão. Você pode seguir as etapas do artigo [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).|
| Não consigo ver determinada opção de configuração. | Muitos elementos da interface do usuário ficam ocultos até que sejam necessários. Tente habilitar todas as opções que você deseja ver. |
| Não consigo ver a guia **Integração local**. | Essa opção só fica visível se você baixou o Azure AD Connect e configurou o write-back de senha. Para saber mais, confira [Guia de introdução ao Azure AD Connect usando as configurações express](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Solução de problemas de relatório de redefinição de senha

| Erro | Solução |
| --- | --- |
| Não consigo ver nenhum tipo de atividade de gerenciamento de senhas na categoria de evento de auditoria **Gerenciamento de Senhas de Autoatendimento**. | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Para resolver esse problema, você pode atribuir uma licença à conta de administrador em questão. Siga as etapas no artigo [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| Os registros do usuário aparecem várias vezes. | Quando um usuário se registra, registramos cada dado individual que é registrado como um evento separado. <br> <br> Se você quiser agregar esses dados e ter maior flexibilidade em como exibi-los, poderá baixar o relatório e abrir os dados como uma tabela dinâmica no Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Solucionar problemas do portal de registro de redefinição de senha

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para redefinição de senha. **O administrador não habilitou você para usar esse recurso.** | Alterne o sinalizador **Autoatendimento de redefinição de senha habilitado** para **Selecionado** ou **Todos** e selecione **Salvar**. |
| O usuário não tem uma licença do Azure AD atribuída. **O administrador não habilitou você para usar esse recurso.** | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Para resolver esse problema, você pode atribuir uma licença à conta de administrador em questão. Siga as etapas no artigo [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).|
| Há um erro ao processar a solicitação. | Isso pode ser causado por vários problemas, mas, em geral, esse erro é causado por uma interrupção do serviço ou um problema de configuração. Se você receber esse erro e ele estiver afetando seus negócios, contate o suporte da Microsoft para obter assistência adicional. |

## <a name="troubleshoot-the-password-reset-portal"></a>Solucionar problemas do portal de redefinição de senha

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para redefinição de senha. | Alterne o sinalizador **Autoatendimento de redefinição de senha habilitado** para **Selecionado** ou **Todos** e selecione **Salvar**. |
| O usuário não tem uma licença do Azure AD atribuída. | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Você pode resolver esse problema atribuindo uma licença à conta de administrador em questão. Siga as etapas no artigo [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| O diretório está habilitado para redefinição de senha, mas o usuário tem informações de autenticação ausentes ou incorretas. | Antes de continuar, verifique se o usuário preencheu corretamente os dados de contato no arquivo no diretório. Para obter mais informações, consulte [Dados usados pelo autoatendimento de redefinição de senha do Azure AD](howto-sspr-authenticationdata.md). |
| O diretório está habilitado para redefinição de senha, mas o usuário tem somente uma parte dos dados de contato no arquivo quando a política está definida para exigir dois métodos de verificação. | Antes de prosseguir, verifique se o usuário tem pelo menos dois métodos de contato configurados corretamente. Um exemplo é ter um número de telefone celular *e* um número de telefone comercial. |
| O diretório está habilitado para redefinição de senha e o usuário está configurado corretamente, mas não pode ser contatado. | Isso pode ser o resultado de um erro de serviço temporário ou de dados de contato incorretos que não podemos detectar corretamente. <br> <br> Se o usuário aguardar 10 segundos, surgirão os links “tente novamente” e "entre em contato com seu administrador". Se o usuário selecionar "tente novamente", uma nova chamada será feita. Se o usuário selecionar "entre em contato com seu administrador", ele envia um email de formulário de email aos administradores solicitando a execução de uma redefinição de senha para essa conta de usuário. |
| O usuário nunca recebe a chamada telefônica ou redefinição de senha de SMS. | Isso pode ser o resultado de um número de telefone incorreto no diretório. Verifique se o número de telefone está no formato "+ ccc xxxyyyzzzzXeeee". <br> <br> A redefinição de senha não dá suporte a ramais, mesmo se você especificar um no diretório. As extensões são removidas antes que a chamada seja realizada. Use um número sem ramal ou integre o ramal do número de telefone à sua central privada (PBX). |
| O usuário nunca recebe o email de redefinição de senha. | A causa mais comum desse problema é que a mensagem foi rejeitada por um filtro de spam. Verifique o email no spam, na pasta Lixo eletrônico ou nos itens de email excluídos. <br> <br> Além disso, veja se você está verificando a conta de email correta para a mensagem. |
| Defini uma política de redefinição de senha, mas quando uma conta de administrador usa a redefinição de senha, a política não é aplicada. | A Microsoft gerencia e controla a política de redefinição de senha do administrador para garantir o nível mais alto de segurança. |
| O usuário foi impedido de tentar a redefinição de senha muitas vezes em um mesmo dia. | Implementamos um mecanismo de limitação automático para impedir que os usuários tentem redefinir suas senhas muitas vezes em um curto período de tempo. A limitação ocorre quando: <br><ul><li>O usuário tenta validar um número de telefone cinco vezes em uma hora.</li><li>O usuário tenta usar o portão de perguntas de segurança cinco vezes em uma hora.</li><li>O usuário tenta redefinir uma senha para a mesma conta de usuário cinco vezes em uma hora.</li></ul>Para corrigir esse problema, instrua o usuário a aguardar 24 horas após a última tentativa. O usuário poderá, então, redefinir a senha. |
| O usuário vê um erro ao validar seu número de telefone. | Esse erro ocorre quando o número de telefone digitado não coincide com o número de telefone no arquivo. Verifique se o usuário está inserindo o número de telefone completo, incluindo o código de área e país, ao tentar usar um método baseado em telefone para a redefinição de senha. |
| Há um erro ao processar a solicitação. | Isso pode ser causado por vários problemas, mas, em geral, esse erro é causado por uma interrupção do serviço ou um problema de configuração. Se você receber esse erro e ele estiver afetando seus negócios, contate o suporte da Microsoft para obter assistência adicional. |
| Violação de política local | A senha não atende à política de senha do Active Directory local. |
| A senha não é compatível com a política difusa | A senha que foi usada aparece na [lista de senhas banidas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) e não pode ser usada. |

## <a name="troubleshoot-password-writeback"></a>Solução de problemas de write-back de senha

| Erro | Solução |
| --- | --- |
| O serviço de redefinição de senha não é iniciado no local. O Erro 6800 aparece no log de eventos do aplicativo do computador do Azure AD Connect. <br> <br> Após a integração, os usuários federados, com autenticação de passagem ou sincronizados com hash de senha não conseguem redefinir suas senhas. | Quando o write-back de senha é habilitado, o mecanismo de sincronização chama a biblioteca de write-back para realizar a configuração (integração) comunicando-se com o serviço de integração em nuvem. Os erros encontrados durante a integração ou ao iniciar o ponto de extremidade do WCF (Windows Communication Foundation) para o write-back de senha resultam em erros no log de eventos e no computador do Azure AD Connect. <br> <br> Durante a reinicialização do serviço ADSync (Sincronização do Azure AD), se o write-back tiver sido configurado, o ponto de extremidade do WCF será inicializado. No entanto, se a inicialização do ponto de extremidade falhar, registraremos o evento 6800 e permitiremos a inicialização do serviço de sincronização. A presença desse evento significa que o ponto de extremidade de write-back de senha de não foi iniciado. Os detalhes do log de eventos desse evento (6800) juntamente com as entradas do log de eventos geradas pelo componente PasswordResetService indicam por que não é possível iniciar o ponto de extremidade. Examine esses erros do log de eventos e tente reiniciar o Azure AD Connect se o write-back de senha ainda não estiver funcionando. Se o problema persistir, tente desabilitar e reabilitar o write-back de senha.
| Quando um usuário tenta redefinir uma senha ou desbloquear uma conta com write-back de senha habilitada, a operação falha. <br> <br> Além disso, você vê um evento no log de eventos do Azure AD Connect que contém: “O Mecanismo de Sincronização retornou um erro hr=800700CE, message=O nome de arquivo ou a extensão é muito longo” após a operação de desbloqueio. | Localize a conta de Active Directory para Azure AD Connect e redefina a senha para que ela contenha no máximo 256 caracteres. Em seguida, abra o **Serviço de Sincronização** no menu **Iniciar**. Navegue para **Conectores** e localize o **Active Directory Connector**. Selecione-o e, em seguida, selecione **Propriedades**. Navegue até a página **Credenciais** e digite a nova senha. Selecione **OK** para fechar a página. |
| Na última etapa do processo de instalação do Azure AD Connect, você verá um erro que indica que não é possível configurar o write-back de senha. <br> <br> O log de eventos do aplicativo Azure AD Connect contém erro 32009 com texto "Erro ao obter token de autenticação". | Esse erro ocorre nos seguintes casos: <br><ul><li>Você especificou uma senha incorreta para a conta de administrador global indicada no início do processo de instalação do Azure AD Connect.</li><li>Você tentou usar um usuário federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li></ul> Para corrigir esse problema, verifique se você não está usando uma conta federada para o administrador global especificado no início do processo de instalação. Além disso, verifique se a senha especificada está correta. |
| O log de eventos do computador do Azure AD Connect contém o erro 32002, lançado pela execução de PasswordResetService. <br> <br> O erro indica: “Erro ao se conectar ao ServiceBus. O provedor de token não pôde fornecer um token de segurança.” | O ambiente local não pode se conectar ao ponto de extremidade do Barramento de Serviço do Azure na nuvem. Esse erro normalmente é causado por uma regra de firewall que bloqueia uma conexão de saída com uma porta ou um endereço web específico. Consulte [Pré-requisitos de conectividade](../hybrid/how-to-connect-install-prerequisites.md) para saber mais. Depois de atualizar essas regras, reinicialize o computador do Azure AD Connect e o write-back de senha deverá começar a funcionar novamente. |
| Após trabalhar um pouco, os usuários federados, com autenticação de passagem ou sincronizados com hash de senha não conseguem redefinir suas senhas. | Em alguns casos raros, o serviço write-back de senha poderá falhar ao reiniciar quando o Azure AD Connect for reiniciado. Nesses casos, primeiro verifique se o write-back de senha parece estar habilitado no local. Você pode verificar usando o Assistente de Conexão do Azure AD ou o PowerShell (consulte a seção de tutoriais anterior). Se o recurso parece estar ativado, tente habilitá-lo ou desabilitá-lo novamente por meio da interface do usuário ou do PowerShell. Se isso não funcionar, tente uma desinstalação completa e reinstale o Azure AD Connect. |
| Os usuários federados, com autenticação de passagem ou sincronizados por hash de senha que tentam redefinir suas senhas veem um erro quando tentam enviar a senha. O erro indica que houve um problema de serviço. <br ><br> Além disso, durante as operações de redefinição de senha, você pode ver um erro sobre o agente de gerenciamento ter acesso negado em seus logs de eventos no local. | Se você vir esses erros no log de eventos, confirme se a conta do ADMA (Agente de Gerenciamento do Active Directory) que foi especificada no assistente no momento da configuração tem as permissões necessárias para o write-back de senha. <br> <br> Após a permissão ser concedida, poderá levar até uma hora para que as permissões sejam repassadas pela tarefa em segundo plano `sdprop` no DC (controlador de domínio). <br> <br> Para que a redefinição de senha funcione, a permissão deve ser marcada no descritor de segurança do objeto do usuário cuja senha esteja sendo redefinida. Até que essa permissão seja exibida no objeto do usuário, a redefinição de senha continuará falhando com uma mensagem de acesso negado. |
| Os usuários federados, com autenticação de passagem ou sincronizados por hash de senha que tentam redefinir suas senhas veem um erro quando enviam a senha. O erro indica que houve um problema de serviço. <br> <br> Além disso, durante as operações de redefinição de senha, você verá um erro em seus logs de eventos do serviço do Azure AD Connect indicando o erro "Objeto não encontrado". | Esse erro geralmente indica que o mecanismo de sincronização não consegue encontrar o objeto de usuário no espaço do conector do Azure AD ou o objeto de espaço do conector do Azure AD ou MV (metaverso) vinculado. <br> <br> Para resolver esse problema, verifique se o usuário está realmente sincronizado do local para o Azure AD pela instância atual do Azure AD Connect e inspecione o estado dos objetos nos espaços conectores e no MV. Confirme se o objeto AD CS (Serviços de Certificados do Active Directory) está conectado ao objeto MV por meio da regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Os usuários federados, com autenticação de passagem ou sincronizados por hash de senha que tentam redefinir suas senhas veem um erro quando enviam a senha. O erro indica que houve um problema de serviço. <br> <br> Além disso, durante as operações de redefinição de senha, você poderá ver um erro nos logs de eventos do serviço Azure AD Connect indicando o erro “Várias correspondências encontradas”. | Isso indica que o mecanismo de sincronização detectou que o objeto de MV está conectado a mais de um objeto de AD CS através de "Microsoft.InfromADUserAccountEnabled.xxx". Isso significa que o usuário tem uma conta habilitada em mais de uma floresta. Não há suporte para write-back de senha nesse cenário. |
| Falha nas operações de senha com um erro de configuração. O log de eventos do aplicativo contém o erro Azure AD Connect 6329 com o texto “0x8023061f (a operação falhou porque a sincronização de senha não está habilitada nesse agente de gerenciamento)”. | Esse erro ocorre quando a configuração do Azure AD Connect é alterada para adicionar uma nova floresta do Active Directory (ou para remover e adicionar novamente uma floresta existente) após o recurso de write-back de senha já ter sido habilitado. As operações de senha para usuários em florestas recém-adicionadas como essas falharão. Para corrigir o problema, desabilite e reabilite o recurso de write-back de senha após a conclusão das alterações de configuração da floresta. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro do log de eventos de write-back de senha

Uma prática recomendada ao solucionar problemas com o Write-back de senha é inspecionar o log de eventos do aplicativo em seu computador Azure AD Connect. Esse log de eventos contém eventos de duas fontes de interesse para o write-back de senha. A fonte PasswordResetService descreve operações e problemas relacionados à operação de write-back de senha. A fonte ADSync descreve operações e problemas relacionados à configuração de senhas no ambiente do Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se a origem do evento é ADSync

| Código | Nome ou mensagem | DESCRIÇÃO |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: “Uma restrição impede que a senha seja alterada para a atual especificada.” | Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha no diretório local que não atende à idade, ao histórico, à complexidade da senha ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado a senha recentemente nessa janela de tempo, não poderá alterar a senha novamente até que ela atinja a duração especificada no domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deve selecionar uma senha que não foi usada nas últimas *X* vezes, em que *X* é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas *X* vezes, verá uma falha. Para fins de teste, o histórico de senha deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou de alteração falhará. |
| 6329 | MMS(3040): admaexport.cpp(2837): O servidor não contém o controle da política de senha do LDAP. | Esse problema ocorre se o controle LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) não está habilitado nos controladores de domínio. Para usar o recurso de write-back de senha, é necessário habilitar o controle. Para fazer isso, os DCs devem estar no Windows Server 2008R2 ou posterior. |
| HR 8023042 | O Mecanismo de Sincronização retornou um erro hr = 80230402, mensagem= Houve uma falha na tentativa de obter um objeto, porque existem entradas duplicadas com a mesma âncora. | Esse erro ocorre quando a mesma ID de usuário está habilitada em vários domínios. Por exemplo, se você estiver sincronizando florestas de contas e recursos e tiver a mesma ID de usuário presente e habilitada em cada uma delas, esse erro poderá ocorrer. <br> <br> Esse erro também pode ocorrer se você usa um atributo de âncora não exclusivo (como alias ou UPN) e dois usuários compartilham o mesmo atributo de âncora. <br> <br> Para resolver esse problema, verifique se você não tem nenhum usuário duplicado em seus domínios e se está usando um atributo de âncora exclusivo para cada usuário. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se a origem do evento é PasswordResetService

| Código | Nome ou mensagem | DESCRIÇÃO |
| --- | --- | --- |
| 31001 | PasswordResetStart | Esse evento indica que o serviço local detectou uma solicitação de redefinição de senha de um usuário federado, com autenticação de passagem ou sincronizado com hash de senha proveniente da nuvem. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha. |
| 31002 | PasswordResetSuccess | Esse evento indica que o usuário selecionou uma nova senha durante uma operação de redefinição de senha. Determinamos que essa senha atende aos requisitos de senha corporativa. A senha foi gravada com êxito novamente no ambiente do Active Directory local. |
| 31003 | PasswordResetFail | Esse evento indica que o usuário selecionou uma senha e a senha chegou com sucesso ao ambiente local. Mas quando tentamos definir a senha no ambiente do Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por diversos motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Para resolver esse problema, crie uma nova senha.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como grupo de administradores corporativos ou de domínio, que não permite operações de conjunto de senha.</li></ul>|
| 31004 | OnboardingEventStart | Esse evento ocorrerá se você habilitar o write-back de senha com o Azure AD Connect e se tivermos iniciado a integração de sua organização com o serviço Web de write-back de senha. |
| 31005 | OnboardingEventSuccess | Esse evento indica que o processo de integração foi bem-sucedido e que a funcionalidade de write-back de senha está pronta para uso. |
| 31006 | ChangePasswordStart | Esse evento indica que o serviço local detectou uma solicitação de alteração de senha de um usuário federado, com autenticação de passagem ou sincronizado com hash de senha proveniente da nuvem. Esse evento é o primeiro evento em cada operação de write-back de alteração de senha. |
| 31007 | ChangePasswordSuccess | Esse evento indica que o usuário selecionou uma nova senha durante uma operação de alteração de senha; determinamos que a senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente Active Directory local. |
| 31008 | ChangePasswordFail | Esse evento indica que um usuário selecionou uma senha e que a senha chegou com sucesso ao ambiente local, mas, quando tentamos definir a senha no ambiente do Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por diversos motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Para resolver esse problema, crie uma nova senha.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | O serviço local detectou uma solicitação de redefinição de senha de um usuário federado, com autenticação de passagem ou sincronizado com hash de senha proveniente do administrador em nome de um usuário. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha iniciada por um administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionou uma nova senha durante uma operação de redefinição de senha iniciada pelo administrador. Determinamos que essa senha atende aos requisitos de senha corporativa. A senha foi gravada com êxito novamente no ambiente do Active Directory local. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionou uma senha em nome do usuário. A senha chegou com sucesso ao ambiente local. Mas quando tentamos definir a senha no ambiente do Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por diversos motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Experimente uma nova senha para resolver esse problema.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha.</li></ul>  |
| 31012 | OffboardingEventStart | Esse evento ocorrerá se você desabilitar o write-back de senha com o Azure AD Connect e indicará que começamos a remoção de sua organização do serviço Web de write-back de senha. |
| 31013| OffboardingEventSuccess| Esse evento indica que o processo de integração foi bem-sucedido e que a funcionalidade de write-back de senha foi desabilitada com êxito. |
| 31014| OffboardingEventFail| Esse evento indica que o processo de desvinculação não foi bem-sucedido. Isso pode ser devido a um erro de permissões na conta de administrador de nuvem ou local especificada durante a configuração. O erro também pode ocorrer se você está tentando usar um administrador global da nuvem federada ao desabilitar o write-back de senha. Para corrigir esse problema, verifique suas permissões administrativas e se não está usando uma conta federada ao configurar a funcionalidade de write-back de senha.|
| 31015| WriteBackServiceStarted| Esse evento indica que o serviço de write-back de senha foi iniciado com êxito. Ele está pronto para aceitar solicitações de gerenciamento de senha da nuvem.|
| 31016| WriteBackServiceStopped| Esse evento indica que o serviço de write-back de senha parou. As solicitações de gerenciamento de senha da nuvem não terão êxito.|
| 31017| AuthTokenSuccess| Esse evento indica que recuperamos com êxito um token de autorização para o administrador global especificado durante a instalação do Azure AD Connect para iniciar o processo de remoção ou integração.|
| 31018| KeyPairCreationSuccess| Esse evento indica que criamos a chave de criptografia de senha com êxito. Essa chave é usada para criptografar senhas da nuvem para envio ao seu ambiente local.|
| 32000| UnknownError| Esse evento indica a ocorrência de um erro desconhecido durante uma operação de gerenciamento de senha. Examine o texto da exceção no evento para obter mais detalhes. Se você está tendo problemas, tente desabilitar e reabilitar o write-back de senha. Se isso não ajudar, inclua uma cópia do seu log de eventos com a ID de acompanhamento especificada internamente ao seu engenheiro de suporte.|
| 32001| ServiceError| Esse evento indica que houve um erro de conexão com o serviço de nuvem de redefinição de senha. Esse erro geralmente ocorre quando o serviço local não pôde se conectar ao serviço Web de redefinição de senha.|
| 32002| ServiceBusError| Esse evento indica que houve um erro ao conectar-se a instância do Barramento de Serviço do seu locatário. Isso pode acontecer porque você está bloqueando as conexões de saída no seu ambiente local. Verifique se o firewall permite conexões via TCP 443 e para https://ssprsbprodncu-sb.accesscontrol.windows.net/ e tente novamente. Se você ainda está com problemas, tente desabilitar e reabilitar o write-back de senha.|
| 32003| InPutValidationError| Esse evento indica que a entrada passada para a API do nosso serviço Web era inválida. Tente a operação novamente.|
| 32004| DecryptionError| Esse evento indica que houve um erro ao descriptografar a senha que chegou da nuvem. Isso pode ser devido a uma falta de correspondência de chave de descriptografia entre o serviço de nuvem e o ambiente local. Para resolver o problema, desabilite e reabilite o write-back de senha no seu ambiente local.|
| 32005| ConfigurationError| Durante a migração, nós salvamos informações específicas de locatário em um arquivo de configuração no seu ambiente local. Esse evento indica que houve um erro ao salvar esse arquivo ou que, quando o serviço foi iniciado, havia erro na leitura do arquivo. Para corrigir o problema, tente desabilitar e reabilitar o write-back de senha para forçar uma regeneração do arquivo de configuração.|
| 32007| OnBoardingConfigUpdateError| Durante a migração, podemos enviar dados da nuvem para o serviço de redefinição de senha local. Esses dados são gravados em um arquivo na memória antes de serem enviados ao serviço de sincronização para armazenamento seguro no disco. Esse evento indica que há um problema com a gravação ou a atualização dos dados na memória. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha para forçar uma regeneração desse arquivo de configuração.|
| 32008| ValidationError| Esse evento indica que recebemos uma resposta inválida do serviço Web de redefinição de senha. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha.|
| 32009| AuthTokenError| Esse evento indica que não conseguimos obter um token de autorização para a conta de administrador global especificada durante a instalação do Azure AD Connect. Esse erro pode ser causado por um nome de usuário ou senha especificado para a conta de administrador global. Esse erro também pode ocorrer se a conta de administrador global especificada é federada. Para corrigir esse problema, execute a configuração novamente com o nome de usuário e senha corretos e verifique se o administrador é uma conta gerenciada (somente em nuvem ou sincronizada por senha).|
| 32010| CryptoError| Esse evento indica que houve um erro ao gerar a chave de criptografia de senha ou ao descriptografar uma senha que chegou do serviço de nuvem. Esse erro provavelmente indica um problema com o seu ambiente. Examine os detalhes de seu log de eventos para saber mais sobre como resolver esse problema. Você também pode tentar desabilitar e reabilitar o serviço de write-back de senha.|
| 32011| OnBoardingServiceError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de integração. Isso pode acontecer por conta de uma regra de firewall ou quando há um problema ao obter uma autenticação de token para o seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída via TCP 443 e TCP 9350 a 9354 ou para https://ssprsbprodncu-sb.accesscontrol.windows.net/. Também verifique se a conta de administrador do Azure AD que você está usando na integração não é federada.|
| 32013| OffBoardingError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de integração. Isso pode acontecer por conta de uma regra de firewall ou quando há um problema ao obter um token de autorização para o seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída via 443 ou para https://ssprsbprodncu-sb.accesscontrol.windows.net/ e se a conta do administrador do Azure Active Directory que você está usando para a remoção não é federada.|
| 32014| ServiceBusWarning| Esse evento indica que tivemos que tentar novamente nos conectar à instância de Barramento de Serviço do locatário. Em condições normais, isso deve não ser uma preocupação, mas se você vir esse evento muitas vezes, considere verificar a sua conexão de rede para o Barramento de Serviço, especialmente se for uma conexão de largura de banda baixa ou de alta latência.|
| 32015| ReportServiceHealthError| Para monitorar a integridade do seu serviço de write-back de senha, enviamos dados de pulsação para o nosso serviço Web de redefinição de senha a cada cinco minutos. Esse evento indica que houve um erro ao enviar essas informações de integridade para o serviço Web em nuvem. Essas informações de integridade não incluem OII (informações de identificação de objeto) ou dados de PII (informações de identificação pessoal) e são essencialmente uma pulsação e estatísticas de serviço básicas para que possamos fornecer informações de status do serviço na nuvem.|
| 33001| ADUnKnownError| Esse evento indica que houve um erro desconhecido retornado pelo Active Directory. Verifique o log de eventos do servidor do Azure AD Connect quanto a eventos provenientes do ADSync para saber mais.|
| 33002| ADUserNotFoundError| Esse evento indica que o usuário que está tentando redefinir ou alterar uma senha não foi encontrado no diretório local. Esse erro pode ocorrer quando o usuário foi excluído no local, mas não na nuvem. Esse erro também pode ocorrer quando há um problema com a sincronização. Verifique os logs de sincronização e os detalhes das últimas execuções de sincronizações para saber mais.|
| 33003| ADMutliMatchError| Quando uma solicitação de redefinição ou de alteração de senha são provenientes da nuvem, usamos a âncora de nuvem especificada durante a instalação do Azure AD Connect para determinar como vincular essa solicitação a um usuário no seu ambiente local. Esse evento indica que encontramos dois usuários em seu diretório local com o mesmo atributo de âncora de nuvem. Verifique os logs de sincronização e os detalhes das últimas execuções de sincronizações para saber mais.|
| 33004| ADPermissionsError| Esse evento indica que a conta de serviço do ADMA (Agente de Gerenciamento do Active Directory) não tem as permissões apropriadas na conta em questão para definir uma nova senha. Verifique se a conta do ADMA na floresta do usuário tem permissões para redefinir e alterar senha em todos os objetos na floresta. Para obter mais informações sobre como definir as permissões, confira a Etapa 4: Configurar as permissões apropriadas do Active Directory.|
| 33005| ADUserAccountDisabled| Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi desabilitada no local. Habilite a conta e tente a operação novamente.|
| 33006| ADUserAccountLockedOut| Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi bloqueada no local. Bloqueios podem ocorrer quando um usuário tiver tentado alterar ou redefinir a senha muitas vezes em um curto período de tempo. Desbloqueie a conta e tente a operação novamente.|
| 33007| ADUserIncorrectPassword| Esse evento indica que o usuário especificou uma senha atual incorreta durante a operação de alteração de senha. Especifique a senha atual correta e tente novamente.|
| 33008| ADPasswordPolicyError| Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha no diretório local que não atende à idade, ao histórico, à complexidade da senha ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado a senha recentemente nessa janela de tempo, não poderá alterar a senha novamente até que ela atinja a duração especificada no domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deve selecionar uma senha que não foi usada nas últimas *X* vezes, em que *X* é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas *X* vezes, verá uma falha. Para fins de teste, o histórico de senha deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou de alteração falhará.|
| 33009| ADConfigurationError| Esse evento indica que houve um problema na gravação de senha no seu diretório local devido a um problema de configuração com o Active Directory. Verifique as mensagens do serviço ADSync no log de eventos de aplicativo do Azure AD Connect do computador para saber mais sobre o erro.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Solucionar problemas de conectividade de write-back de senha

Se você estiver enfrentando interrupções de serviço com o componente de write-back de senha do Azure AD Connect, abaixo há algumas etapas rápidas que você pode seguir para resolver esse problema:

* [Verificar a conectividade de rede](#confirm-network-connectivity)
* [Reiniciar o serviço Sincronização do Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Desabilitar e reabilitar o recurso de write-back de senha](#disable-and-re-enable-the-password-writeback-feature)
* [Instalar a versão mais recente do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Solucionar problemas de write-back de senha](#troubleshoot-password-writeback)

Em geral, para recuperar o serviço da maneira mais rápida, recomendamos que você execute essas etapas na ordem indicada anteriormente.

### <a name="confirm-network-connectivity"></a>Verificar a conectividade de rede

O ponto mais comum de falha é que o firewall e ou tempos-limite de ociosidade e portas de proxy estão configuradas incorretamente. 

Para o Azure AD Connect versão 1.1.443.0 e posterior, você precisa do acesso a HTTPS de saída ao seguinte:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Para ter mais granularidade, confira a lista atualizada de [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que é atualizada toda quarta-feira e entra em vigor na segunda-feira seguinte.

Para saber mais, examine os pré-requisitos de conectividade no artigo [Pré-requisitos para o Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md).

> [!NOTE]
> O SSPR também poderá falhar se a conta se as configurações "a senha nunca expirar" ou "o usuário não pode alterar a senha" estiverem configuradas na conta no AD DS local. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar o serviço Sincronização do Azure AD Connect

Para resolver problemas de conectividade ou outros problemas temporários com o serviço, reinicie o serviço Sincronização do Azure AD Connect:

1. Como administrador, selecione **Iniciar** no servidor que executa o Azure AD Connect.
1. Insira **services.msc** no campo de pesquisa e selecione **Enter**.
1. Procure a entrada **Microsoft Azure AD Sync**.
1. Clique com o botão direito do mouse na entrada do serviço, selecione **Reiniciar**e aguarde a conclusão da operação.

   ![Reiniciar o serviço de Azure AD Sync usando a GUI][Service restart]

Essas etapas restabelecerão a conexão com o serviço de nuvem e resolverão as interrupções que estão ocorrendo. Se a reinicialização do Serviço ADSync não resolver seu problema, recomendamos que você tente desabilitar e reabilitar o recurso de write-back de senha.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desabilitar e reabilitar o recurso de write-back de senha

Para resolver problemas de conectividade, desabilite e reabilite o recurso de write-back de senha:

   1. Como administrador, abra o assistente Configuração do Azure AD Connect.
   1. Em **Conectar-se ao Azure AD**, insira suas credenciais de administrador global do Azure AD.
   1. Em **Conectar-se ao AD DS**, insira suas credenciais de administrador de Serviços de Domínio do AD.
   1. Em **Identificando seus usuários com exclusividade**, selecione o botão **Avançar**.
   1. Em **Recursos opcionais**, desmarque a caixa de seleção **write-back de senha**.
   1. Selecione **Avançar** nas páginas de diálogo restantes sem alterar nada até chegar à página **Pronto para configurar**.
   1. Verifique se a **página Pronto para configurar** mostra a opção **Write-back de senha** como **desabilitada** e selecione o botão verde **Configurar** para confirmar as alterações.
   1. Em **Concluído**, desmarque a opção **Sincronizar agora** e selecione **Concluir** para fechar o assistente.
   1. Abra novamente o assistente Configuração do Azure AD Connect.
   1. Repita as etapas 2 a 8, mas confirme se você marcou a opção **Write-back de senha** na página **Recursos opcionais** para habilitar o serviço novamente.

Essas etapas restabelecerão a conexão com nosso serviço de nuvem e resolverão as interrupções que estão ocorrendo.

Se desabilitar e reabilitar o recurso write-back de senha não resolver o problema, recomendamos que você reinstale o Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar a versão mais recente do Azure AD Connect

A reinstalação do Azure AD Connect pode resolver problemas de configuração e conectividade entre nossos serviços de nuvem e seu ambiente do Active Directory local.

É recomendável que você execute esta etapa somente após tentar as duas primeiras etapas descritas anteriormente.

> [!WARNING]
> Se você personalizou as regras de sincronização de uso imediato, *faça backup delas antes de continuar com a atualização e reimplante-as manualmente após a conclusão*.

1. Baixe a última versão do Azure AD Connect no [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Como você instalou o Azure AD Connect, é necessário realizar uma atualização in-loco para atualizar sua instalação do Azure AD Connect para a última versão.
1. Execute o pacote baixado e siga as instruções na tela para atualizar o computador do Azure AD Connect.

As etapas anteriores restabelecerão a conexão com nosso serviço de nuvem e resolverão as interrupções que estão ocorrendo.

Se a instalação da versão mais recente do servidor do Azure AD Connect não resolver o problema, recomendamos que você tente desabilitar e reabilitar o write-back de senha como uma etapa final depois de instalar a última versão.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Verificar se o Azure AD Connect tem a permissão necessária para write-back de senha

O Azure AD Connect requer a permissão para **Redefinir Senha** do Active Directory a fim de executar o write-back de senha. Para descobrir se o Azure AD Connect tem a permissão necessária para uma determinada conta de Usuário do Active Directory local, você pode usar o recurso de Permissão Efetiva do Windows:

1. Faça o logon no servidor do Azure AD Connect e inicie o **Synchronization Service Manager** selecionando **Iniciar** > **Serviço de Sincronização**.
1. Na guia **Conectores**, selecione o conector do **Active Directory Domain Services** local e selecione **Propriedades**.  
   ![Synchronization Service Manager mostrando como editar propriedades @ no__t-1  
  
1. Na janela pop-up, selecione **Conectar-se à Floresta do Active Directory** e anote a propriedade **User name**. Essa propriedade é a conta de AD DS usada pelo Azure AD Connect para executar a sincronização de diretório. Para que o Azure AD Connect execute o write-back de senha, a conta do AD DS deve ter permissão para redefinir senha.  

   ![Localizando o serviço de sincronização Active Directory conta de usuário](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Entre em um controlador de domínio local e inicie o aplicativo **Usuários e Computadores do Active Directory**.
1. Selecione **Exibir** e verifique se a opção **Recursos Avançados** está habilitada.  

   ![Active Directory usuários e computadores mostram recursos avançados](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Procure a conta de usuário do Active Directory que você deseja verificar. Clique com o botão direito do mouse na conta e selecione **Propriedades**.  
1. Na janela pop-up, vá para a guia **Segurança** e selecione **Avançado**.  
1. Na janela **Configurações de segurança Avançadas para o Administrador**, vá para a guia **Acesso Efetivo**.
1. Selecione **Selecionar um usuário**, selecione a conta do AD DS usada pelo Azure AD Connect (consulte a etapa 3) e selecione **Exibir acesso efetivo**.

   ![Guia acesso efetivo mostrando a conta de sincronização](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Role para baixo e procure **Redefinir senha**. Se a entrada tem uma marca de seleção, a conta do AD DS tem permissão para redefinir a senha da conta de usuário do Active Directory selecionada.  

   ![Validando que a conta de sincronização tem a permissão Redefinir senha](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóruns do Azure AD

Caso você tenha uma pergunta geral sobre o Azure AD e o autoatendimento de redefinição de senha, peça ajuda à comunidade nos [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Os membros da comunidade incluem engenheiros, gerentes de produto, MVPs e colegas profissionais de TI.

## <a name="contact-microsoft-support"></a>Contatar Suporte da Microsoft

Caso não encontre a resposta para um problema, nossas equipes de suporte sempre estarão disponíveis para fornecer assistência adicional.

Para uma assistência adequada, solicitamos que você forneça o máximo de detalhes possíveis ao abrir um caso. Os detalhes incluem:

* **Descrição geral do erro**: Qual é o erro? Qual foi o comportamento observado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em qual página você estava quando observou o erro? Se possível, inclua a URL e uma captura de tela da página.
* **Código de suporte**: Qual foi o código de suporte gerado quando o usuário viu o erro?
   * Para encontrar o código, reproduza o erro, selecione o link **Código de Suporte** na parte inferior da tela e envie o GUID resultante ao engenheiro de suporte.

   ![Localizar o código de suporte na parte inferior da tela][Support code]

  * Se você estiver em uma página sem um código de suporte na parte inferior, selecione F12 para o SID e o CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Inclua a data e a hora exatas *com o fuso horário* em que ocorreu o erro.
* **ID de Usuário**: Qual usuário viu o erro? Um exemplo é *User\@contoso.com*.
   * Trata-se de um usuário federado?
   * Trata-se de um usuário de autenticação de passagem?
   * Um usuário sincronizado com hash de senha?
   * Ou de um usuário somente de nuvem?
* **Licenciamento**: O usuário tem uma licença do Azure AD atribuída?
* **Log de eventos do aplicativo**: Se estiver usando o write-back de senha e o erro estiver na infraestrutura local, inclua uma cópia compactada do log de eventos do aplicativo por meio do servidor do Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reiniciar o serviço Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "O código de suporte está localizado no canto inferior direito da janela"

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha através do Azure AD:

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)
