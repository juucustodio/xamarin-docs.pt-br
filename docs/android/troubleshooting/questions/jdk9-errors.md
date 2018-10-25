---
title: Xamarin. Android e Java Development Kit 9
description: Este artigo explica como resolver Java Development Kit (JDK) 9 ou posteriores erros no xamarin. Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: d8c64ff79367d93e282edd9534ffb98f5bb90c93
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "36269667"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin. Android e Java Development Kit 9 ou posterior

_Este artigo explica como resolver Java Development Kit (JDK) 9 ou posteriores erros no xamarin. Android._


## <a name="overview"></a>Visão geral

Xamarin. Android usa o Java Development Kit (JDK) para integrar com o SDK do Android para criar aplicativos Android e executar o designer do Android. As versões mais recentes do SDK do Android (API 24 e superior) exigem o JDK 8 (1.8) ou o Microsoft Mobile OpenJDK Preview. **Como as ferramentas do SDK do Android disponíveis no Google ainda não são compatíveis com o JDK 9, xamarin. Android não funciona com o JDK 9 ou posterior.**

## <a name="jdk-errors"></a>Erros JDK

Se você tentar compilar um projeto xamarin. Android com uma versão do JDK mais tarde do que o JDK 8, você obterá um erro explícito, indicando que não há suporte para esta versão do JDK. Por exemplo:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

Para resolver esses erros, você deve instalar o JDK 8 (1.8) conforme explicado em [como atualizar a versão do Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
Como alternativa, você pode instalar o [versão prévia do Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md) o OpenJDK de móveis Microsoft substituirá o JDK 8 para o desenvolvimento do xamarin. Android.


## <a name="checking-the-jdk-version"></a>Verificando a versão do JDK

Você pode verificar qual versão do Java que você instalou, digitando o comando a seguir (o JDK `bin` directory deve estar no seu `PATH`):

```shell
java -version
```

Se o JDK 9 instalado, você verá uma mensagem semelhante ao seguinte:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Se o JDK 9 ou posterior estiver instalado, você deve instalar o Java JDK 8 (1.8) ou o Microsoft Mobile OpenJDK Preview. Para obter informações sobre como instalar o JDK 8, consulte [como atualizar a versão do Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md). Para obter informações sobre como instalar o Microsoft Mobile OpenJDK, consulte [versão prévia do Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md).

Observe que você não precisa desinstalar uma versão posterior do JDK; No entanto, você deve garantir que o Xamarin está usando o JDK 8 em vez de uma versão mais recente do JDK. No Visual Studio, clique em **Ferramentas > Opções > Xamarin > configurações do Android**. Se **local do Java Development Kit** não está definido como um local do JDK 8 (como **c:\\arquivos de programas\\Java\\jdk1.8.0_111**), clique em **alteração**  e defini-lo para o local onde o JDK 8 está instalado. No Visual Studio para Mac, navegue até **Preferências > projetos > locais do SDK > Android > SDK do Java (JDK)** e clique em **procurar** para atualizar esse caminho.

## <a name="known-issues-with-jdk-9"></a>Problemas conhecidos com o JDK 9

### <a name="apksigner"></a>apksigner

Há um problema conhecido com o JDK 9 e o apksigner no qual o `apksigner.bat` arquivo invoca o `apksigner.jar` com `-Djava.ext.dirs` em vez de `-classpath` que espera JDK 9. É recomendável usar o JDK 8 (1.8). Para obter informações sobre como instalar o JDK 8, consulte [como atualizar a versão do Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Se você tiver instalado o JDK 9, certifique-se de que o caminho a seguir não está definido no seu `PATH` variável de ambiente como ela apontará para o JDK 9: `C:\ProgramData\Oracle\Java\javapath`. Após a remoção, `java-version` na linha de comando deve mostrar o JDK 8.
