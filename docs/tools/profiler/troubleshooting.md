---
title: Solução de problemas Xamarin Profiler
description: Este documento fornece informações de solução de problemas relacionadas ao Xamarin Profiler. Ele descreve problemas relacionados ao log e diagnóstico, ao IDE e a outros tópicos.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: davidortinau
ms.author: daortin
ms.date: 10/27/2017
ms.openlocfilehash: 5b4b4bdf85ec79a46a4e4c06504eb8b9b85af329
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566951"
---
# <a name="xamarin-profiler-troubleshooting"></a>Solução de problemas Xamarin Profiler

## <a name="logging-and-diagnostics"></a>Registro em log e diagnóstico

A equipe do Xamarin pode ajudar a acompanhar problemas se você nos fornecer informações, incluindo:

- Um screencast do problema, falha ou falha e seu fluxo de trabalho que levou até ele.
- Saídas de log (veja abaixo).
- O **. MLPD** que está sendo gerado para a sessão de criação de perfil (veja abaixo).

### <a name="getting-log-outputs"></a>Obtendo saídas de log

Nos logs do Mac são salvos no `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log` .

No Windows, eles são salvos para `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` incluir o log mais recente sempre que você enviar um problema.

Estamos adicionando mais registro em log à medida que passamos, portanto, essa saída deve aumentar e se tornar mais útil ao longo do tempo.

<a name="gen_mlpd"></a>

### <a name="generating-mlpd-files"></a>Gerando arquivos. MLPD

Um arquivo **. MLPD** é a saída compactada do analisador de tempo de execução mono. O Xamarin Profiler GUI lê os dados de um **. MLPD** e os exibe para o usuário. arquivos **. MLPD** são ferramentas de depuração úteis para o Xamarin, pois ajudam nossos engenheiros a diagnosticar problemas que o criador de perfil pode ter com seus dados.

O **. MLPD** para a sessão atual é automaticamente salvo no diretório do seu Mac `/tmp` e pode ser identificado pelo carimbo de data/hora. Se você ativar o registro em log, a primeira saída será o caminho para o arquivo **. MLPD** . O arquivo **. MLPD** normalmente será salvo no diretório a partir de ~/var/Folders...

O **. MLPD** para uma sessão atual também pode ser salvo escolhendo **arquivo > salvar como...** no menu do criador de perfil:

**Visual Studio para Mac**:

![](troubleshooting-images/image17.png "Saving .mlpd file in Visual Studio for Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Saving .mlpd file in Visual Studio")

É importante observar que **. MLPD** contêm muitas informações e o tamanho do arquivo será grande.

## <a name="troubleshooting"></a>Solução de problemas

A lista a seguir mostra armadilhas comuns, soluções alternativas e dicas e truques para usar o criador de perfil.

> [!NOTE]
> Você precisa ser um assinante do Visual Studio **Enterprise** para desbloquear esse recurso em qualquer Visual Studio Enterprise no Windows ou Visual Studio para Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Não consigo ver a opção do criador de perfil do iOS ou ela está esmaecida [Visual Studio e Visual Studio para Mac]

Verifique as seguintes configurações para resolver isso:

- Verifique se você está usando a configuração de depuração
- Verifique se você está usando o coletor de lixo do SGen.
- Verifique se a plataforma tem [suporte](~/tools/profiler/index.md#Profiler_Support).
- Verifique se você tem a licença correta.
- Verifique se você está conectado e autenticado corretamente.
- [Visual Studio] Você deve estar usando [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) e ter uma licença corporativa válida.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Recebo um erro ao tentar iniciar o criador de perfil

Se você encontrar essa caixa de erro ao usar o criador de perfil no Visual Studio:

![](troubleshooting-images/error.png "Error box when using the profiler in Visual Studio")

Normalmente, é porque não é possível iniciar o simulador/emulador. Tente executar o aplicativo normalmente, corrija os problemas que ele fornece e tente usar o criador de perfil novamente.

#### <a name="to-watch-a-specific-thread"></a>Para assistir a um thread específico

Se você tiver um thread que quisesse observar especificamente, seria ideal nomear o thread no início da criação para obter `ThreadName` em vez de `0x0` . Por exemplo, para definir o nome do thread como `UI` , você pode usar o seguinte código:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Links relacionados

- [Walkthrough – usando o Xamarin Profiler](~/tools/profiler/index.md)
- [Práticas recomendadas de memória e desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas sobre a versão](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
