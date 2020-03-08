---
title: Controles personalizados no acesso condicional do Azure AD
description: Saiba como os controles personalizados no Azure Active Directory acesso condicional funcionam.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671836"
---
# <a name="custom-controls-preview"></a>Controles personalizados (versão prévia)

Os controles personalizados são uma funcionalidade do Azure Active Directory Premium P1 Edition. Ao usar controles personalizados, seus usuários são redirecionais para um serviço compatível a fim de atender a requisitos adicionais fora do Azure Active Directory. Para atender a esse controle, o navegador de um usuário é redirecionado para o serviço externo, executa todas as atividades necessárias de autenticação ou validação e, em seguida, é Redirecionado de volta para o Azure Active Directory. Azure Active Directory verifica a resposta e, se o usuário foi autenticado ou validado com êxito, o usuário continua no fluxo de acesso condicional.

Esses controles permitem o uso de determinados serviços externos ou personalizados como controles de acesso condicional e geralmente estendem os recursos de acesso condicional.

Provedores que atualmente oferecem um serviço compatível incluem:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para obter mais informações sobre esses serviços, entre em contato diretamente com os provedores.

## <a name="creating-custom-controls"></a>Criando controles personalizados

Para criar um controle personalizado, você deve entrar em contato com o provedor que você deseja utilizar. Cada provedor que não é da Microsoft tem seu próprio processo e requisitos para inscrever-se, assinar ou se tornar uma parte do serviço e indicar que você deseja integrar com o acesso condicional. Nesse ponto, o provedor lhe fornecerá um bloco de dados no formato JSON. Esses dados permitem que o provedor e o acesso condicional funcionem juntos para seu locatário, cria o novo controle e define como o acesso condicional pode informar se os usuários executaram com êxito a verificação com o provedor.

Os controles personalizados não podem ser usados com a automação da proteção de identidade que requer autenticação multifator ou para elevar funções no PIM (Privileged Identity Manager).

Copie os dados JSON e, em seguida, cole-os na caixa de texto relacionada. Não faça nenhuma alteração no JSON, a menos que você entenda explicitamente a alteração que está fazendo. Fazer qualquer alteração pode interromper a conexão entre o provedor e a Microsoft e potencialmente bloquear você e seus usuários do acesso às respectivas contas.

A opção para criar um controle personalizado está na seção **gerenciar** da página de **acesso condicional** .

![Control](./media/controls/82.png)

Clicar em **Novo controle personalizado** abrirá uma folha com uma caixa de texto para os dados JSON do seu controle.  

![Control](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Excluindo controles personalizados

Para excluir um controle personalizado, primeiro você deve verificar se ele não está sendo usado em nenhuma política de acesso condicional. Depois de concluído:

1. Vá para a lista de Controles personalizados
1. Clique em...  
1. Selecione **Excluir**.

## <a name="editing-custom-controls"></a>Editando controles personalizados

Para editar um controle personalizado, você deve excluir o controle atual e criar um novo controle com as informações atualizadas.

## <a name="next-steps"></a>Próximas etapas

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo somente de relatório](concept-conditional-access-report-only.md)

- [Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
