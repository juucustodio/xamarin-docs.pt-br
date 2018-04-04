---
title: Criador de perfil do Xamarin de solução de problemas
description: O criador de perfil do Xamarin de solução de problemas
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 4844c999ceddcee89d4f45f6e41dd4c7f2caf054
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarin-profiler-troubleshooting"></a>Criador de perfil do Xamarin de solução de problemas

_O criador de perfil do Xamarin de solução de problemas_

## <a name="logging-and-diagnostics"></a>Registro em log e diagnóstico

A equipe do Xamarin pode ajudar a rastrear problemas se você nos fornecer informações, incluindo:

- Screencast do problema, falha, ou falha e o fluxo de trabalho que levam a ele.
- Saídas de log (veja abaixo).
- O **.mlpd** que está sendo gerado para a sessão de criação de perfil (veja abaixo).

### <a name="getting-log-outputs"></a>Obtendo as saídas de Log
No Mac os logs são salvos para `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

No Windows, eles são salvos em `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` , inclua o log mais recente sempre que você enviar um problema.

Estamos adicionando mais log à medida que passamos, portanto, essa saída deve crescer e se tornam mais útil ao longo do tempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Gerando arquivos .mlpd

Um **.mlpd** arquivo é a saída compactada do criador de perfil de tempo de execução mono. A GUI do criador de perfil Xamarin lê os dados de um **.mlpd** e o exibe para o usuário. **.mlpd** os arquivos são úteis para as ferramentas de depuração para Xamarin porque eles ajudam nossos engenheiros diagnosticar problemas o criador de perfil pode estar ocorrendo com seus dados.

O **.mlpd** para a sessão atual é automaticamente salvo no seu Mac `/tmp` diretório e pode ser identificado pelo carimbo de hora. Se você ativar o registro em log, a primeira saída será o caminho para o **.mlpd** arquivo. O **.mlpd** arquivo normalmente será salvo no diretório inicial ~/var/pastas...

O **.mlpd** para uma sessão atual também pode ser salvas escolhendo **arquivo > Salvar como...** no menu do criador de perfil:

**O Visual Studio para Mac**:

![](troubleshooting-images/image17.png "Salvando arquivo .mlpd no Visual Studio para Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Salvando arquivo .mlpd no Visual Studio")


É importante observar que **.mlpd** contêm muitas informações e o tamanho do arquivo será grande.

## <a name="troubleshooting"></a>Solução de problemas

A lista a seguir mostra as armadilhas comuns, soluções alternativas e dicas e truques para usar o criador de perfil.

> [!NOTE]
> **Observação**: você precisa ser um Visual Studio **Enterprise** assinante para desbloquear esse recurso em um Visual Studio Enterprise no Windows ou o Visual Studio para Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Não consigo ver a opção de criador de perfil do iOS ou fica acinzentado [Visual Studio e o Visual Studio para Mac]

Verifique as configurações a seguir para resolver esse problema:

- Certifique-se de que você está usando a configuração de depuração
- Certifique-se de que você está usando o coletor de lixo SGen.
- Certifique-se de que a plataforma é [suporte](~/tools/profiler/index.md#Profiler_Support).
- Certifique-se de que você tem a licença à direita.
- Certifique-se de que você está conectado no e corretamente autenticados.
- [Visual Studio] Você deve estar usando [Visual Studio Enterprise](https://www.visualstudio.com/vs/enterprise/) e ter uma licença válida do Enterprise.


#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Eu recebo um erro ao tentar iniciar o criador de perfil

Se você executar nesta caixa de erro ao usar o criador de perfil do Visual Studio:

![](troubleshooting-images/error.png "Caixa de erro ao usar o criador de perfil do Visual Studio")

É normalmente porque não é possível iniciar o simulador / emulador. Tentar executar o aplicativo normalmente, corrija os problemas que ele oferece e, em seguida, tente usar o criador de perfil novamente.

#### <a name="to-watch-a-specific-thread"></a>Para assistir a um segmento específico

Se você tiver um thread que você deseja observar especificamente, seria ideal para nomear o segmento com o início muito de sua criação para que obter get `ThreadName` em vez de `0x0`. Por exemplo definir o nome de thread como a interface do usuário, você pode usar o código a seguir:


```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```



## <a name="related-links"></a>Links relacionados

- [Passo a passo - usando o criador de perfil do Xamarin](~/tools/profiler/index.md)
- [Práticas recomendadas de memória e desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de Versão](https://developer.xamarin.com/releases/profiler/preview/)
