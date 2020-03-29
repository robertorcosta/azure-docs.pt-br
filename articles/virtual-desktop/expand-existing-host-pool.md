---
title: Expandir o pool de host existente com novos hosts de sessão - Azure
description: Como expandir um pool de host existente com novos hosts de sessão no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365212"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expanda um pool de host existente com novos hosts de sessão

À medida que você aumenta o uso dentro do pool de host, talvez seja necessário expandir seu pool de host existente com novos hosts de sessão para lidar com a nova carga.

Este artigo lhe dirá como você pode expandir um pool de host existente com novos hosts de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>O que você precisa para expandir o pool de host

Antes de começar, certifique-se de ter criado um pool de host e máquinas virtuais de hospedagem (VMs) usando um dos seguintes métodos:

- [Oferta do Azure Marketplace](./create-host-pools-azure-marketplace.md)
- [Modelo de Gerenciador de recursos do GitHub Azure](./create-host-pools-arm-template.md)
- [Criar um pool de host com o PowerShell](./create-host-pools-powershell.md)

Você também precisará das seguintes informações de quando criou pela primeira vez o pool de host e as VMs do host de sessão:

- Tamanho, imagem e prefixo de vm
- As credenciais de administrador de inquilinos do Windows Join e do Windows Virtual Desktop
- Nome da rede virtual e nome da sub-rede

As próximas três seções são três métodos que você pode usar para expandir o pool de host. Você pode fazer qualquer ferramenta de implantação com a qual você esteja confortável.

>[!NOTE]
>Durante a fase de implantação, você verá mensagens de erro para os recursos vm do host de sessão anterior se eles estiverem atualmente desligados. Esses erros acontecem porque o Azure não pode executar a extensão Do DSC do PowerShell para validar que as VMs do host de sessão estão corretamente registradas no pool de host existente. Você pode ignorar esses erros com segurança ou evitar os erros iniciando todas as VMs de host de sessão no pool de host existente antes de iniciar o processo de implantação.

## <a name="redeploy-from-azure"></a>Reimplantar do Azure

Se você já criou um pool de host e VMs de host usando a oferta do [Azure Marketplace](./create-host-pools-azure-marketplace.md) ou [o modelo do GitHub Azure Resource Manager,](./create-host-pools-arm-template.md)você pode reimplantar o mesmo modelo do portal Azure. Reimplantar o modelo reinicialização automaticamente de todas as informações inseridas no modelo original, exceto senhas.

Veja como reimplantar o modelo do Azure Resource Manager para expandir um pool de hosts:

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Na barra de pesquisa no topo do portal Azure, procure **grupos de recursos** e selecione o item em **Serviços**.
3. Encontre e selecione o grupo de recursos criado quando fez o pool de host.
4. No painel no lado esquerdo do navegador, selecione **Implantações**.
5. Selecione a implantação apropriada para o processo de criação do pool de host:
     - Se você criou o pool de host original com a oferta do Azure Marketplace, selecione a implantação começando com **rds.wvd-provision-host-pool**.
     - Se você criou o pool de host original com o modelo GitHub Azure Resource Manager, selecione a implantação chamada **Microsoft.Template**.
6. Selecione **Reimplantar**.
     
     >[!NOTE]
     >Se o modelo não for reimplantado automaticamente quando você selecionar **Reimplantar,** selecione **Modelo** no painel no lado esquerdo do navegador e selecione **'Implantar**'

7. Selecione o grupo de recursos que contém as VMs de host de sessão atuais no pool de host existente.
     
     >[!NOTE]
     >Se você vir um erro que lhe diga para selecionar um grupo de recursos diferente, mesmo que o que você inseriu esteja correto, selecione outro grupo de recursos e selecione o grupo de recursos original.

8. Digite a seguinte URL para o *_artifactsLocation*:`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Digite o novo número total de hosts de sessão desejados no *Número de Instâncias do Rdsh*. Por exemplo, se você estiver expandindo seu pool de host de cinco hosts de sessão para oito, digite **8**.
10. Digite a mesma senha de domínio existente que você usou para o UPN de domínio existente. Não altere o nome de usuário, porque isso causará um erro ao executar o modelo.
11. Digite a mesma senha de admin de inquilino que você usou para o ID de usuário ou aplicativo que você inseriu para *O Dissé.* Mais uma vez, não altere o nome de usuário.
12. Complete a submissão para expandir seu pool de host.

## <a name="run-the-azure-marketplace-offering"></a>Execute a oferta do Azure Marketplace

Siga as instruções em [Criar um pool de host usando o Azure Marketplace](./create-host-pools-azure-marketplace.md) até chegar ao Run the [Azure Marketplace oferecendo para prover um novo pool de host](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). Quando você chegar a esse ponto, você precisará inserir as seguintes informações para cada guia:

### <a name="basics"></a>Noções básicas

Todos os valores nesta seção devem corresponder ao que você forneceu quando criou pela primeira vez o pool de host e as VMs do host de sessão, exceto para *usuários de desktop padrão*:

1.    Para *assinatura*, selecione a assinatura onde você criou pela primeira vez o pool de host.
2.    Para *o grupo Resource*, selecione o mesmo grupo de recursos onde as VMs de host de sessão de pool de host existentes estão localizadas.
3.    Para *Região,* selecione a mesma região onde as VMs de host pool de host existentes estão localizadas.
4.    Para *nome hostpool,* digite o nome do pool de host existente.
5.    Para *o tipo desktop,* selecione o tipo de área de trabalho que corresponde ao pool de host existente.
6.    Para *usuários de desktop padrão,* digite uma lista separada por comma de quaisquer usuários adicionais que você deseja fazer login nos clientes do Windows Virtual Desktop e acesse uma área de trabalho após o acessando a oferta do Azure Marketplace. Por exemplo, se você user3@contoso.com quiser user4@contoso.com atribuir user3@contoso.comeuser4@contoso.comacessar, digite , .
7.    Selecione **Next : Configure a máquina virtual**.

>[!NOTE]
>Com *exceção dos usuários de desktop padrão,* todos os campos devem corresponder exatamente ao que foi configurado no pool de host existente. Se houver uma incompatibilidade que resultará em uma nova piscina de hospedagem.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Todos os valores de parâmetro nesta seção devem corresponder ao que você forneceu quando criou pela primeira vez o pool de host e as VMs do host, exceto o número total de VMs. O número de VMs que você inserir será o número de VMs em seu pool de host expandido:

1. Selecione o tamanho da VM que corresponde às VMs de host de sessão existentes.
    
    >[!NOTE]
    >Se o tamanho específico da VM que você procura não aparece no seletor de tamanho da VM, é porque ainda não o integramos à ferramenta Azure Marketplace. Para solicitar um tamanho de VM, crie uma solicitação ou atualize uma solicitação existente no [fórum UserVoice do Windows Virtual Desktop](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Personalize os *parâmetros de Perfil de Uso,* Total de *usuários*e *Número de máquinas virtuais* para selecionar o número total de hosts de sessão que você gostaria de ter em seu pool de host. Por exemplo, se você estiver expandindo seu pool de host de cinco hosts de sessão para oito, configure essas opções para chegar a 8 máquinas virtuais.
3. Digite um prefixo para os nomes das máquinas virtuais. Por exemplo, se você inserir o nome “prefixo”, as máquinas virtuais serão chamadas de "prefixo-0," "prefixo-1" e assim por diante.
4. Selecione **Next : Configurações da máquina virtual**.

### <a name="virtual-machine-settings"></a>Configurações da máquina virtual

Todos os valores de parâmetro nesta seção devem corresponder ao que você forneceu quando criou pela primeira vez o pool de host e as VMs do host de sessão:

1. Para *a origem da imagem* e a versão do Sistema Operacional da *Imagem,* digite as mesmas informações que você forneceu quando criou o pool de host.
2. Para *o domínio AD, junte-se à UPN* e às senhas associadas, digite as mesmas informações fornecidas quando criou o pool de host para se juntar às VMs ao domínio Active Directory. Essas credenciais serão usadas para criar uma conta local em suas máquinas virtuais. Você pode redefinir essas contas locais para alterar suas credenciais mais tarde.
3. Para obter as informações da rede virtual, selecione a mesma rede virtual e a sub-rede para onde as VMs de sessão de pool de host existentes estão localizadas.
4. Selecione **A seguir : Configure as informações da área de trabalho virtual do Windows**.

### <a name="windows-virtual-desktop-information"></a>Informações do Windows Virtual Desktop

Todos os valores de parâmetro nesta seção devem corresponder ao que você forneceu quando criou pela primeira vez o pool de host e as VMs do host de sessão:

1. Para *Nome do grupo de locatário da Área de Trabalho Virtual do Windows*, digite o nome do grupo de locatário que contém seu locatário. Deixe-o como o padrão, a menos que você tenha recebido um nome de grupo de locatário específico.
2. Para *Nome de locatário da Área de Trabalho Virtual do Windows*, digite o nome do locatário no qual você criará esse pool de hosts.
3. Especifique as mesmas credenciais que você usou quando criou pela primeira vez o pool de host e as VMs do host de sessão. Se você estiver usando um diretor de serviço, digite o ID da instância do Diretório Ativo do Azure onde o seu diretor de serviço está localizado.
4. Selecione **A seguir : Revisão + criar**.

## <a name="run-the-github-azure-resource-manager-template"></a>Execute o modelo do Gerenciador de recursos do GitHub Azure

Siga as instruções no [modelo Executar o gerenciador de recursos do Azure para provisionar um novo pool de host](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) e fornecer todos os mesmos valores de parâmetro, exceto o Número de *Instâncias rdsh*. Digite o número de VMs host de sessão desejadas no pool de host após executar o modelo. Por exemplo, se você estiver expandindo seu pool de host de cinco hosts de sessão para oito, digite **8**.

## <a name="next-steps"></a>Próximas etapas

Agora que você expandiu seu pool de host existente, você pode fazer login em um cliente do Windows Virtual Desktop para testá-los como parte de uma sessão de usuário. Você pode se conectar a uma sessão com qualquer um dos seguintes clientes:

- [Conectar-se ao Cliente de Área de Trabalho do Windows](./connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web](./connect-web.md)
- [Conectar-se ao cliente Android](./connect-android.md)
- [Conectar-se ao cliente macOS](./connect-macos.md)
- [Conectar-se ao cliente iOS](./connect-ios.md)
