---
title: Profiler de Xamarin de solução de problemas
description: Este documento fornece informações de solução de problemas relacionadas ao Xamarin Profiler. Ele descreve os problemas relacionados ao registro em log e diagnóstico, o IDE e outros tópicos.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: lobrien
ms.author: laobri
ms.date: 10/27/2017
ms.openlocfilehash: 34406ce8d4276ba36ab2ddd49e73681ff2fef773
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103745"
---
# <a name="xamarin-profiler-troubleshooting"></a>Profiler de Xamarin de solução de problemas

## <a name="logging-and-diagnostics"></a>Registro em log e diagnóstico

A equipe do Xamarin pode ajudar a acompanhar os problemas se você nos fornecer informações, incluindo:

- Screencast do problema, falha, ou falha e o fluxo de trabalho que levam a ele.
- Saídas de log (veja abaixo).
- O **.mlpd** sendo gerados para a sessão de criação de perfil (veja abaixo).

### <a name="getting-log-outputs"></a>Obtenção de resultados de Log

No Mac os logs são salvos para `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

No Windows, eles são salvos em `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` , inclua o log mais recente sempre que você envie um problema.

Estamos adicionando mais registro em log à medida que avançarmos, portanto, essa saída deve crescem e se tornam mais úteis ao longo do tempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Gerando arquivos .mlpd

Uma **.mlpd** arquivo é a saída compactada do criador de perfil de tempo de execução mono. A GUI do Xamarin Profiler lê os dados de um **.mlpd** e o exibe para o usuário. **.mlpd** os arquivos são úteis para ferramentas de depuração para Xamarin porque elas ajudam nossos engenheiros de diagnosticar problemas do Profiler que possam estar ocorrendo com seus dados.

O **.mlpd** para a sessão atual é salvo automaticamente no seu Mac `/tmp` diretório e pode ser identificado pelo carimbo de hora. Se você ativar o registro em log, a primeira saída será o caminho para o **.mlpd** arquivo. O **.mlpd** arquivo normalmente será salvo no diretório iniciando ~/var/pastas...

O **.mlpd** de uma sessão atual também pode ser salvos, escolhendo **arquivo > Salvar como...** no menu do Profiler:

**O Visual Studio para Mac**:

![](troubleshooting-images/image17.png "Salvando arquivo .mlpd no Visual Studio para Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Salvando arquivo .mlpd no Visual Studio")

É importante observar que **.mlpd** contêm muitas informações e o tamanho do arquivo será grande.

## <a name="troubleshooting"></a>Solução de problemas

A lista a seguir mostra as armadilhas comuns, soluções alternativas e dicas e truques para usar o Profiler.

> [!NOTE]
> **Observação**: você precisa ser um Visual Studio **Enterprise** assinante para desbloquear esse recurso em qualquer Visual Studio Enterprise no Windows ou o Visual Studio para Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Não consigo ver a opção de criador de perfil do iOS ou ela fica acinzentada [Visual Studio e o Visual Studio para Mac]

Verifique as configurações a seguir para resolver esse problema:

- Certifique-se de que você está usando a configuração de depuração
- Certifique-se de que você está usando o coletor de lixo SGen.
- Verifique se a plataforma está [suporte](~/tools/profiler/index.md#Profiler_Support).
- Certifique-se de que você tenha a licença correta.
- Certifique-se de que você está conectado no e adequadamente autenticados.
- [Visual Studio] Você deve usar [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) e ter uma licença válida do Enterprise.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Eu recebo um erro ao tentar iniciar o criador de perfil

Se você executar nessa caixa de erro ao usar o criador de perfil no Visual Studio:

![](troubleshooting-images/error.png "Caixa de erro ao usar o criador de perfil no Visual Studio")

Normalmente é devido a não ser capaz de iniciar o simulador / emulador. Tentar executar o aplicativo normalmente, corrija os problemas que ele oferece e, em seguida, tente usar o Profiler novamente.

#### <a name="to-watch-a-specific-thread"></a>Para assistir a um thread específico

Se você tiver um thread que você quisesse especificamente assistir, seria ideal para nomear o segmento com o muito a partir de sua criação para que obtenham get `ThreadName` em vez de `0x0`. Por exemplo definir o nome do thread como interface do usuário, você pode usar o código a seguir:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Links relacionados

- [Passo a passo: usando o Xamarin Profiler](~/tools/profiler/index.md)
- [Práticas recomendadas de desempenho e memória](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de Versão](https://developer.xamarin.com/releases/profiler/preview/)
