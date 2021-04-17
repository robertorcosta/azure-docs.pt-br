---
title: Implantar a função de Gateway de Área de Trabalho Remota na Área de Trabalho Virtual do Windows – Azure
description: Como implantar a função de Gateway de Área de Trabalho Remota na Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 37d859bccd7fea082e059c7ab19c789276974cd1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445679"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>Implantar a função de Gateway de Área de Trabalho Remota na Área de Trabalho Virtual do Windows (versão prévia)

> [!IMPORTANT]
> Esse recurso está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve como usar a função de Gateway de Área de Trabalho Remota (versão prévia) para implantar os servidores de Gateway de Área de Trabalho Remota no seu ambiente. É possível instalar as funções de servidor em computadores físicos ou máquinas virtuais, dependendo de onde estão sendo criadas: no local, baseadas em nuvem ou em ambiente híbrido.

## <a name="install-the-rd-gateway-role"></a>Instalar a função de Gateway de Área de Trabalho Remota

1. Entre no servidor de destino com credenciais administrativas.

2. No **Gerenciador do Servidor**, selecione **Gerenciar** e **Adicionar Funções e Recursos**. O instalador **Adicionar Funções e Recursos** será aberto.

3. Em **Antes de começar**, selecione **Avançar**.

4. Em **Selecionar Tipo de Instalação**, selecione **Instalação baseada em função ou recurso** e selecione **Avançar**.

5. Em **Selecionar servidor de destino**, escolha **Selecionar um servidor no pool de servidores**. Em **Pool de Servidores**, selecione o nome do computador local. Quando terminar, selecione **Avançar**.

6. Em **Selecionar Funções de Servidor** > **Funções**, selecione **Serviços de Área de Trabalho Remota**. Quando terminar, selecione **Avançar**.

7. Em **Serviços de Área de Trabalho Remota**, selecione **Avançar**.

8. Em **Selecionar serviços de função**, selecione somente **Gateway de Área de Trabalho Remota**. Quando houver solicitação para adicionar os recursos necessários, selecione **Adicionar Recursos**. Quando terminar, selecione **Avançar**.

9. Em **Política de Rede e Serviços de Acesso**, selecione **Avançar**.

10. Em **Função de servidor Web (IIS)** , selecione **Avançar**.

11. Em **Selecionar serviços de função**, selecione **Avançar**.

12. Em **Confirmar seleções de instalação**, selecione **Instalar**. Não feche o instalador enquanto o processo de instalação estiver ocorrendo.

## <a name="configure-rd-gateway-role"></a>Configurar a função de Gateway de Área de Trabalho Remota

Depois que a função de Gateway de Área de Trabalho Remota for instalada, você precisará configurá-la.

Para configurar a função de Gateway de Área de Trabalho Remota:

1. Abra o **Gerenciador do Servidor** e selecione **Serviços de Área de Trabalho Remota**.

2. Vá para **Servidores**, clique com o botão direito do mouse no nome do servidor e selecione **Gerenciador de Gateway de Área de Trabalho Remota**.

3. No Gerenciador de Gateway de Área de Trabalho Remota, clique com o botão direito do mouse no nome do gateway e selecione **Propriedades**.

4. Abra a guia **Certificado SSL**, selecione a bolha **Importar um certificado para o Gateway de Área de Trabalho Remota** e selecione **Procurar e Importar Certificado…** .

5. Selecione o nome do arquivo PFX e escolha **Abrir**.

6. Insira a senha para o arquivo PFX quando solicitado.

7. Depois de importar o certificado e a chave privada, a exibição deverá mostrar os atributos de chave do certificado.

>[!NOTE]
>Como a função de Gateway de Área de Trabalho Remota deve ser pública, recomendamos que você use um certificado emitido publicamente. Se você usar um certificado emitido de maneira particular, precisará configurar todos os clientes com a cadeia confiável do certificado com antecedência.

## <a name="next-steps"></a>Próximas etapas

Se você quiser adicionar alta disponibilidade à sua função de Gateway de Área de Trabalho Remota, confira [Adicionar alta disponibilidade ao front Gateway Web e Web da Área de Trabalho Remota](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).