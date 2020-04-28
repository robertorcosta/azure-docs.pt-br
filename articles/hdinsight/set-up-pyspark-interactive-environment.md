---
title: Ambiente interativo do PySpark com as ferramentas do Azure HDInsight
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
keywords: VSCode, Ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Hive Interativo, Consulta Interativa
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 47f774f7b848e34aa7dc219ee4b08fb083cbd813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188201"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo do PySpark para o Visual Studio Code

As etapas a seguir mostram como configurar o ambiente interativo do PySpark no VS Code.

Usamos comando **python/pip** para criar o ambiente virtual em seu caminho de Página Inicial. Se você deseja usar outra versão, precisa alterar a versão padrão do comando **python/pip** manualmente. Para obter mais detalhes, confira [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Instale o [Python](https://www.python.org/downloads/) e o [Pip](https://pip.pypa.io/en/stable/installing/).

   * Instale o Python [https://www.python.org/downloads/](https://www.python.org/downloads/)do.
   * Instale o Pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) do (se ele não estiver instalado na instalação do Python).
   * Valide se o Python e o Pip foram instalados com êxito usando os comandos a seguir. (Opcional)

        ![Verificar o comando de versão do Python Pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > É recomendável instalar o Python manualmente em vez de usar a versão padrão do macOS.

2. Instale **virtualenv** executando o comando a seguir.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Outros pacotes

Se você vir uma mensagem de erro, instale os pacotes necessários executando os seguintes comandos:

   ![Instalar o pacote libkrb5 para Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Reinicie o VS Code e volte para o editor de scripts que está executando o **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Próximas etapas

### <a name="demo"></a>Demonstração

* HDInsight para VS Code: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar a ferramenta Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Use o Azure Toolkit for IntelliJ para criar e enviar aplicativos Scala do Apache Spark](spark/apache-spark-intellij-tool-plugin.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
