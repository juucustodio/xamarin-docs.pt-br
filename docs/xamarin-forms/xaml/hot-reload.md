---
title: Hot recarregamento de XAML para Xamarin. Forms
description: Recarregue as alterações no arquivo XAML instantaneamente em seu aplicativo em execução, para que você não precise compilar seu projeto Xamarin. Forms após cada alteração em XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: d94f18d00ebf6eeec5f33343b5c0f985ba2a6ea8
ms.sourcegitcommit: 9ab907e053c57fc96419149f83187bc3e8983a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75655401"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>Hot recarregamento de XAML para Xamarin. Forms (visualização)

Os Hot recarregamento de XAML são conectados ao fluxo de trabalho existente para aumentar a produtividade e poupar tempo. Sem o Hot recarregamento de XAML, você precisa compilar e implantar seu aplicativo toda vez que desejar ver uma alteração XAML. Com a recarga a quente, quando você salva o arquivo XAML, as alterações são refletidas em seu aplicativo em execução. Além disso, o estado de navegação e os dados serão mantidos, permitindo que você itere rapidamente na interface do usuário sem perder seu lugar no aplicativo. Portanto, com o Hot recarregamento de XAML, você gastará menos tempo recriando e implantando seus aplicativos para validar as alterações na interface do usuário.

> [!NOTE]
> Se você estiver escrevendo um aplicativo do WPF ou UWP, confira [Hot recarregamento de XAML para UWP e WPF](/visualstudio/debugger/xaml-hot-reload).
>
> O Hot recarregamento de XAML para Xamarin. _Forms não funciona_ atualmente para projetos UWP Xamarin. Forms.

## <a name="system-requirements"></a>Requisitos do sistema

| IDE/estrutura | Versão necessária |
|------|------------------|
|Visual Studio 2019 | 16,4 ou superior
Visual Studio 2019 para Mac | 8,4 ou superior
Xamarin.Forms | 4,1 ou superior

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>Usar o Hot recarregamento de XAML para Xamarin. Forms

Nenhuma instalação ou configuração adicional é necessária para usar o Hot recarregamento de XAML. Ele é incorporado ao Visual Studio e pode ser habilitado nas configurações do IDE. Uma vez habilitado, você pode começar a usar o Hot recarregamento de XAML Depurando seu aplicativo em um emulador, simulador ou dispositivo físico. Atualmente, o recarregamento dinâmico de XAML funciona apenas durante a depuração no iOS ou Android.

No Windows, o Hot recarregamento de XAML pode ser habilitado marcando a caixa de seleção Habilitar o recarregamento do **xamarin** em **ferramentas** > **Opções** > **Xamarin** > **Hot recarregar**.

Em um Mac, o Hot recarregamento XAML pode ser habilitado marcando a caixa de seleção **habilitar o WebLoad do xamarin** no **Visual Studio** > **preferências** > **projetos** > **Xamarin Hot recarregar**.

## <a name="resilient-reloading"></a>Recarregamento resiliente

Se você fizer uma alteração que o Hot recarregamento de XAML não possa recarregar, ele mostrará uma mensagem de erro usando o IntelliSense. Essas alterações, conhecidas como edições rudes, incluem a digitação incorreta do XAML ou a ligação de um controle a um manipulador de eventos que não existe. Mesmo com uma edição rude, você pode continuar a recarregar sem reiniciar o aplicativo. faça outra alteração em outro lugar no arquivo XAML e clique em salvar. A edição rude não será recarregada, mas suas outras alterações continuarão sendo aplicadas.

## <a name="known-limitations"></a>Limitações conhecidas

- Você não pode adicionar, remover ou renomear arquivos ou pacotes NuGet durante uma sessão de recarga ativa de XAML. Se você adicionar ou remover um arquivo ou pacote NuGet, recompile e reimplante seu aplicativo para continuar usando o Hot recarregamento de XAML.
- Defina o vinculador como **não vincular** para obter a melhor experiência. A configuração do **SDK do link somente** funciona na maior parte do tempo, mas pode falhar em determinados casos.
- A depuração em um iPhone físico exige que o intérprete use o Hot recarregamento de XAML. Para fazer isso, abra as configurações do projeto, selecione a guia Build do iOS e certifique-se **de habilitar a configuração do intérprete mono** está habilitada. Talvez seja necessário alterar a opção de **plataforma** na parte superior da página de propriedades para **iPhone**.
- Todas as referências criadas por meio da atribuição de um controle a outro campo ou propriedade usando seu valor `x:Name` não serão recarregadas.
- Atualizar a hierarquia visual do aplicativo Shell no **AppShell. XAML** pode causar problemas ao manter o estado do seu aplicativo. Recompile o aplicativo para continuar recarregando.
- O Hot recarregamento de XAML não C# pode recarregar o código, incluindo manipuladores de eventos, controles personalizados, code-behind de página e classes adicionais.
- Não _funciona em_ outras plataformas Xamarin. Forms com suporte (como Mac os ou UWP).

## <a name="migrate-from-the-private-preview"></a>Migrar da versão prévia privada

Se você fazia parte da visualização privada, sua extensão de recarregamento de XAML é atualizada automaticamente quando o Visual Studio for atualizado. Se você optar por não atualizar o Visual Studio, poderá continuar usando a versão atual do Hot recarregamento de XAML, mas não receberá nenhuma atualização adicional por meio do feed de extensão de visualização particular.

## <a name="troubleshooting"></a>Solução de problemas

- Se o Hot recarregamento do XAML falhar ao inicializar:
  - Atualize sua versão do Xamarin. Forms.
  - Verifique se você está na versão mais recente do IDE.
  - Defina as configurações do vinculador do Android ou iOS para **não vincular** nas configurações de Build do projeto.
- Se nada acontecer ao salvar o arquivo XAML, certifique-se de que o Hot recarregamento esteja habilitado no IDE.
- Se você estiver Depurando em um iPhone físico e seu aplicativo ficar sem resposta, verifique se o intérprete está habilitado. Para ativá-lo, abra as configurações do projeto, selecione a guia Build do iOS e marque a configuração **habilitar o intérprete mono** .

Para relatar um bug, use a ferramenta de comentários na **ajuda** > **enviar comentários** > **reportar um** menu de problemas no Windows e a **ajuda** > **relatar um** menu de problemas em um Mac.
