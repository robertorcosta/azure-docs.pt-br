---
title: Criar controlador de dados no Azure Data Studio
description: Criar controlador de dados no Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 208c9b4172719b876766f0c4d07a17caa24bfd63
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310960"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Criar controlador de dados no Azure Data Studio

Você pode criar um controlador de dados usando Azure Data Studio por meio do assistente de implantação e notebooks.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ter acesso a um cluster kubernetes e ter o arquivo kubeconfig configurado para apontar para o cluster kubernetes no qual você deseja implantar.
- Você precisa [instalar as ferramentas de cliente](install-client-tools.md) , incluindo **Azure Data Studio** as extensões de Azure Data Studio chamadas **Azure Arc** e **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- Você precisa fazer logon no Azure em Azure Data Studio.  Para fazer isso: digite CTRL/Command + SHIFT + P para abrir a janela de texto do comando e digite **Azure**.  Escolha **Azure: entrar**.   No painel, que aparece, clique no ícone + no canto superior direito para adicionar uma conta do Azure.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Usar o assistente de implantação para criar o controlador de dados de arco do Azure

Siga estas etapas para criar um controlador de dados de arco do Azure usando o assistente de implantação.

1. Em Azure Data Studio, clique na guia conexões no painel de navegação esquerdo.
2. Clique no botão **...** na parte superior do painel conexões e escolha **nova implantação...**
3. No Assistente para nova implantação, escolha **controlador de dados de arco do Azure**, marque a caixa de seleção aceitação da licença e clique no botão **selecionar** na parte inferior.
4. Use o arquivo kubeconfig padrão ou selecione outro.  Clique em **Próximo**.
5. Escolha um contexto de cluster kubernetes. Clique em **Próximo**.
6. Escolha um arquivo de perfil de configuração de implantação dependendo do cluster kubernetes de destino. **Clique em Avançar**.
8. Escolha a assinatura e o grupo de recursos desejados.
9. Insira um nome para o controlador de dados e para o namespace no qual o controlador de dados será criado.  

> [!NOTE]
> Se o namespace já existir, ele será usado se o namespace ainda não contiver outros objetos kubernetes-pods, etc.  Se o namespace não existir, será feita uma tentativa de criar o namespace.  A criação de um namespace em um cluster kubernetes requer privilégios de administrador de cluster kubernetes.  Se você não tiver privilégios de administrador de cluster kubernetes, peça ao administrador de cluster do kubernetes para executar as primeiras etapas no artigo [criar um controlador de dados usando ferramentas nativas do kubernetes](./create-data-controller-using-kubernetes-native-tools.md) que devem ser executadas por um administrador do kubernetes antes de concluir este assistente.

> [!NOTE]
> Observação: o nome do namespace e do controlador de dados será usado para criar um recurso personalizado no cluster kubernetes para que eles devam estar em conformidade com as [convenções de nomenclatura do kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

10. Selecione um local do Azure.
   
> [!NOTE]
> O local do Azure selecionado aqui é o local no Azure, no qual os *metadados* sobre o controlador de dados e as instâncias de banco que ele gerencia serão armazenados.  As instâncias do controlador de dados e do banco de dados serão realmente crewted no cluster do kubernetes, onde quer que possa ser.

11.  Insira um nome de usuário e uma senha e confirme a senha da conta do administrador do controlador de dados.

> [!NOTE]
> A senha deve ter pelo menos oito caracteres.

1.  Clique em **Próximo**.
2.  Examine e clique em **script para notebook**.
3.  **Examine o bloco de anotações gerado**.  Faça as alterações necessárias, como nomes de classe de armazenamento ou tipos de serviço.
4.  Clique em **executar tudo** na parte superior do bloco de anotações.

## <a name="monitoring-the-creation-status"></a>Monitorando o status de criação

A criação do controlador levará alguns minutos para ser concluída. Você pode monitorar o progresso em outra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo a seguir pressupõem que você criou um controlador de dados e um namespace kubernetes com o nome ' Arc '.  Se você usou um nome de controlador de dados/namespace diferente, poderá substituir "Arc" pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Você também pode verificar o status de criação de qualquer Pod específico executando um comando como abaixo.  Isso é especialmente útil para solucionar problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Solucionando problemas de criação

Se você encontrar qualquer problemas com a criação, consulte o [Guia de solução de problemas](troubleshoot-guide.md).
