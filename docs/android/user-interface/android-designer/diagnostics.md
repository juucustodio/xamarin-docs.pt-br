---
title: Diagnóstico de layout do Android
description: Explica o diagnóstico de layout do Android e como começar
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 5c29a1a80d8c1f599f0bbc750d22d8334ddb3494
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247737"
---
# <a name="android-layout-diagnostics"></a>Diagnóstico de layout do Android

O diagnóstico de layout do Android foi projetado para ajudar a melhorar a qualidade dos arquivos de layout do Android, destacando problemas comuns de qualidade e otimizações úteis. Esse recurso está disponível para o Visual Studio. + e o Visual Studio para Mac 8.5 +.

Um conjunto padrão de analisadores é fornecido para uma ampla gama de problemas e cada um pode ser personalizado para abranger as necessidades específicas de um projeto. Os analisadores são livremente baseados no sistema de refiapos do Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Habilitar o diagnóstico de layout do Android no Visual Studio 2019

Verifique se a configuração diagnóstico de layout, **habilitar diagnóstico de layout**, está habilitada. Para acessar essa página de opções, escolha **ferramentas** > **Opções**e, em seguida, escolha o **Editor de texto** > **Android XML** > **avançado**:

![Caixa de diálogo opções mostrando como habilitar a opção de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Uma vez habilitado, o editor de layout do Android exibirá problemas:

![Diagnósticos do Android habilitados no Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Habilitar o diagnóstico de layout do Android no Visual Studio para Mac

Verifique se a configuração diagnóstico de layout, **habilitar diagnóstico de layout**, está habilitada. Para acessar essa página de opções, escolha **Visual Studio** > **preferências...** e, em seguida, escolha **Editor de texto** > **Android XML**:

![Caixa de diálogo Preferências mostrando como habilitar a opção de diagnóstico](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Uma vez habilitado, o editor de layout do Android exibirá problemas:

![Diagnósticos do Android habilitados no Visual Studio para Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Recursos

As seções a seguir descrevem os recursos disponíveis no diagnóstico de layout do Android.

### <a name="analyzers"></a>Analisadores

Os analisadores são usados para ajudar a detectar problemas em arquivos de layout. Algumas maneiras de reduzir valores codificados, melhorar o desempenho e sinalizar erros.

### <a name="diagnostic-configuration"></a>Configuração de diagnóstico

Os analisadores podem ser configurados usando um arquivo XML, permitindo que você altere o nível de severidade padrão, ignore determinados arquivos e transmita variáveis.

Você pode usar um arquivo de linha de base se tiver um conjunto de configurações que deseja compartilhar entre vários aplicativos Android. Para usar esse recurso, crie um novo arquivo de configuração e acrescente `-baseline` ao nome do arquivo. As configurações de linha de base são aplicadas primeiro e, em seguida, os arquivos de configuração restantes.

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
> Atualmente, as únicas variáveis são `MAX_VIEW_COUNT` (padrão: 80) e `MAX_DEPTH` (padrão: 10) para `TooManyViews` e `TooDeepLayout`, respectivamente.

Os níveis de severidade são:

- Sugestão
- Informações
- Aviso
- Erro
- Ignorar

### <a name="add-a-configuration-file"></a>Adicionar um arquivo de configuração

Crie um novo arquivo XML na raiz de um projeto de aplicativo do Android. O nome do arquivo não é importante, mas este exemplo usa `AndroidLayoutDiagnostics.xml`:

![Adicionar Novo Item](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Depois que o novo arquivo XML for adicionado, ele deverá aparecer na árvore de projeto do aplicativo Android:

![Árvore de projeto de aplicativo do Android](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Verifique se a ação de compilação está definida como **AndroidResourceAnalysisConfig** no painel de propriedades.
A maneira mais fácil de extrair o painel de propriedades para o novo arquivo é clicar com o botão direito do mouse no arquivo e selecionar Propriedades. Depois que o painel Propriedades for exibido, você deverá alterar a **ação de Build** para **AndroidResourceAnalysisConfig**:

![Definir ação de compilação nas propriedades do item](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Agora que você tem um arquivo XML em branco, precisa adicionar o elemento raiz `<configuration>`. Neste ponto, você pode ajustar o comportamento padrão de quaisquer problemas com suporte.
Se você quiser garantir que as cadeias de caracteres embutidas em código sejam tratadas como erros, adicione:

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![Arquivo de configuração de diagnóstico](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Agora que o texto embutido em código é considerado um erro, agora ele está sinalizado com um ondulado vermelho no editor de layout:

![Layout usando a configuração de diagnóstico](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Para que as novas alterações de arquivo de configuração entrem em vigor, todos os arquivos de layout abertos no momento precisam ser reabertos.
>

## <a name="troubleshooting"></a>solução de problemas

Aqui estão alguns problemas comuns possíveis.

- Verifique se não há erro de formato XML.
- A ação de compilação está definida corretamente para **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problemas conhecidos

- O painel de erro não é preenchido até que o arquivo seja alterado pela primeira vez.

## <a name="related-links"></a>Links relacionados

- [Verificações de fiapos do Android](http://tools.android.com/tips/lint-checks)
- [Aprimore seu código com verificações de pano sem fiapos](https://developer.android.com/studio/write/lint)
