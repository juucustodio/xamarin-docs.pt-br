---
title: Visualizador XAML para xamarin. Forms
description: Este artigo explica como usar o Visualizador de XAML para ver os layouts de xamarin. Forms renderizados conforme você digita. O Visualizador de XAML está disponível no Visual Studio de 2017 e o Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/31/2018
ms.openlocfilehash: 25c8e1a34f8be5ab2f8491e75fa5aac470d55bc8
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245853"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Visualizador XAML para xamarin. Forms

_Consulte os layouts de xamarin. Forms renderizados conforme você digita!_

## <a name="requirements"></a>Requisitos

Os projetos requerem o pacote NuGet do xamarin. Forms mais recente para o Visualizador de XAML trabalhar. Visualização de aplicativos do Android requer [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Há mais informações no [notas de versão](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Guia de Introdução

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Use o **exibição > outras janelas > Visualizador xamarin. Forms** menu do Visual Studio para abrir a janela de visualização. Use o **Janela > novo grupo de guias Vertical** menu para posicioná-la lado a lado.

[![Visualização do controle ListView no Visual Studio](xaml-previewer-images/xamlp-list-vs-sml.png "visualizador Forms no Visual Studio")](xaml-previewer-images/xamlp-list-vs.png#lightbox "visualizador Forms no Visual Studio")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O **visualização** botão pode ser exibido no editor do mouse em um arquivo XAML e selecionando **abrir com > Visualizador de formulários**. O painel de visualização, em seguida, pode ser mostrado ou ocultado pressionando o **visualização** botão no canto superior direito de qualquer janela de documento XAML:

[![Visualização do controle ListView no Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "visualizador Forms no Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox "visualizador Forms no Visual Studio para Mac")

-----

## <a name="xaml-preview-options"></a>Opções de visualização do XAML

As opções na parte superior do painel de visualização são:

* **Telefone** – processar em uma tela de tamanho de telefone
* **Tablet** – processar em uma tela de tamanho de tablet (Observe que há controles de zoom no canto inferior direito do painel)
* **Android** – mostrar a versão do Android da tela
* **iOS** – mostrar a versão do iOS da tela
* Portait (ícone) – usa orientação retrato para a visualização
* Paisagem (ícone) – usa paisagem orientação para a visualização

## <a name="adding-design-time-data"></a>Adicionando dados de tempo de Design

Alguns layouts podem ser difíceis de visualizar sem dados associados a controles de interface de usuário. Para tornar a visualização mais úteis, atribua alguns dados estáticos para os controles por fixar um contexto de associação (seja no code-behind ou usando XAML).

Consulte de James Montemagno [postagem de blog sobre como adicionar dados de tempo de design](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver como vincular a um ViewModel estático em XAML.

## <a name="detecting-design-mode"></a>Detectando o modo de Design

Estático [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propriedade pode ser examinada para determinar se o aplicativo está em execução no visualizador. Isso permite que você especifique o código que será executado somente quando o aplicativo é executado no visualizador:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>Solução de problemas

Verifique os problemas a seguir e o [Xamarin fóruns](https://forums.xamarin.com/categories/xamarin-forms), se você encontrar problemas.

### <a name="xaml-preview-isnt-showing"></a>Visualização de XAML não está mostrando

Verifique o seguinte:

* Projeto deve ser criado (compilada) antes de tentar visualizar arquivos XAML.
* O agente de Designer deve ser a configuração na primeira vez que você visualiza um arquivo XAML - um indicador de progresso será exibida no visualizador, junto com mensagens de andamento, até que isso está pronto.
* Tente fechar e abrir novamente o arquivo XAML.

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>XAML inválido: O projeto Android precisa criados antes da criação de visualização

O Visualizador de XAML requer que o projeto seja compilado antes de renderizar uma página.
Se o erro abaixo é exibido na parte superior do painel de visualização, crie novamente o aplicativo e tente novamente.

![Mensagem de erro: projeto deve ser compilado primeiro](xaml-previewer-images/error-not-built-sml.png "mensagem de erro: recompile o projeto")
