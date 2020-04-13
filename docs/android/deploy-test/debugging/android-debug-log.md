---
title: Log de depuração do Android
description: Como usar o log de depuração para depurar aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 39ee1039104052b8799debf19f09439f57a1791d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73028090"
---
# <a name="android-debug-log"></a>Log de depuração do Android

Um truque muito comum que os desenvolvedores usam para depurar seus aplicativos é fazer chamadas para o `Console.WriteLine`. No entanto, em uma plataforma móvel como Android não há console. Dispositivos Android fornecem um log que você pode utilizar ao gravar aplicativos. Isso às vezes é referido como _logcat_ devido ao comando que você digita para recuperá-lo. Use a ferramenta **Log de Depuração** para visualizar os dados registrados.

## <a name="android-debug-log-overview"></a>Visão geral do log de depuração do Android

A ferramenta **Log de Depuração** fornece uma maneira de visualizar a saída do log durante a depuração de um aplicativo através do Visual Studio. O log de depuração suporta os seguintes dispositivos:

- acessórios, tablets e telefones Android físicos.
- Um dispositivo Virtual Android em execução no Android Emulator. 

> [!NOTE]
> A ferramenta **Log de Depuração** não funciona com o Xamarin Live Player.

O **Log de Depuração** não exibe mensagens de log que são geradas durante o execução do aplicativo autônomo no dispositivo (ou seja, enquanto ele estiver desconectado do Visual Studio).

## <a name="accessing-the-debug-log-from-visual-studio"></a>Acessar o Log de Depuração do Visual Studio

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para abrir a ferramenta **Log do Dispositivo**, clique no ícone **Log do Dispositivo (logcat)** na barra de ferramentas:

[![Localização da ferramenta Registro de Dispositivos na barra de ferramentas](android-debug-log-images/vswin-01-logcat-sml.png)](android-debug-log-images/vswin-01-logcat.png#lightbox)

Como alternativa, inicie a ferramenta **Log do Dispositivo** por meio de uma das seguintes seleções de menu:

- **Exibir -> Outras Janelas -> Log do Dispositivo**
- **Ferramentas -> Android -> Log do Dispositivo**

A captura de tela a seguir ilustra as várias partes da janela **Ferramenta de Depuração**:

[![Partes da janela Debug Tool](android-debug-log-images/vswin-03-features-sml.png)](android-debug-log-images/vswin-03-features.png#lightbox)

- **Seletor** &ndash; de dispositivos Seleciona qual dispositivo físico ou emulador em execução para monitorar.

- **Entradas de Log** &ndash; Uma tabela de mensagens de log de logcat.

- **Limpar Entradas de Log** &ndash; Limpa todas as entradas de log atuais da tabela.

- **Reproduzir/Pausar** &ndash; Alterna entre atualizar ou pausar a exibição de novas entradas de log.

- **Parar** &ndash; Interrompe a exibição de novas entradas de log.

- **Caixa de Pesquisa** &ndash; Insira as cadeias de caracteres de pesquisa nessa caixa para filtrar um subconjunto de entradas de log.

Quando a janela da ferramenta **Log de Depuração** for exibida, use o menu suspenso do dispositivo para escolher o dispositivo Android a ser monitorado:

[![Localização do Seletor de Dispositivos](android-debug-log-images/vswin-02-devices-combo-sml.png)](android-debug-log-images/vswin-02-devices-combo.png#lightbox)

Após o dispositivo ser selecionado, a ferramenta **Log de Dispositivo** adiciona automaticamente entradas de log de um aplicativo em execução &ndash; essas entradas de log são mostradas na tabela de entradas de log. Alternar entre dispositivos interrompe e inicia o registro de dispositivo. Observe que um projeto Android deve ser carregado antes que qualquer dispositivo apareça no seletor de dispositivos. Se o dispositivo não aparecer no seletor de dispositivos, verifique se ele está disponível no menu suspenso do dispositivo do Visual Studio ao lado do botão **Iniciar**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para abrir a ferramenta **Log de Dispositivo**, clique em **Exibir > Pads > Log de Dispositivo**:

[![Localização do item do menu Registro de Dispositivos](android-debug-log-images/vsmac-01-logcat-sml.png)](android-debug-log-images/vsmac-01-logcat.png#lightbox)

A captura de tela a seguir ilustra as várias partes da janela **Ferramenta de Depuração**:

[![Características da janela Ferramenta de depuração](android-debug-log-images/vsmac-03-features-sml.png)](android-debug-log-images/vsmac-03-features.png#lightbox)

- **Seletor** &ndash; de dispositivos Seleciona qual dispositivo físico ou emulador em execução para monitorar.

- **Entradas de Log** &ndash; Uma tabela de mensagens de log de logcat.

- **Limpar Entradas de Log** &ndash; Limpa todas as entradas de log atuais da tabela.

- **Caixa de Pesquisa** &ndash; Insira as cadeias de caracteres de pesquisa nessa caixa para filtrar um subconjunto de entradas de log.

- **Mostrar Mensagens** &ndash; Alterna a exibição de mensagens informativas.

- **Mostrar Avisos** &ndash; Alterna a exibição de mensagens de aviso (mensagens de aviso são exibidas em amarelo).

- **Mostrar Erros** &ndash; Alterna a exibição de mensagens de erro (mensagens de erro são exibidas em vermelho).

- **Reconectar** &ndash; Reconecta ao dispositivo e atualiza a exibição da entrada do log.

- **Adicionar Marcador** &ndash; Insere uma mensagem de marcador (como `--- Marker N ---`) após a última entrada de log, em que _N_ é um contador que começa em 1 e é incrementado em 1 conforme novos marcadores são adicionados.

Quando a janela da ferramenta Log de Depuração for exibida, use o menu suspenso do dispositivo para escolher o dispositivo Android a ser monitorado:

[![Localização do seletor de dispositivos](android-debug-log-images/vsmac-02-devices-combo-sml.png)](android-debug-log-images/vsmac-02-devices-combo.png#lightbox)

Após o dispositivo ser selecionado, a ferramenta **Log de Dispositivo** adiciona automaticamente entradas de log de um aplicativo em execução &ndash; essas entradas de log são mostradas na tabela de entradas de log. Alternar entre dispositivos interrompe e inicia o registro de dispositivo. Observe que um projeto Android deve ser carregado antes que qualquer dispositivo apareça no seletor de dispositivos. Se o dispositivo não aparecer no seletor de dispositivos, verifique se ele está disponível no menu suspenso do dispositivo do Visual Studio ao lado do botão **Iniciar**.

-----

## <a name="accessing-from-the-command-line"></a>Acessando da Linha de Comando

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Outra opção é exibir o log de depuração por meio da linha de comando. Abra uma janela do prompt de comando e navegue até a pasta de ferramentas da plataforma do SDK do Android (normalmente, a pasta de ferramentas da plataforma SDK está localizada em **C:\\Arquivos de Programas (x86)\\Android\\android-sdk\\platform-tools**).

Se apenas um único dispositivo (dispositivo físico ou emulador) estiver conectado, o log poderá ser visualizado inserindo o seguinte comando:

```shell
$ adb logcat
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Outra opção é exibir o log de depuração por meio da linha de comando. Abra uma janela do Terminal e navegue até a pasta de ferramentas de plataforma do Android SDK (normalmente, a pasta de ferramentas de plataforma do SDK está localizada em **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/platform-tools**).

Se apenas um único dispositivo (dispositivo físico ou emulador) estiver conectado, o log poderá ser visualizado inserindo o seguinte comando:

```shell
$ ./adb logcat
```

-----

Se mais de um dispositivo estiver conectado, o dispositivo deverá ser identificado explicitamente. Por exemplo, **adb -d logcat** exibe o log do único dispositivo físico conectado, enquanto **adb -e logcat** mostra o log do único emulador em execução.

Mais comandos podem ser encontrados inserindo **adb** e lendo as mensagens de ajuda.

## <a name="writing-to-the-debug-log"></a>Gravar no Log de Depuração

As mensagens podem ser gravadas no [Log de Depuração](xref:Android.Util.Log) usando os métodos da classe **Android.Util.Log**.
Por exemplo: 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

Isso produz uma saída semelhante à seguinte:

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

Também é possível usar `Console.WriteLine` para gravar no **Log de depuração** &ndash; essas mensagens que aparecem no logcat com um formato de saída superficialmente diferente (essa técnica é particularmente útil ao depurar aplicativos Xamarin.Forms no Android):

```csharp
System.Console.WriteLine ("DEBUG - Button Clicked!");
```

Isso produz um resultado semelhante ao seguinte logcat:

```
Info (19543) / mono-stdout: DEBUG - Button Clicked!
```

## <a name="interesting-messages"></a>Mensagens interessantes

Ao ler o log (e especialmente ao fornecer snippets de log a outras pessoas), ler o arquivo de log em sua totalidade é muitas vezes incômodo.
Para tornar mais fácil de navegar por meio de mensagens de log, comece procurando uma entrada de log semelhante à seguinte:

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

Em particular, procure uma linha que corresponda à expressão regular que também contenha o nome do pacote do aplicativo:

```shell
^I.*ActivityManager.*Starting: Intent
```

Esta é a linha que corresponde ao início de uma atividade e *a maior parte* das mensagens a seguir (mas não todas) deve estar relacionada ao aplicativo.

Observe que cada mensagem contém o PID (identificador de processo) do processo de geração de mensagem. Na mensagem `ActivityManager` acima, o processo `12944` gerou a mensagem. Para determinar qual processo é o processo do aplicativo que está sendo depurado, procure a mensagem **mono.MonoRuntimeProvider**: 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

Essa mensagem vem do processo que foi iniciado. Todas as mensagens subsequentes que contêm este pid vêm do mesmo processo.
