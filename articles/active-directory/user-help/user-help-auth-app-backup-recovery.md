---
title: Fazer backup e recuperar contas com o aplicativo Microsoft Authenticator - Azure AD
description: Aprenda a fazer backup e recuperar suas credenciais de conta de backup, usando o aplicativo Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297945"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Fazer backup e recuperar credenciais de conta usando o aplicativo Microsoft Authenticator

**Aplica-se a:**

- dispositivos iOS, rodando a versão 5.7.0 e posterior

- Dispositivos Android, rodando a versão 6.6.0 e posterior

O aplicativo Microsoft Authenticator faz backup das credenciais da sua conta e das configurações de aplicativos relacionadas, como a ordem de suas contas, na nuvem. Após o backup, também será possível usar o aplicativo para recuperar as informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

Cada local de armazenamento de backup requer que você tenha uma conta Microsoft pessoal, enquanto o iOS também exige que você tenha uma conta no iCloud. Você pode ter várias contas armazenadas nesse único local. Por exemplo, você pode ter uma conta pessoal, uma conta de trabalho ou escola, e uma conta pessoal, não-Microsoft, como para facebook, Google e assim por diante.

> [!IMPORTANT]
> Apenas suas credenciais pessoais e de terceiros são armazenadas, o que inclui seu nome de usuário e o código de verificação da conta necessário para provar sua identidade. Não armazenamos nenhuma outra informação associada às suas contas, incluindo e-mails ou arquivos. Também não associamos ou compartilhamos suas contas de forma alguma ou com qualquer outro produto ou serviço. E, finalmente, seu anúncio de TI não obterá nenhuma informação sobre nenhuma dessas contas.

## <a name="back-up-your-account-credentials"></a>Fazer backup das credenciais da conta

Antes de fazer backup de suas credenciais, você deve ter:

- Uma [conta pessoal da Microsoft](https://account.microsoft.com/account) para agir como uma conta de recuperação.

- **Somente para iOS,** você deve ter uma [conta no iCloud](https://www.icloud.com/) para o local real de armazenamento.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Para ativar o backup na nuvem para dispositivos iOS

- Em seu dispositivo iOS, selecione **Configurações**, **Backup** e, em seguida, ative **Backup do iCloud**.

    O backup das credenciais da sua conta é feito para a conta do iCloud.

    ![Tela de configurações do iOS mostrando a localização das configurações de backup do iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Para ativar o backup na nuvem para dispositivos Android

- Em seu dispositivo Android, selecione **Configurações,** selecione **Backup**e, em seguida, ative **o backup**na nuvem .

    As credenciais de sua conta são copiadas para sua conta na nuvem.

    ![Tela de configurações do Android, mostrando a localização das configurações de backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recupere as credenciais da sua conta no seu novo dispositivo

Você pode recuperar as credenciais da sua conta na nuvem, mas primeiro deve ter certeza de que a conta que está recuperando não existe no aplicativo Microsoft Authenticator. Por exemplo, se você está recuperando sua conta pessoal da Microsoft, você deve ter certeza de que não tem uma conta pessoal da Microsoft já configurada no aplicativo autenticador. Este cheque é importante para que possamos ter certeza de que não estamos sobreescrevendo ou apagando uma conta existente por engano.

### <a name="to-recover-your-information"></a>Para recuperar as informações

1. Em seu dispositivo móvel, abra o aplicativo Microsoft Authenticator e selecione **Iniciar recuperação** na parte inferior da tela.

    ![Aplicativo Microsoft Authenticator, mostrando onde clicar em Iniciar recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Entre na conta de recuperação, usando a mesma conta pessoal da Microsoft utilizada durante o processo de backup.

    As credenciais da conta são recuperadas para o novo dispositivo.

Após concluir a recuperação, você poderá notar que os códigos de verificação de conta pessoal da Microsoft no aplicativo Microsoft Authenticator são diferentes entre os telefones novos e antigos. Os códigos são diferentes porque cada dispositivo tem sua própria credencial exclusiva, mas ambos são válidos e funcionam durante a autenticação usando o telefone associado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperar contas adicionais que exigem mais verificação

Se você usar notificações push com suas contas pessoais ou de trabalho ou escola, você receberá um alerta na tela que diz que você deve fornecer verificação adicional antes de recuperar suas informações. Como as notificações push exigem o uso de uma credencial vinculada ao seu dispositivo específico e nunca enviada pela rede, você deve provar sua identidade antes que a credencial seja criada no seu dispositivo.

Para contas pessoais da Microsoft, você pode provar sua identidade inserindo a senha junto com um email ou número de telefone alternativo. Para contas corporativas ou de estudante, você deverá digitalizar um código QR fornecido pelo provedor de conta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Para fornecer verificação adicional para contas pessoais

1. Na tela **Contas** do aplicativo Microsoft Authenticator, selecione a conta que deseja recuperar. Em um dispositivo Android, selecione a seta ao lado da conta que deseja recuperar.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    Em um dispositivo iOS, toque na conta que deseja recuperar para abrir a exibição em tela cheia da conta.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Faça login para recuperar a conta. Em um dispositivo Android, selecione **Entrar para recuperar**.

    ![Aplicativo Microsoft Authenticator para inserir suas informações de login no Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    Em um dispositivo iOS, toque no azulejo da conta que deseja recuperar e toque na opção para fazer login e recuperar. Em seguida, digite sua senha e, em seguida, confirme seu endereço de e-mail ou número de telefone como verificação adicional.unt.

    ![Aplicativo Microsoft Authenticator para inserir suas informações de login no iOS](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Para fornecer verificação adicional para contas corporativas ou de estudante

1. Faça login para recuperar a conta. Em um dispositivo Android, selecione **Entrar para recuperar**.

    ![Aplicativo Microsoft Authenticator recupera uma conta de trabalho ou escola no Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    Em um dispositivo iOS, toque na conta que você gostaria de recuperar para abrir a exibição em tela cheia da conta.

    ![Aplicativo Microsoft Authenticator recupera uma conta de trabalho ou escola no iOS](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. Você pode usar um código QR para recuperar uma conta. Selecione **Digitalizar código QR para recuperar** e, em seguida, digitalize o código QR.

    No Android:

    ![Aplicativo Microsoft Authenticator no Android, permitindo que você escaneie seu código QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    No iOS:

    ![Aplicativo Microsoft Authenticator no iOS, permitindo que você escaneie seu código QR](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >Para obter mais informações sobre códigos QR e como obter um, consulte [Comece com o aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) ou [Configure informações de segurança para usar um aplicativo autenticador,](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)com base em se o seu anúncio ligou as informações de segurança.
    >
    >Se esta for a primeira vez que está configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou que o aplicativo tire fotos e grave vídeo (Android). Você precisa selecionar **Permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma foto do código QR na próxima etapa. Se não permitir acesso da câmera, você ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, confira [Adicionar uma conta manualmente ao aplicativo](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Solucionando problemas de backup e recuperação

Existem algumas razões pelas quais seu backup pode não estar disponível

- **Mudança de sistemas operacionais**: Seu backup é armazenado no iCloud para iOS e no provedor de armazenamento em nuvem da Microsoft para Android. Isso significa que seu backup não está disponível se você alternar entre dispositivos Android e iOS. Se você fizer a troca, você deve recriar manualmente suas contas dentro do aplicativo Microsoft Authenticator.

- **Problemas**de rede : Se você estiver tendo problemas relacionados à rede, certifique-se de que está conectado à rede e faça o seu uso corretamente na sua conta.

- **Problemas na conta**: Se você estiver tendo problemas relacionados à conta, certifique-se de que você está devidamente conectado à sua conta. Para o iOS isso significa que você deve ser conectado no iCloud usando a mesma conta AppleID que o seu iPhone.

- **Exclusão acidental**: É possível que você excluiu sua conta de backup do dispositivo anterior ou durante o gerenciamento de sua conta de armazenamento na nuvem. Nessa situação, será necessário recriar manualmente a conta no aplicativo.

- **Contas microsoft authenticator existentes**: Se você já configurou contas no aplicativo Microsoft Authenticator, o aplicativo não poderá recuperar suas contas de backup. Impedir a recuperação ajuda a garantir que os detalhes da conta não sejam substituídos por informações desatualizadas. Nessa situação, antes de recuperar o backup você deverá remover todas as informações de conta existentes das contas existentes configuradas no aplicativo Authenticator.

- **O backup está desatualizado**: Se suas informações de backup estão desatualizadas, você pode ser solicitado a atualizar as informações fazendo login em sua conta microsoft recovery novamente. Sua conta de recuperação é a conta pessoal da Microsoft que você usou inicialmente para armazenar seu backup. Se um login for necessário, você verá um ponto vermelho no menu ou na barra de ação, ou verá um ponto de exclamação solicitando que você faça login para terminar de restaurar a restauração do backup. Depois de selecionar o ícone, você será solicitado a fazer login novamente para atualizar suas informações.

## <a name="next-steps"></a>Próximas etapas

Agora que você fez backup e recuperou as credenciais da conta para o novo dispositivo, continue usando o aplicativo Microsoft Authenticator para verificar a identidade. Para obter mais informações, consulte [Entrar em suas contas usando o aplicativo Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Artigos relacionados

- [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](user-help-auth-app-faq.md)

- [Autenticação Multifator](https://docs.microsoft.com/azure/multi-factor-authentication/)
