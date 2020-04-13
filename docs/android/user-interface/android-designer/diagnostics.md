---
title: Diagnósticos de layout do Android
description: Explica os diagnósticos de layout do Android e como começar
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 746f74e68fa4816f1f7979980af9506dc0173542
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987576"
---
# <a name="android-layout-diagnostics"></a>Diagnósticos de layout do Android

Os diagnósticos de layout do Android são projetados para ajudar a melhorar a qualidade dos arquivos de layout do Android, destacando problemas de qualidade comuns e otimizações úteis. Este recurso está disponível tanto para visual studio 16.5+ e visual studio para Mac 8.5+.

Um conjunto padrão de analisadores é fornecido para uma ampla gama de problemas e cada um pode ser personalizado para cobrir as necessidades específicas de um projeto. Os analisadores são vagamente baseados no sistema de fiadura Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Habilite os diagnósticos de layout do Android no Visual Studio 2019

Certifique-se de que a configuração de diagnóstico de layout, **habilitar diagnósticos de layout,** esteja ativada. Para acessar esta página de opções, escolha **Opções** > **de ferramentas**e, em seguida, escolha O Editor **de** > Texto**Android XML** > **Avançado**:

![Diálogo de opções mostrando como ativar a opção de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Uma vez ativado, o editor de layout do Android exibirá problemas:

![Diagnósticos para Android ativados no Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Habilite os diagnósticos de layout do Android no Visual Studio para Mac

Certifique-se de que a configuração de diagnóstico de layout, **habilitar diagnósticos de layout,** esteja ativada. Para acessar esta página de opções, escolha **Visual Studio** > **Preferences...** e, em seguida, escolha **Editor de** > texto**Android XML**:

![Diálogo de preferências mostrando como ativar a opção de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Uma vez ativado, o editor de layout do Android exibirá problemas:

![Diagnósticos android habilitados no Visual Studio para Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Recursos

As seções a seguir descrevem os recursos disponíveis nos diagnósticos de layout do Android.

### <a name="analyzers"></a>Analisadores

Os analisadores são usados para ajudar a detectar problemas em arquivos de layout, reduzir valores codificados, melhorar o desempenho e detectar erros de sinalização. Para uma lista de analisadores, consulte [analisadores](diagnostic-analyzers.md) de diagnóstico de designers do Android

### <a name="diagnostic-configuration"></a>Configuração de diagnóstico

Os analisadores podem ser configurados usando um arquivo XML, permitindo que você altere o nível de gravidade padrão, ignore certos arquivos e passe em variáveis.

Você pode usar um arquivo de linha de base se você tiver um conjunto de configurações que deseja compartilhar em vários aplicativos Android. Para usar esse recurso, crie um `-baseline` novo arquivo de configuração e apêndice ao nome do arquivo. As configurações da linha de base são aplicadas primeiro e, em seguida, os arquivos de configuração restantes.

> [!TIP]
> Isso pode ser útil se você quiser ignorar um conjunto de problemas em um aplicativo Android novo ou existente.

O formato é:

```xml
<?xml version="1.0" encoding="utf-8" ?> 
<configuration>
    <issue id="DuplicateIDs" severity="warning">
        <ignore path="Resources/layout/layout1.xml" />
    </issue>
    <issue id="HardcodedText" severity="informational">
        <ignore path="Resources/layout/layout1.xml" />
        <ignore path="Resource/layout/layout2.xml" />
    </issue>
    <issue id="TooManyViews">
        <variable name="MAX_VIEW_COUNT" value="12" />
    </issue>
    <issue id="TooDeepLayout">
        <variable name="MAX_DEPTH" value="12" />
    </issue>
</configuration>
```

> [!NOTE]
> Atualmente, as únicas variáveis `MAX_VIEW_COUNT` são (padrão: 80) e `TooDeepLayout` `MAX_DEPTH` (padrão: 10) para `TooManyViews` e respectivamente.

Os níveis de severidade são:

- Sugestão
- Informações
- Aviso
- Erro
- Ignorar

### <a name="add-a-configuration-file"></a>Adicionar um arquivo de configuração

Crie um novo arquivo XML na raiz de um projeto de aplicativo para Android. O nome do arquivo não é importante, `AndroidLayoutDiagnostics.xml`mas este exemplo usa:

![Adicionar Novo Item](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Uma vez que o novo arquivo XML é adicionado, ele deve aparecer na árvore de projeto do aplicativo para Android:

![Árvore de projeto do aplicativo para Android](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Certifique-se de que a ação de compilação está definida como **AndroidResourceAnalysisAnalysisfig** no painel de propriedades.
A maneira mais fácil de puxar o painel de propriedades para o novo arquivo é clicar com o botão direito do mouse no arquivo e selecionar propriedades. Uma vez que o painel de propriedades esteja sendo exibido, você deve alterar a **Ação de Compilação** para **AndroidResourceAnalysis:**

![Definir ação de construção em propriedades de itens](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Agora que você tem um arquivo XML `<configuration>` em branco, você precisa adicionar o elemento raiz. Neste ponto, você pode ajustar o comportamento padrão de quaisquer problemas suportados.
Se você quiser garantir que as strings codificadas sejam tratadas como erros, adicione:

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![Arquivo de configuração de diagnóstico](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Agora que o texto codificado é considerado um erro, ele agora está marcado com um rabisco vermelho no editor de layout:

![Layout usando configuração de diagnósticos](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Para que qualquer nova configuração de arquivo entre em vigor, todos os arquivos de layout abertos atualmente precisam ser reabertos.
>

## <a name="troubleshooting"></a>Solução de problemas

Aqui estão alguns possíveis problemas comuns.

- Certifique-se de que não há erro de formato XML.
- A ação de compilação é definida corretamente para **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problemas conhecidos

- O bloco de erro não é preenchido até que o arquivo seja alterado pela primeira vez.

## <a name="related-links"></a>Links relacionados

- [Android Lint Checks](http://tools.android.com/tips/lint-checks)
- [Melhore seu código com verificações de fiapos](https://developer.android.com/studio/write/lint)
