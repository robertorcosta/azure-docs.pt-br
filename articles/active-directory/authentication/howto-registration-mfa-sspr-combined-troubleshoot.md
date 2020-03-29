---
title: Solucionar problemas de registro combinado - Azure Active Directory
description: Solução de problemas Autenticação multifatorial AD azure e redefinição de senha de autoatendimento (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab7c38d23cb1f05e07488810640aeb791ded3d4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847381"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Solução de problemas registro combinado de informações de segurança (visualização)

As informações deste artigo destinam-se a orientar os admins que estão solucionando problemas relatados pelos usuários da experiência de registro combinado.

|     |
| --- |
| O registro combinado de informações de segurança para autenticação multifatorial do Azure e redefinição de senha do Azure Active Directory (Azure AD) é um recurso de visualização pública do Azure AD. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Logs de auditoria

Os eventos registrados para registro combinado estão na categoria Métodos de Autenticação nos registros de auditoria do Azure AD.

![Ainterface de logs de auditoria do Azure AD mostrando eventos de registro](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

A tabela a seguir lista todos os eventos de auditoria gerados pelo registro combinado:

| Atividade | Status | Motivo | Descrição |
| --- | --- | --- | --- |
| O usuário registrou todas as informações de segurança necessárias | Sucesso | O usuário registrou todas as informações de segurança necessárias. | Este evento ocorre quando um usuário tem concluído o registro com sucesso.|
| O usuário registrou todas as informações de segurança necessárias | Falha | O usuário cancelou o registro de informações de segurança. | Esse evento ocorre quando um usuário cancela o registro do modo de interrupção.|
| Informações de segurança registradas pelo usuário | Sucesso | Método registrado pelo *usuário*. | Esse evento ocorre quando um usuário registra um método individual. *O método* pode ser aplicativo Autenticador, Telefone, E-mail, Perguntas de segurança, senha do aplicativo, telefone alternativo, e assim por diante.| 
| Usuário analisou informações de segurança | Sucesso | O usuário revisou com sucesso as informações de segurança. | Esse evento ocorre quando um usuário seleciona **Parece bom** na página de revisão de informações de segurança.|
| Usuário analisou informações de segurança | Falha | O usuário falhou em revisar as informações de segurança. | Esse evento ocorre quando um usuário seleciona **Parece bom** na página de revisão de informações de segurança, mas algo falha no backend.|
| Informações de segurança excluídas pelo usuário | Sucesso | Método excluído pelo *usuário*. | Esse evento ocorre quando um usuário exclui um método individual. *O método* pode ser aplicativo Autenticador, Telefone, E-mail, Perguntas de segurança, senha do aplicativo, telefone alternativo, e assim por diante.|
| Informações de segurança excluídas pelo usuário | Falha | O usuário não conseguiu excluir o *método*. | Esse evento ocorre quando um usuário tenta excluir um método, mas a tentativa falha por algum motivo. *O método* pode ser aplicativo Autenticador, Telefone, E-mail, Perguntas de segurança, senha do aplicativo, telefone alternativo, e assim por diante.|
| O usuário alterou informações de segurança padrão | Sucesso | O usuário alterou as informações de segurança padrão para o *método*. | Esse evento ocorre quando um usuário altera o método padrão. *O método* pode ser a notificação do aplicativo Authenticator, um código do meu aplicativo autenticador ou token, Call +XXXXXXXXXX, Texto de um código para +X XXXXXXXXX, e assim por diante.|
| O usuário alterou informações de segurança padrão | Falha | O usuário não alterou as informações de segurança padrão para o *método*. | Esse evento ocorre quando um usuário tenta alterar o método padrão, mas a tentativa falha por algum motivo. *O método* pode ser a notificação do aplicativo Authenticator, um código do meu aplicativo autenticador ou token, Call +XXXXXXXXXX, Texto de um código para +X XXXXXXXXX, e assim por diante.|

## <a name="troubleshooting-interrupt-mode"></a>Modo de interrupção de solução de problemas

| Sintoma | Etapas para solucionar problemas |
| --- | --- |
| Não estou vendo os métodos que esperava ver. | 1. Verifique se o usuário tem uma função de administrador Azure AD. Se sim, veja as diferenças políticas de admin sspr. <br> 2. Determine se o usuário está sendo interrompido devido à aplicação do registro de autenticação multifatorial ou à aplicação do registro SSPR. Consulte o [fluxograma](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) em "Modos de registro combinados" para determinar quais métodos devem ser mostrados. <br> 3. Determine quão recentemente a política de Autenticação multifatorial ou SSPR foi alterada. Se a mudança foi recente, pode levar algum tempo para a política atualizada se propagar.|

## <a name="troubleshooting-manage-mode"></a>Modo de gerenciamento de solução de problemas

| Sintoma | Etapas para solucionar problemas |
| --- | --- |
| Eu não tenho a opção de adicionar um método específico. | 1. Determine se o método está habilitado para autenticação multifatorial ou para SSPR. <br> 2. Se o método estiver ativado, salve as políticas novamente e aguarde 1-2 horas antes de testar novamente. <br> 3. Se o método estiver ativado, certifique-se de que o usuário ainda não configurou o número máximo desse método que ele está autorizado a configurar.|

## <a name="disable-combined-registration"></a>Desativar o registro combinado

Quando um usuário registra um número de telefone e/ou aplicativo móvel na nova experiência combinada, nosso serviço carimba um conjunto de sinalizadores (StrongAuthenticationMethods) para esses métodos naquele usuário. Essa funcionalidade permite que o usuário execute a Autenticação Multifatorial com esses métodos sempre que a Autenticação Multifatorial for necessária.

Se um admin habilitar a visualização, os usuários se registram através da nova experiência e, em seguida, o admin desativa a visualização, os usuários podem, sem saber, ser registrados para autenticação multifatorial também.

Se um usuário que completou o registro combinado for para a página de registro [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)de redefinição de senha de autoatendimento (SSPR) atual na página de registro , o usuário será solicitado a realizar a Autenticação Multifatorial antes de acessar essa página. Esta etapa é esperada do ponto de vista técnico, mas é nova para usuários que foram previamente registrados apenas para SSPR. Embora esse passo extra melhore a postura de segurança do usuário, fornecendo outro nível de segurança, os admins podem querer reverter seus usuários para que eles não sejam mais capazes de executar a Autenticação Multifatorial.  

### <a name="how-to-roll-back-users"></a>Como reverter os usuários

Se você, como um admin, quiser redefinir as configurações de Autenticação Multifatorial de um usuário, você poderá usar o script PowerShell fornecido na próxima seção. O script limpará a propriedade StrongAuthenticationMethods para o aplicativo móvel do usuário e/ou número de telefone. Se você executar este script para seus usuários, eles precisarão se registrar para Autenticação Multifatorial se precisarem. Recomendamos testar a reversão com um ou dois usuários antes de reverter todos os usuários afetados.

Os passos a seguir ajudarão você a reverter um usuário ou grupo de usuários.

#### <a name="prerequisites"></a>Pré-requisitos

1. Instale os módulos Ad PowerShell apropriados do Azure. Em uma janela do PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Salve a lista de IDs de objeto de usuário afetados no seu computador como um arquivo de texto com um ID por linha. Anote o local do arquivo.
1. Salve o seguinte script no computador e anote a localização do script:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Reversão

Em uma janela PowerShell, execute o seguinte comando, fornecendo os locais de script e arquivo do usuário. Quando solicitado, forneça as credenciais de administrador global do seu locatário. O script produzirá o resultado da operação de atualização de cada usuário.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Desativar a experiência de visualização

Para desativar a experiência de visualização para seus usuários, complete estas etapas:

1. Faça login no portal Azure como administrador de usuários.
2. Ir para as**configurações** > do usuário do diretório >  **ativo do Azure****Gerenciar configurações para recursos de visualização do painel de acesso**.
3. Em **Usuários, os usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança,** definir o seletor como **Nenhum**e, em seguida, selecionar **Salvar**.

Os usuários não serão mais solicitados a se registrar usando a experiência de visualização.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre a visualização pública do registro combinado para redefinição de senha de autoatendimento e autenticação multifatorial do Azure](concept-registration-mfa-sspr-combined.md)
